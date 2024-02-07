<template>
  <block class="flie-list" :d="computedModelValue" v-loading="loading">
    <div class="disabled-box" v-if="isDisabled"></div>
     <input type="file" :id="'uploda-file-'+inputName" hidden="hidden" ref="fileInputRef" @change="addMeteria">
    <ul class="file-ul" v-if="!(fileTypeList.length == 1 && fileTypeList[0] == 1 && selectList.length == 1 && MAX == 1)">
      <template v-for="(item,index) in fileType" :key="item">
        <li v-if="fileTypeList.includes(item.type)">
          <block @click.stop="selectFile(index)">
            <div v-if="item.type == 1" class="icon"><el-icon><Picture /></el-icon></div>
            <div v-if="item.type == 3" class="icon"><el-icon><VideoPlay /></el-icon></div>
            <div v-if="item.type == 4" class="icon"><el-icon><Document /></el-icon></div>
            <div v-if="item.type == 5" class="icon"><el-icon><Link /></el-icon></div>
            <div v-if="item.type == 6" class="icon icon-miniprogram"><img :src="miniprogramSVG" alt=""></div>
            <div>{{ item.text }}</div>
          </block>
          <div v-if="item.type < 5 && selectList.length < max" class="activeBox">
            <el-button @click.stop="showMeteria(item)">素材库</el-button>
            <label :for="'uploda-file-'+inputName" class="el-button upload-label" @click.stop="changeType(item)">本地</label>
          </div>
        </li>

      </template>
    </ul>
    <div class="notic" v-if="max && isShowLimitText">最多添加{{max}}个附件</div>
    <template v-if="isShowPreview">
      <ul class="filelist-ul" v-if="selectList.length">
          <draggable v-model="selectList" :animation="100" item-key="index" class="list-group" forceFallback="true" @change="handleChange">
            <template #item="{ element, index }">
              <li>
                <div v-if="element.mediaType == 1" class="icon"><el-icon><Picture /></el-icon></div>
                <div v-if="element.mediaType == 3" class="icon"><el-icon><VideoPlay /></el-icon></div>
                <div v-if="element.mediaType == 4" class="icon"><el-icon><Document /></el-icon></div>
                <div v-if="element.mediaType == 5" class="icon"><el-icon><Link /></el-icon></div>
                <div v-if="element.mediaType == 6" class="icon icon-miniprogram"><img :src="miniprogramSVG" alt=""></div>
                <div class="text">{{element.mediaType < 5 ? element.mediaName:element.title}}</div>
                <div class="icon-box" >
                  <el-icon v-if="element.mediaType >= 5" @click.stop="edit(element,index)"><EditPen /></el-icon>
                  <el-icon @click.stop="deleteFile(index)"><Delete /></el-icon>
                  <div class="drag-icon"><img :src="dragSVG" alt=""></div>
                </div>
              </li>
            </template>

        </draggable>
      </ul>
    </template>
    <template v-if="isShowImagePreview">
      <ul v-if="selectList.length" class="imgPriew-ul">
        <li v-for="(item,index) in selectList" :key="item">
          <el-image :preview-src-list="[item.url.includes('http')?item.url:baseURL+item.url]" :src="item.url.includes('http')?item.url:baseURL+item.url" />
          <el-icon @click.stop="deleteFile(index)"><CircleClose /></el-icon>
        </li>
      </ul>

    </template>
    <lmeteria ref="lmeteriaRef" :type="meteriaType" @checkList="checkList" :max="max" :maxSize="maxSize * M" />
    <Addlink ref="AddlinkRef" @addList="checkList"  />
    <AddMiniPrograme ref="AddMiniProgrameRef" @addList="checkList" />
    <meterForm ref="meterFormRef"  />
  </block>
</template>
<script setup>
import draggable from 'vuedraggable'
import request from '/@/utils/request';
import {Session} from "/@/utils/storage";
import meterForm from '/@/views/core/mediaMangerVo/form.vue'
import lmeteria from '/@/views/core/mediaMangerVo/l-materia.vue'
import Addlink from "/@/components/WelcomeContent/addlink.vue";
import AddMiniPrograme from "/@/components/WelcomeContent/addMiniPrograme.vue";
import {useMessage} from "/@/hooks/message";
import {deepClone} from "/@/utils/other";
import {isArray} from "/@/utils/vo";
import miniprogramSVG from '/@/assets/icons/miniprogram.svg'
import dragSVG from '/@/assets/icons/drag.svg'
import Compressor from "compressorjs";
const meterFormRef = ref()
const props = defineProps(['max','fileTypeList','modelValue','isShowPreview',
    'isShowLimitText','isShowImagePreview','typeReg','maxSize','inputName','isDisabled'])
