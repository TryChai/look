<template>
 <div class="mediaFormBox" :d="getData">
   <div class="mediaForm">
     <el-form class="form" :model="form">
       <el-form-item label="欢迎语">
         <el-input type="textarea" style="max-width: 500px;" :disabled="type == 'detail' || configData.isDisabled" show-word-limit maxlength="1000" v-model="form.textContent" :rows="8"></el-input>
       </el-form-item>
       <el-form-item label="添加附件">
         <div >
            <AddFileList v-model="selectList" :max="9" :isDisabled="type == 'detail' || configData.isDisabled"
              :fileTypeList="configData.fileTypeList" :isShowPreview="configData.isShowPreview"
              :max-size="configData.maxSize"
               inputName="meteriaManage" :isShowLimitText="configData.isShowLimitText" />
         </div>
       </el-form-item>
     </el-form>
   </div>
   <div class="miniMobi" v-if="configData.isShowMobile">
     <section>
        <img :src="mobileHeadJPG" alt=""/>
        <img :src="mobileTopJPG" alt="" />
       <div class="main">
         <ul v-if="selectList.length || form.textContent">
           <li class="time">{{dateTimeStr}}</li>
           <li v-if="form.textContent">
               <el-avatar class="avatar" shape="square" size="small" :src="userPNG" />
               <div class="bubble">
                 {{ form.textContent }}
               </div>
           </li>
           <li v-for="(item,index) in selectList" :key="item.mediaName+'_'+item.mediaType+'_'+index">
               <el-avatar class="avatar" shape="square" size="small" :src="userPNG" />
              <div v-if="item.mediaType == 1" class="img-box">
                <img v-if="item.url" :src="getImgSrc(item)" alt="" />
              </div>
             <div v-if="item.mediaType == 3" class="img-box">
               <template v-if="item.coverUrl">
                  <img :src="item.coverUrl.includes('http')?item.coverUrl:baseURL+item.coverUrl" alt="" />
                 <div class="cover_play"><img :src="coverPlay" alt=""></div>
               </template>
               <template v-else>
                 <div v-loading="loading" class="video-img-box" :d="getImgByVideo(item.url.includes('http')?item.url:baseURL+item.url,index)">
                    <img v-if="selectList[index]._src" :src="selectList[index]._src" alt="">
                   <div class="cover_play"><img :src="coverPlay" alt=""></div>
                 </div>
<!--                 <video :src="item.url.includes('http')?item.url:baseURL+item.url" />-->
               </template>
              </div>
               <div v-if="item.mediaType == 4" class="bubble" >
                    <div class="doc-box">
                      <div class="title">{{item.mediaName}}</div>
                      <div class="dec">{{item.mediaSize?item.mediaSize+'kb':'0kb'}}</div>
                    </div>
                    <div class="icon-box"><img :src="getIcon(item)" /></div>
               </div>
             <div v-if="item.mediaType == 5" class="bubble link-box">
                    <div class="doc-box">
                      <div class="title">{{item.title}}</div>
                    </div>
                    <div class="icon-box"><img :src="item.url.includes('http')?item.url:baseURL+item.url" alt="" /></div>
               </div>
             <div v-if="item.mediaType == 6" class="bubble mini-box">
               <div class="miniprogram-head">
                 <div class="miniprogram-head-img">
                    <img :src="userSVG" alt="">
                 </div>
                 <div class="mini-name">小程序名称</div>
               </div>
               <div class="title">{{item.title}}</div>
               <div class="icon-box"><img :src="item.url.includes('http')?item.url:baseURL+item.url" alt="" /></div>
               <el-divider />
               <div class="miniprogram-box">
                 <div class="miniprogram-icon"><img :src="miniprogramSVG" alt=""></div>
                 <div>小程序</div>
               </div>
               </div>
           </li>
         </ul>
       </div>
         <img :src="mobileFooterJPG" />
      </section>
   </div>

 </div>
