<template>
  <div class="comments">
    <block v-if="type!='detail'">
      <textarea ref="content" :disabled="disabled" @dragenter="handleEvent" @dragover="handleEvent" @drop="handleEvent" @dragleave="handleEvent"
                v-model="commentsText"
                name="" id="" placeholder="描述你的问题，可以直接粘贴图片，或者拖拽图片"></textarea>
      <div class="img-list" v-if="imgList.length || videoList.length">
        <div v-for="(item,index) in imgList" :key="item">
          <span class="del-icon" @click.stop="del(index)"><el-icon><CloseBold /></el-icon></span>
          <img @click.stop="_openPreview({images:imgPreview,index:index})" :src="item?.src" :key="item" :alt="item">
        </div>
        <div v-for="(item,index) in videoList" :key="item" @click.stop="showVideo(item?.src)">
          <span class="del-icon" @click.stop="delVideo(index)"><el-icon><CloseBold /></el-icon></span>
          <el-icon class="play"><VideoPlay /></el-icon>
          <div class="masked"></div>
          <video :src="item?.src"></video>
        </div>
      </div>

      <div>
        <label class="label" :class="[disabled?'disabled':'']" for="fileInput"><el-icon><Upload /></el-icon>添加附件</label>
        <input type="file" :disabled="disabled" id="fileInput" ref="fileInput" style="display: none" />
      </div>

    </block>
    <block v-if="type == 'detail'">
      <pre v-html="commentsText"></pre>
      <div class="img-list" v-if="imgList.length || videoList.length">
        <div v-for="(item,index) in imgList" :key="item">
          <img @click.stop="_openPreview({images:imgPreview,index:index})" :src="item?.src"  :alt="item">
        </div>
        <div v-for="(item) in videoList" :key="item" @click.stop="showVideo(item?.src)">
          <el-icon class="play"><VideoPlay /></el-icon>
          <div class="masked"></div>
          <video :src="item?.src"></video>
        </div>
      </div>
    </block>
  </div>