const {modelValue,max,fileTypeList,isShowPreview,isShowLimitText,isShowImagePreview,
    typeReg,maxSize,inputName,isDisabled} = toRefs(props)
const emit = defineEmits(['update:modelValue'])
const M = 1024*1024
const IMGREG = ['bmp','png','jpeg','jpg','gif','svg','webp']
const IMGMAX = 10*M
const AUDIOREG = ['mp3','wma','wav','amr']
const AUDIOMAX = 2*M
const VIDEOREG = ['mp4']
const VIDEOMAX = 10*M
const DOCREG = ['doc','docx','ppt','pptx','csv','xls','xlsx','pdf']
const DOCMAX = 10*M
const fileType=ref([
  { type:1,
    text:'图片',
  },
  { type:3,
    text:'视频',
  },
  { type:4,
    text:'文件',
  },
  { type:5,
    text:'链接',
  },
  { type:6,
    text:'小程序',
  }
])
const lmeteriaRef = ref(null)
const AddlinkRef = ref(null)
const AddMiniProgrameRef = ref(null)
const fileInputRef = ref(null)
const meteriaType = ref(1)
const selectList = ref([])
const editIndex = ref(-1)
const loading = ref(false)


const MAX = ref(0)
const type = ref(1)
//获取后缀名
const getExt = (str)=>{
  let ext = str.match(/\..*/g) ? str.match(/\..*/g)[0] : null
  if(str) return ext.substring(1)
  return null
}
// 获取文件名
const getName = (str)=>{
  let ext = str.match(/\..*/g) ? str.replace(/\..*/g,''):null
  return ext
}
//清除输入框内容 不然第二次无法选择同个文件
const clearInput = ()=>{
  if(fileInputRef && isArray(fileInputRef.value)){
    fileInputRef.value.forEach(file=>file.value = null)
  }else{
    fileInputRef.value.value = null
  }
}