</template>
<script setup lang="ts">
import AddFileList from "/@/components/WelcomeContent/addFileList.vue";
import {deepClone} from "/@/utils/other";

import mobileHeadJPG from '/@/assets/mobile-head.jpg'
import mobileTopJPG from '/@/assets/mobile-top.jpg'
import mobileFooterJPG from '/@/assets/mobile-footer.jpg'
import userPNG from '/@/assets/icons/mobile/user.png'
import userSVG from '/@/assets/icons/mobile/user.svg'
import miniprogramSVG from '/@/assets/icons/miniprogram.svg'
import coverPlay from '/@/assets/cover_play.png'
import xlsSVG from '/@/assets/icons/mobile/xls.svg'
import docSVG from '/@/assets/icons/mobile/doc.svg'
import csvSVG from '/@/assets/icons/mobile/csv.svg'
import pdfSVG from '/@/assets/icons/mobile/pdf.svg'
import pptSVG from '/@/assets/icons/mobile/ppt.svg'
import unKnowSVG from '/@/assets/icons/mobile/unknow.svg'

const baseURL = import.meta.env.VITE_API_URL;

const props = defineProps(['modelValue','type','config'])
const {modelValue,type,config} = toRefs(props)
const form = reactive({
  textContent:''
})
const loading = ref(false)
const selectList = ref([])
const date = new Date()
const configDefault = {
  fileTypeList:'1,3,4,5,6',
  max:9,
  isShowPreview:1,
  isShowLimitText:1,
  isShowImagePreview:1,
  maxSize:0,
  isDisabled:0,
  isShowMobile:1
}
const configData :any = reactive({})
const videoShowIndex = ref([])
const dateTimeStr = date.getMonth()+1+'月'+date.getDate()+'日 '+ date.getHours()+':'+date.getMinutes()

const getImgSrc = (item,name='coverUrl')=>{
  const getUrl = url=> url.includes('http') ? url : baseURL + url
  return item[name] ? getUrl(item[name]): getUrl(item.url)
}
const getData = computed(()=>{
  if(modelValue?.value && modelValue?.value?.messageContent){
    form.textContent = modelValue.value?.messageContent?.textContent
    selectList.value = modelValue.value?.messageContent?.attachments.map(item=>{
      switch (item.msgType){
        case 'image' :
          item.mediaType = 1
          break;
        case 'video' :
          item.mediaType = 3
          break;
        case 'file' :
          item.mediaType = 4
          break;
        case 'link' :
          item.mediaType = 5
          break;
        case 'miniprogram' :
          item.mediaType = 6
          break;
      }
      return item
    })
  }else{
    form.textContent = ''
    selectList.value = []
  }
  let configMap = configDefault
  if(typeof config?.value === 'object'){
    const conf = deepClone(config.value)
    configMap = Object.assign(configDefault,conf)
  }
  const {fileTypeList,max,isShowPreview,isShowLimitText,isShowImagePreview,maxSize,isDisabled,isShowMobile} = configMap
  configData.fileTypeList = fileTypeList.split(',').map(item=>Number(item))
  configData.max = max
  configData.isShowPreview = Boolean(isShowPreview)
  configData.isShowLimitText = Boolean(isShowLimitText)
  configData.isShowImagePreview = Boolean(isShowImagePreview)
  configData.maxSize = maxSize
  configData.isDisabled = isDisabled
  configData.isShowMobile = isShowMobile
  videoShowIndex.value = videoShowIndex.value.filter(item=>item < selectList.value.length)
})