</template>
<script setup lang="ts">
  import {preview} from 'vue3-image-preview';
  import {vo} from '/@/stores/vo'
  import { v4 as uuidv4 } from 'uuid';
  import {uploadFile} from "/@/api/vo/upload";
  import {loadVideo} from "/@/utils/vo";
  const content = ref(null)
  const fileInput = ref(null)
  const imgList = ref([])
  const commentsText = ref('')
  const props = defineProps(['modelValue','disabled','type','parentKey','dataName','parentIndex'])
  const {modelValue,disabled,type,parentKey,dataName,parentIndex} = toRefs(props)
  const emit = defineEmits(['update:modelValue'])
  const imgPreview = computed(()=>{
    return imgList.value.map(img=>img?.src) || []
  })
  const videoList:any = ref([])
  const ALIOSS = 'https://oss.localhome.cn/'
  const del = (index)=>{
    imgList.value.splice(index,1)
  }
  const delVideo = index => videoList.value.splice(index,1)
  const addImgList = ({name,file,src})=>{
    imgList.value.push({name,file,src})
  }
  const showVideo = (item)=>{
    const src = item.includes('http') ? item : import.meta.env.baseURL + item
    const page = window.open();
    const html="<body style='background:black'> <div style='width:90%;margin:auto;height: 100%; display: flex;'> <video controls width='100%' autoplay src='"+src+ "'></video> </div> </body>"
    page.document.write(html);
  }

  const handleFile = (file)=>{
    if (file.type.includes('image')) {
      var reader = new FileReader();
      reader.onload = function (e) {
        // 展示图片
        addImgList({
          name:'paste_'+file?.lastModified,
          file:file,
          src:e.target.result
        })
      };
      // 读取粘贴的图片数据
      reader.readAsDataURL(file);
    }
    //视频
    if(file.type.includes('video')){
      videoList.value.push({src:URL.createObjectURL(file),file:file})
    }
  }

  const handleEvent = (e)=>{
    if(disabled?.value) return
    e.stopPropagation()
    e.preventDefault()
    if(e.type == 'drop'){
      for(let file of e.dataTransfer.files){
        handleFile(file)
      }
    }
  }
  const uploadImg = (item)=>{
    let data = new FormData();
    data.append("file",item);
    return uploadFile(data)
  }
  watch(()=>commentsText?.value,()=>{
    if(type.value != 'detail'){
      emit('update:modelValue',commentsText?.value)
    }
  })

  const uploadFn = async (list,stitching) =>{
    let str = ''
    const arr = list.map(item=>uploadImg(item?.file))
    const ret = await Promise.all(arr)
    ret.forEach(item=>{
      if(item.code == 0){
        const src = ALIOSS+item.data.fileName
        str += stitching(src)
      }
    })
    return str
  }
  const onSubmit = async (data)=>{
    if(imgList?.value?.length || videoList?.value?.length){
      let str = ''
      const imgStr = await uploadFn(imgList?.value,(src)=>`<img src="${src}" alt="image" />`)
      const videoStr = await uploadFn(videoList?.value,(src)=>`<video src="${src}" />`)
      str += imgStr
      str += videoStr
      const newStr: any = commentsText.value + str
      const cn = dataName?.value
      data[cn] = newStr
    }
  }

  const uuid = ref(null)
  onBeforeMount(()=>{
    uuid.value = uuidv4()
    if(type.value != 'detail' ){
      vo().setUpdateFormDataMap(parentKey.value,{
        key:'comments',
        fn:onSubmit,
        parentKey:parentKey?.value,
        index:typeof parentIndex != 'undefined'?parentIndex?.value : -1,
        isVo:1, //这里是为了拿到父VO 的东西
        hasS:1,
        originKey:uuid.value,
        specificName:'add'
      },'add')
    }
  })
  onBeforeUnmount(() => {
    if(type.value != 'detail'){
      vo().setUpdateFormDataMap(parentKey.value,{
        key:'comments',
        fn:onSubmit,
        parentKey:parentKey?.value,
        index:typeof parentIndex != 'undefined'?parentIndex?.value : -1,
        isVo:1,
        hasS:0,
        originKey:uuid.value
      },'remove')
    }
  })
  const subUrl = (dataList,data)=>{
    dataList.forEach(p=>{
      const list = p.split(/\s/)
      let str = ''
      list.forEach((item)=>{
        if(item.indexOf('src')!=-1){
          str = item.substring(5,item.length-1)
          const i = str.lastIndexOf('/')
          const s = str.substring(i+1)
          str = s ? ALIOSS + s : str
          data.push({src:str})
        }
      })
    })
  }
  const initStop = watch(()=>modelValue?.value,()=>{
    if(modelValue?.value){
      if(type?.value == 'detail'){
        const imgMatch = modelValue.value.match(/<img [^<>]+>/g)
        const videoMatch = modelValue.value.match(/<source [^<>]+>|<video[^<>]+>/g)
        commentsText.value = modelValue.value.replace(/<img [^<>]+>|<video[^<>]+>|<source [^<>]+>|<\/video>/g,'')
        var content = commentsText.value;
        var reg = /<([a-z]+?)(?:\s+?[^>]*?)?>\s*?<\/\1>/ig;
        while (reg.test(content)) {
          content = content.replace(reg,"");
        }
        commentsText.value = content
        if(imgMatch?.length){
          subUrl(imgMatch,imgList.value)
        }
        if(videoMatch?.length){
          subUrl(videoMatch,videoList.value)
        }
      }
      nextTick(()=>{
        typeof initStop === 'function' && initStop()
      })
    }
  },{immediate:true})
  onMounted(()=>{
    nextTick(()=>{
      if(content?.value){
        content.value.addEventListener('paste', function (event) {
          if(disabled?.value) return
          event.stopPropagation()
          event.preventDefault()
          var items = (event.clipboardData || event.originalEvent.clipboardData).items;
          for (var i = 0; i < items.length; i++) {
            const bolb = items[i].getAsFile()
            handleFile(bolb)
          }
        });

      }
      if(fileInput?.value){
        fileInput?.value.addEventListener('change', function (event) {
          if(disabled?.value) return
          event.stopPropagation()
          event.preventDefault()
          var file = event.target.files[0];
          handleFile(file)
        });
      }
    })
  })
</script>
<style scoped lang="scss">
  .comments{
    width: 100%;
    textarea{
        border: 1px solid var(--el-border-color);
        border-radius: 5px;
        width: 100%;
        padding: 10px;
        box-sizing: border-box;
        height: 200px;
     }
    .img-list{
      display: flex;
      flex-wrap: wrap;
      margin-top: 1em;
      >div{
        display: flex;
        position: relative;
        //border: 1px solid #f3f3f3;
        margin: 0 5px 10px;
        align-items: center;
        //padding: 3px;
        width: 9em;
        img,video{
          width: 100%;
          cursor: pointer;
          height: 100px;
          object-fit: cover;
          object-position: top;
        }
        .play{
          position: absolute;
          top: 0;
          bottom: 0;
          left: 0;
          right: 0;
          margin: auto;
          color: #fff;
          font-size: 49px;
          z-index:3;
          cursor: pointer;
        }
        .masked{
          width: 9em;
          height: 100%;
          display: block;
          position: absolute;
          background: rgba(0,0,0,0.3);
          border-radius: 1px;
          z-index: 2;
          cursor: pointer;
        }
        .del-icon{
          position: absolute;
          top: -8px;
          right: -10px;
          background: #aaa;
          width: 20px;
          height: 20px;
          text-align: center;
          display: flex;
          justify-content: center;
          align-items: center;
          border-radius: 100%;
          cursor: pointer;
          color: #fff;
          z-index: 10;
        }
      }
    }
    .label{
      border: 1px solid var(--el-border-color);
      padding: 2px 10px;
      color: var(--el-color-primary);
      border-radius: 3px;
      display: flex;
      width: fit-content;
      align-items: center;
      i{
        margin-right: 3px;
      }
    }
    .disabled{
      color: #ccc;
      cursor: no-drop;
    }
  }
</style>