const handleChange = () => {
  emit('update:modelValue',deepClone(selectList.value))
}
const toThumbFile = blob => new File([blob], 'thumb__img.png')
const loadVideo = (file)=> {
  return new Promise(function(resolve, reject) {
    const videoElem = document.createElement('video')
    const dataUrl = URL.createObjectURL(file)
    // 当前帧的数据是可用的
    videoElem.onloadeddata = function() {
      const canvasElem = document.createElement('canvas')
      const { videoWidth, videoHeight } =  videoElem
      canvasElem.width = videoWidth
      canvasElem.height = videoHeight
      canvasElem.getContext('2d').drawImage(videoElem, 0, 0, videoWidth, videoHeight)
      // 导出成blob文件
      canvasElem.toBlob(blob => {
        // 将blob文件转换成png文件
        resolve(blob)
      }, 'image/png')
    }
    videoElem.onerror = function() {
      reject('video 后台加载失败')
    }
    // 设置 auto 预加载数据， 否则会出现截图为黑色图片的情况
    videoElem.setAttribute('preload', 'auto')
    videoElem.src = dataUrl

  })
}
const uploadFile = async (file,name)=>{
  let data = new FormData();
  if(name == 'thumbFile'){
    data.append("file", file,'thumb__img.jpg');
  }else{
    data.append("file",file);
  }
  return request({
    url: '/admin/sys-file/upload',
    method: 'post',
    data: data,
    headers:{
      Authorization: 'Bearer ' + Session.get('token'),
      'TENANT-ID': Session.getTenant(),
    }
  })
}
const zipImg = (file)=>{
  return new Promise((resolve,reject)=>{
    new Compressor(file, {
      quality: (180*1024) / file.size,
      maxWidth:800,
      maxHeight:780,
      success(result) {
        resolve(result)
      },
      error(err) {
        reject(err)
        console.log(err);
      },
    });
  })
}
//文件处理
const fileOperate = async (file)=>{
  let ext = getExt(file.name)
  let name = getName(file.name)
  const regJSON = {
    1:IMGREG,
    2:AUDIOREG,
    3:VIDEOREG,
    4:DOCREG
  }
  const maxJSON = {
    1:IMGMAX,
    2:AUDIOMAX,
    3:VIDEOMAX,
    4:DOCMAX
  }
  const reg = typeReg && typeReg.value?typeReg.value:regJSON[type.value]
  const max = maxSize && maxSize.value && maxSize.value>0 ? maxSize.value * M : maxJSON[type.value]
  if(ext){
    if(!reg.includes(ext)){
      useMessage().error('上传的文件格式不对！')
      clearInput()
      return
    }
    if( file.size > max){
      useMessage().error('文件太大！')
      clearInput()
      return
    }
    loading.value = true

    let coverUrl = ''
    if(type.value == 1 ){
      //图片压缩
      loading.value = true
      const imgCover = await zipImg(file)
      const ret = await uploadFile(imgCover,'thumbFile')
      if(ret.code == 0){
        coverUrl = ret.data.url
      }
    }
    if(type.value == 3){
      const videoCover = await loadVideo(file)
      const imgCover = await zipImg(videoCover)
      const ret = await uploadFile(imgCover,'thumbFile')
      if(ret.code == 0){
        coverUrl = ret.data.url
      }
    }

    const res = await uploadFile(file,'file')
    loading.value = false
    if(res.code == 0){
      let item = {
        mediaName:name,
        url:res.data.url,
        ossUrl:res.data.url,
        mediaType:type.value,
        format:ext,
        coverUrl:coverUrl,
        mediaSize:file.size
      }
      const ret = await meterFormRef?.value?.externalUpload(item)
      if(ret.code == 0){
        switch (item.mediaType){
          case 1 :
            item.msgType = 'image'
            item.mediaName = item?.mediaName+(item?.format ? '.'+(item?.format || '') : '')
            break;
          case 3 :
            item.msgType = 'video'
            item.mediaName = item?.mediaName+(item?.format ? '.'+(item?.format || '') : '')
            break;
          case 4 :
            item.msgType = 'file'
            item.mediaName = item?.mediaName+(item?.format ? '.'+(item?.format || '') : '')
            break;
        }
        item.mediaId = ret.data
      }
      selectList.value.push(item)
      emit('update:modelValue',deepClone(selectList.value))
      clearInput()
    }
  }
}
// 用来初始化 关闭弹窗的时候把数据清除
  const computedModelValue = computed(()=>{
    if(Array.isArray(modelValue.value)){
      selectList.value = modelValue.value
    }
  })

  watch(()=>max.value,()=>{
    MAX.value = max.value || Number.MAX_VALUE
  },{immediate:true})