const getImgByVideo = (url,index)=>{
  if(videoShowIndex.value.includes(index)){
    return
  }
  videoShowIndex.value.push(index)
  if(selectList.value[index]._src){
    loading.value = false
    return false
  }
  loading.value = true
  const video = document.createElement('video')
  video.style.width = '120px'
  video.style.height = '100px'
  const getImg = (e)=>{
    const canvasElem = document.createElement('canvas')
    const { videoWidth, videoHeight } =  video
    canvasElem.width = videoWidth
    canvasElem.height = videoHeight
    const cxt = canvasElem.getContext('2d')
    cxt.drawImage(video, 0, 0, videoWidth, videoHeight)
    const src = canvasElem.toDataURL('image/jpeg');
    if(selectList.value[index] && !selectList.value[index]?._src){
      selectList.value[index]._src = src
      loading.value = false
      e.preventDefault()
      e.stopPropagation()
      video.removeEventListener('loadeddata',getImg)
      video.remove()
      return false
    }
  }
  video.addEventListener('loadeddata', getImg)
  video.setAttribute('preload', 'auto')
  video.src = url
  return false
}
const getIcon = (item)=>{
  const iconJSON:any = {
    xls:xlsSVG,
    doc:docSVG,
    csv:csvSVG,
    pdf:pdfSVG,
    ppt:pptSVG,
  }
  const {mediaName} = item
  const ext = mediaName.match(/\.\w+/g)
  if(ext){
    const str = ext[0].substring(1,4)
    return iconJSON[str]
  }
  return unKnowSVG
}
const submitData = ()=>{
  const data = {
    textContent:form.textContent,
    attachments:deepClone(selectList.value)
  }
  return {
    messageContent:data
  }
}
defineExpose({
  submitData
});
</script>
<style lang="scss" scoped>
.mediaFormBox{
  display: flex;
  .mediaForm{
    width:75% ;
    .form{
      margin-right: 5%;
      :deep(.el-form-item__label){
        min-width: 90px;
      }
    }

  }
  .miniMobi{
    width: 288px;
    .main{
      height: 40vh;
      overflow: auto;
      background: rgba(237,237,237,1);
      padding: 15px;
      min-height: 400px;
      ul{
        .time{
          text-align: center;
          width: 100%;
          display: flex;
          justify-content: center;
          color: var(--el-color-info-light-3);
          font-size: 9px;
          transform: scale(.95);
        }
        li{
          display: flex;
          margin-bottom: 15px;
          clear:both;
          .avatar{margin-right: 8px;}
          .bubble {
            background: #fff;
            padding: 8px 9px;
            border-radius: 5px;
            font-size: 13px;
            position: relative;
            display: flex;
            .doc-box{
              width: 113px;
              .title{
                width: 100%;
                white-space: nowrap;
                overflow: hidden;
                text-overflow:ellipsis;
              }
              .dec{
                color: var(--el-border-color-darker);
              }
            }
            .icon-box{
              width: 32px;
              display: flex;
            }
            &::after{
              content: "";
              width: 8px;
              height: 10px;
              background: #fff;
              position: absolute;
              top: 8px;
              left: -3px;
              transform: rotate(45deg);

            }
          }
          .link-box{
            display: block;
            .icon-box{
              float: right;
              width: 40px;
              height: 40px;
              img{
                object-fit: cover;
              }
            }
          }
          .mini-box{
            display: block;
            font-size: 12px;
            .mini-name{
              white-space: nowrap;
              color: var(--el-color-info);
            }
            .icon-box{
              width: 140px;
              margin-top: 5px;
            }
            :deep(.el-divider--horizontal){
              margin: 7px 0;
            }
            .miniprogram-head{
              display: flex;
              margin-bottom: 5px;
              .miniprogram-head-img{
                width: 12px;
                display: flex;
                margin-right: 5px;
              }
            }
            .miniprogram-box{
              display: flex;
              color: var(--el-color-info);
              align-items: center;
              .miniprogram-icon{
                width: 15px;
                margin: 0px 8px 5px 3px;
              }
            }
          }
          .img-box{
            width: 70px;
            position: relative;
            .cover_play {
              position: absolute;
              top: 11px;
              width: 20px;
              left: 0;
              right: 0;
              bottom: 0;
              margin: auto;
            }
          }
          .video-img-box{
            width: 120px;
            position: relative;
            .cover_play{
              top:25px
            }
          }
        }
      }
    }
  }
}
</style>