const showMeteria = (item)=>{
  if(selectList.value.length +1 >MAX.value){
    useMessage().error('最多添加'+MAX.value+'个附件')
    return
  }
  meteriaType.value = item.type
  lmeteriaRef.value.openDialog()
}
const checkList = (list)=>{
  if(editIndex.value !== -1){
    const temp = deepClone(selectList.value)
    temp[editIndex.value] = list[0]
    selectList.value = temp
    emit('update:modelValue',deepClone(selectList.value))
    editIndex.value = -1
    return
  }
  list.map(item=>{
    switch (item.mediaType){
      case 1 :
        item.msgType = 'image'
        item.mediaName = item?.mediaName+(item?.format ? '.'+(item?.format || '') : '')
        break;
      case 3 :
        item.msgType = 'video'
        item.mediaName = item?.mediaName+(item?.format ? '.'+(item?.format || '') : '')
        break;
      case 4 :
        item.msgType = 'file'
        item.mediaName = item?.mediaName+(item?.format ? '.'+(item?.format || '') : '')
        break;
    }

    let temp = {
      msgType:item.msgType,
      title:item?.title || '',
      desc:item?.desc || '',
      mediaId:item?.mediaId||'',
      mediaType:item.mediaType,
      linkUrl:item?.linkUrl ||'',
      page:item?.page||'',
      tpAuthorizeAppId:item?.tpAuthorizeAppId||'',
      coverUrl:item?.coverUrl||'',
      mediaName:item?.mediaName||'',
      url:item?.url || '',
      format:item?.format || ''
    }
    selectList.value.push(temp)
  })
  emit('update:modelValue',deepClone(selectList.value))
}
const changeType = (item)=>{
  type.value = item.type
}
const addMeteria = (e) =>{
  if(selectList.value.length +1 >MAX.value){
    useMessage().error('最多添加'+MAX.value+'个附件')
    return
  }
  for(let file of e.target.files){
    fileOperate(file)
  }

}
const deleteFile = (index)=>{
  selectList.value.splice(index,1)
  emit('update:modelValue',deepClone(selectList.value))
}
const edit = (item,index)=>{
  editIndex.value = index
  if(item.mediaType === 5){
    AddlinkRef.value?.openDialog(item)
  }else if(item.mediaType === 6){
    AddMiniProgrameRef.value?.openDialog(item)
  }
}
const selectFile = (index)=>{
  if(max?.value && selectList.value.length >=max.value) {
    useMessage().error('最多只能选择'+max.value+'个文件！')
    return
  }
  if(index === 3){
    AddlinkRef.value?.openDialog()
  }else if(index === 4){
    AddMiniProgrameRef.value?.openDialog()
  }
}
</script>
<style lang="scss" scoped>
.flie-list {
  position:relative;
  ul {
    margin-left: 0;
    list-style: none;
  }
  :deep(.el-input__wrapper)  {
    box-shadow: 0 0 0 1px var(--el-input-border-color,var(--el-border-color)) inset;
  }
  :deep(.el-textarea__inner)  {
    box-shadow: 0 0 0 1px var(--el-input-border-color,var(--el-border-color)) inset;
  }
}
.disabled-box{
  position: absolute;
  width: 100%;
  background: transparent;
  height: 100%;
  z-index: 10;
}
.file-ul{
  display: flex;
  border: 1px solid var(--el-border-color);
  border-radius: 3px;
  width: fit-content;
  li{
    width: 6.5em;
    text-align: center;
    border-right: 1px solid var(--el-border-color);
    position: relative;
    padding: 15px 5px;
    user-select: none;
    cursor: pointer;
    .icon{
      display: flex;
      justify-content: center;
      align-items: center;
      margin-bottom: 5px;
      margin-top: 8px;
      i{
        font-size: 20px;
      }
    }
    .icon-miniprogram{
      width: 18px;
      display: flex;
      justify-content: center;
      margin: 10px auto 4px;
    }
    .activeBox{
      position: absolute;
      top: 0;
      width: 100%;
      background: rgba(0,0,0,0.3);
      padding: 15px 5px;
      box-sizing: border-box;
      height: 100%;
      left: 0;
      display: none;
      .upload-label{
        width: 4.3em;
        height: 0;
        padding: 13px 0;
        background: #fff;
        margin: 0 auto;
      }
      >button{
        &:first-child{
          margin-bottom: 7px;
        }
        background: #fff;
        width: 4.3em;
        margin: auto;
        height: 28px;
      }
    }
    &:last-child{
      border-right: 0;
    }
    &:hover{
      >.activeBox{display: block}
    }
  }
}
.notic{
  color: var(--el-color-info);
  margin-bottom: 15px;
}
.filelist-ul{
  width: 100%;
  li{
    display: flex;
    align-items: center;
    background: var(--el-bg-color-page);
    padding: 3px 16px;
    width: 100%;
    margin-bottom: 10px;
    >div{
      display: flex;
      align-items: center;
      margin-right: 15px;
    }
    .text{
      width: 82%;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      display: block;
      color: var(--el-text-color-primary);
    }
    .icon-box{
      color: var(--el-color-primary-dark-2);
      cursor: pointer;
      .drag-icon{
        width: 14px;
        display: flex;
        align-items: center;
      }
      i{
        margin-right: 8px;
      }
    }
    .icon-miniprogram{
      width: 15px;
    }
  }
}
.imgPriew-ul{
  margin-top: 15px;
  li{
    width: fit-content;
    position: relative;
    .el-image{
      width: 70px;
    }
    .el-icon{
      font-size: 18px;
      position: absolute;
      top: -7px;
      right: -4px;
      cursor: pointer;
    }
  }
}
</style>