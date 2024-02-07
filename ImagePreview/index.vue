<template>
  <div class="preview-box" v-if="visiable">
    <svg aria-hidden="true" style="position: absolute; width: 0px; height: 0px; overflow: hidden;"><symbol id="icon-flip-h" viewBox="0 0 1024 1024"><path d="M469.333 42.667h85.334v938.666h-85.334V42.667z m-384 153.002L401.664 512 85.334 828.33V195.67z m853.334 632.662L622.336 512l316.33-316.33v632.66z m-85.334-426.667L742.997 512l110.336 110.336V401.664z"></path></symbol><symbol id="icon-flip-v" viewBox="0 0 1024 1024"><path d="M981.333 469.333v85.334H42.667v-85.334h938.666z m-153.002-384L512 401.664 195.67 85.334h632.66zM195.669 938.667L512 622.336l316.33 316.33H195.67z m426.667-85.334L512 742.997 401.664 853.333h220.672z"></path></symbol><symbol id="icon-large" viewBox="0 0 1024 1024"><path d="M970.837 919.85L765.141 714.198a382.421 382.421 0 0 0 88.192-244.864 384 384 0 0 0-384-384 384 384 0 0 0-384 384 384 384 0 0 0 384 384 382.421 382.421 0 0 0 244.907-88.192l205.653 205.654a36.053 36.053 0 0 0 50.987 0 36.267 36.267 0 0 0-0.043-50.944zM590.72 756.865c-38.4 16.256-79.19 24.448-121.387 24.448a311.296 311.296 0 0 1-220.586-91.392 311.296 311.296 0 0 1-91.435-220.587 311.296 311.296 0 0 1 91.435-220.586 311.296 311.296 0 0 1 220.586-91.392 311.296 311.296 0 0 1 220.587 91.392 311.296 311.296 0 0 1 91.435 220.586 311.296 311.296 0 0 1-91.392 220.587 310.187 310.187 0 0 1-99.243 66.901z"></path><path d="M652.672 431.83h-147.84V292.01a35.968 35.968 0 1 0-71.979 0v139.82H292.011a35.968 35.968 0 1 0 0 72.02h140.8v140.8a35.968 35.968 0 1 0 72.021 0v-140.8h147.84a35.968 35.968 0 1 0 0-72.02z"></path></symbol><symbol id="icon-small" viewBox="0 0 1024 1024"><path d="M970.837 919.85L765.141 714.198a382.421 382.421 0 0 0 88.192-244.864 384 384 0 0 0-384-384 384 384 0 0 0-384 384 384 384 0 0 0 384 384 382.421 382.421 0 0 0 244.907-88.192l205.653 205.654a36.053 36.053 0 0 0 50.987 0 36.267 36.267 0 0 0-0.043-50.944zM590.72 756.865c-38.4 16.256-79.19 24.448-121.387 24.448a311.296 311.296 0 0 1-220.586-91.392 311.296 311.296 0 0 1-91.435-220.587 311.296 311.296 0 0 1 91.435-220.586 311.296 311.296 0 0 1 220.586-91.392 311.296 311.296 0 0 1 220.587 91.392 311.296 311.296 0 0 1 91.435 220.586 311.296 311.296 0 0 1-91.392 220.587 310.187 310.187 0 0 1-99.243 66.901z"></path><path d="M652.672 431.83H292.011a35.968 35.968 0 1 0 0 72.02h360.661a35.968 35.968 0 1 0 0-72.02z"></path></symbol><symbol id="icon-close" viewBox="0 0 1024 1024"><path d="M155.003 868.997c-21.969-21.97-21.969-60.415 0-82.384l631.61-631.61c21.97-21.969 60.415-21.969 82.384 0s21.969 60.415 0 82.384l-631.61 631.61c-21.97 21.969-60.415 21.969-82.384 0z"></path><path d="M155.003 155.003c21.97-21.969 60.415-21.969 82.384 0l631.61 631.61c21.969 21.97 21.969 60.415 0 82.384s-60.415 21.969-82.384 0l-631.61-631.61c-21.969-21.97-21.969-60.415 0-82.384z"></path></symbol><symbol id="icon-turn-left" viewBox="0 0 1024 1024"><path d="M672 418H144c-17.7 0-32 14.3-32 32v414c0 17.7 14.3 32 32 32h528c17.7 0 32-14.3 32-32V450c0-17.7-14.3-32-32-32z m-44 402H188V494h440v326z m191.3-491.5c-78.8-100.7-196-153.6-314.6-154.2l-0.2-64c0-6.5-7.6-10.1-12.6-6.1l-128 101c-4 3.1-3.9 9.1 0 12.3L492 318.6c5.1 4 12.7 0.4 12.6-6.1v-63.9c12.9 0.1 25.9 0.9 38.8 2.5 42.1 5.2 82.1 18.2 119 38.7 38.1 21.2 71.2 49.7 98.4 84.3 27.1 34.7 46.7 73.7 58.1 115.8 11 40.7 14 82.7 8.9 124.8-0.7 5.4-1.4 10.8-2.4 16.1h74.9c14.8-103.6-11.3-213-81-302.3z"></path></symbol><symbol id="icon-turn-right" viewBox="0 0 1024 1024"><path d="M480.5 251.2c13-1.6 25.9-2.4 38.8-2.5v63.9c0 6.5 7.5 10.1 12.6 6.1L660 217.6c4-3.2 4-9.2 0-12.3l-128-101c-5.1-4-12.6-0.4-12.6 6.1l-0.2 64c-118.6 0.5-235.8 53.4-314.6 154.2-69.6 89.2-95.7 198.6-81.1 302.4h74.9c-0.9-5.3-1.7-10.7-2.4-16.1-5.1-42.1-2.1-84.1 8.9-124.8 11.4-42.2 31-81.1 58.1-115.8 27.2-34.7 60.3-63.2 98.4-84.3 37-20.6 76.9-33.6 119.1-38.8zM880 418H352c-17.7 0-32 14.3-32 32v414c0 17.7 14.3 32 32 32h528c17.7 0 32-14.3 32-32V450c0-17.7-14.3-32-32-32z m-44 402H396V494h440v326z"></path></symbol><symbol id="icon-arrow-right" viewBox="0 0 1024 1024"><path d="M269.952 147.936c0-14.88 11.904-28.272 23.312-39.68 11.904-11.904 37.2-37.2 63.488-11.904l377.952 377.952c8.928 8.928 19.84 22.816 19.84 37.696s-10.912 28.768-19.84 37.696L356.752 927.648c-10.416 10.416-21.824 13.392-34.224 8.928-9.424-3.472-18.352-11.408-28.768-21.824-14.384-14.384-21.824-28.768-21.824-41.168 0-7.936 2.976-15.872 9.424-21.824L621.12 512 281.36 171.744c-7.44-7.44-11.408-15.376-11.408-23.808z"></path></symbol><symbol id="icon-ratio" viewBox="0 0 1024 1024"><path d="M870.4 256.512V768H154.112V256.512H870.4m15.872-64H137.728c-26.624 0-47.616 21.504-47.616 47.616v544.256c0 26.624 21.504 47.616 47.616 47.616h748.544c26.624 0 47.616-21.504 47.616-47.616V240.128c0.512-26.624-20.992-47.616-47.616-47.616z"></path><path d="M338.432 355.328c2.56-1.024 6.144-2.048 9.728-2.048h51.2c5.12 0 7.68 2.56 7.68 7.68v302.08c0 5.12-2.56 7.68-7.68 7.68h-48.64c-5.12 0-7.68-2.56-7.68-7.68V412.672c0-2.56-1.024-3.072-3.072-2.56l-39.936 8.192c-5.632 1.024-8.704-1.536-8.704-6.656v-30.72c0-4.096 2.048-6.656 6.144-8.704l40.96-16.896zM558.08 508.416c-8.192 7.68-17.92 11.776-29.696 11.776s-21.504-4.096-29.696-11.776c-7.68-7.68-11.776-16.896-11.776-28.16 0-10.752 4.096-19.968 12.8-27.648s17.92-11.776 28.672-11.776 20.48 4.096 29.184 11.776 12.8 16.896 12.8 27.648c0 10.752-4.096 20.48-12.288 28.16z m0 148.48c-8.192 7.68-17.92 11.264-29.696 11.264s-21.504-3.584-29.696-11.264c-7.68-7.68-11.776-16.896-11.776-27.648s4.096-19.968 12.8-27.648 17.92-11.776 28.672-11.776 20.48 4.096 29.184 11.776 12.8 16.896 12.8 27.648c0 10.752-4.096 19.968-12.288 27.648z m106.496-301.568c2.56-1.024 6.144-2.048 9.728-2.048h51.2c5.12 0 7.68 2.56 7.68 7.68v302.08c0 5.12-2.56 7.68-7.68 7.68h-48.64c-5.12 0-7.68-2.56-7.68-7.68V412.672c0-2.56-1.024-3.072-3.072-2.56l-39.936 8.192c-5.632 1.024-8.704-1.536-8.704-6.656v-30.72c0-4.096 2.048-6.656 6.144-8.704l40.96-16.896z"></path></symbol><symbol id="icon-arrow-left" viewBox="0 0 1024 1024"><path d="M753.29 874.923c0 14.834-11.867 28.184-23.24 39.556-11.866 11.867-37.083 37.083-63.288 11.867L289.994 549.578c-8.9-8.9-19.778-22.745-19.778-37.578s10.878-28.678 19.778-37.578L666.762 97.654c10.383-10.383 21.755-13.35 34.116-8.9 9.395 3.462 18.295 11.373 28.678 21.756 14.339 14.339 21.756 28.678 21.756 41.04 0 7.91-2.967 15.821-9.395 21.755L403.222 512l339.19 339.19c6.922 7.416 10.878 15.328 10.878 23.733z"></path></symbol></svg>
    <div class="preview" ref="previewRef" >
      <block v-for="(item,index) in imgList" :key="item">
        <div v-show="currentIndex == index" class="img-preview" :style="{width:imgWidth,height:imgHeight}">
          <img :class="[!isTransition?'image-none':'image-view']"
               :style="{transform:`rotate(${rotate}deg) rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale(${scale})`}"
               :src="item" alt="image">
        </div>
      </block>
    </div>
    <div class="closeIcon" @click.stop="closePreview"><el-icon><CloseBold /></el-icon></div>
    <div class="leftIcon" v-if="imgList.length>1" @click.stop="doCurrentIndex('pre')"><el-icon><ArrowLeftBold /></el-icon></div>
    <div class="rightIcon" v-if="imgList.length>1"  @click.stop="doCurrentIndex('next')"><el-icon><ArrowRightBold /></el-icon></div>
<!--    底部按钮-->
    <div  class="bottomIcon">
      <div  class="option button" @click.stop="doScale('big')">
      <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-large"></use></svg>
    </div>
      <div  class="option button" @click.stop="doScale('small')">
      <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-small"></use></svg>
    </div>
      <div  class="option button" @click.stop="doTurn('left')">
      <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-turn-left"></use></svg>
    </div>
      <div  class="option button" @click.stop="normal()">
      <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-ratio"></use></svg>
    </div>
      <div  class="option button" @click.stop="doTurn('right')">
        <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-turn-right"></use></svg>
      </div>
      <div  class="option button" style="font-size: 12px;" @click.stop="doFlipV()">
        <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-flip-v"></use></svg>
      </div>
      <div  class="option button" style="font-size: 12px;" @click.stop="doFlipH()">
        <svg class="base-icon" aria-hidden="true"><use  xlink:href="#icon-flip-h"></use></svg>
      </div>
    </div>
<!--    底部图片-->
    <div class="bottomImg">
        <div v-for="(item,index) in imgList" @click.stop="selectIndex(index)" :class="[currentIndex == index ? 'selected':'']" :key="item"><img :src="item" alt=""></div>
    </div>
  </div>
</template>
<script setup lang="ts">
  const visiable = ref(false)
  const imgList = ref([])
  const currentIndex = ref(null)
  const windowHeight = window.innerHeight
  const windowWidth = window.innerWidth
  const previewRef: any = ref(null)
  const reallyIndex = ref(null)
  const isTransition = ref(true)

  const imgWidth = ref('100%')
  const imgHeight = ref('100%')
  let imgAttr:any = {}
  const scale :any = ref(1)
  const rotate :any = ref(0)
  const rotateX :any = ref(0)
  const rotateY :any = ref(0)

  const getImgAttr = ()=>{
    const key = reallyIndex.value
    if(imgAttr[key]){
      const {width,height} = imgAttr[key]
      imgWidth.value = width+'px'
      imgHeight.value = height+'px'
      currentIndex.value = reallyIndex.value
      return
    }
    const item = imgList.value[key]
    const img = new Image()
    img.onload = function(){
      let {width,height} = img
      const proportion = width/height
      if(height > windowHeight){
        height = windowHeight - 30
        width = height*proportion
      }
      if(width > windowWidth){
        width = windowWidth - 30
        height = width/proportion
      }
      imgWidth.value = width+'px'
      imgHeight.value = height+'px'
      imgAttr[key] = {width,height}
      currentIndex.value = reallyIndex.value
    }
    img.src = item
  }
  const closePreview = ()=>{
    visiable.value = false
    imgAttr = {}
    imgList.value = []
    currentIndex.value = null
    reallyIndex.value = null

    normal()
  }
  const doScale = (type: string)=>{
    if(type == 'big'){
      if(scale.value < 3.2){
        scale.value += 0.4
      }
    }else if(type == 'small'){
      if(scale.value > 0.6){
        scale.value -= 0.4
      }
    }
  }
  const doTurn = (type : string)=>{
    if(type == 'left'){
      rotate.value -= 90
    }else{
      rotate.value += 90
    }
  }
  let clickX = 0
  const doFlipV = ()=>{
    if(!clickX){
      rotateX.value = 180
      clickX = 1
    }else{
      rotateX.value = 0
      clickX = 0
    }
  }
  let clickH = 0
  const doFlipH = ()=>{
    if(!clickH){
      rotateY.value = 180
      clickH = 1
    }else{
      rotateY.value = 0
      clickH = 0
    }
  }
  const normal = ()=>{
    scale.value = 1
    rotateY.value = 0
    rotate.value = 0
    rotateX.value = 0
    isTransition.value = false
    previewRef.value.style.top = ''
    previewRef.value.style.left = ''
    nextTick(()=>{
      setTimeout(()=>{
        isTransition.value = true
      },60)
    })
  }
  const doCurrentIndex = (type: string)=>{
    if(type == 'pre'){
      reallyIndex.value =  reallyIndex.value -1 < 0 ? imgList.value.length -1 :reallyIndex.value - 1
    }else{
      reallyIndex.value =  reallyIndex.value +1 > imgList.value.length -1 ? 0 :reallyIndex.value + 1
    }
    nextTick(()=>{
     normal()
    })
  }
const selectIndex = (index)=>{
  reallyIndex.value = index
  nextTick(()=>{
    normal()
  })
}
  watch(()=>reallyIndex.value,()=>{
    getImgAttr()
  })
  const openPreview = ({images,index})=>{
      imgList.value = images
      reallyIndex.value = index
      visiable.value = true
  }
  getCurrentInstance().appContext.app.config.globalProperties._openPreview = openPreview
  watch(()=>previewRef?.value,()=>{
    if(visiable.value && previewRef?.value){
      nextTick(()=>{
        const draggableElement = previewRef?.value;
        let offsetX, offsetY;

        draggableElement.addEventListener('mousedown', dragMouseDown);
        draggableElement.addEventListener('mouseup', stopDrag);

        function dragMouseDown(e) {
          e.preventDefault();
          offsetX = e.clientX;
          offsetY = e.clientY;
          document.addEventListener('mousemove', elementDrag);
        }

        function elementDrag(e) {
          e.preventDefault();
          const x = offsetX - e.clientX;
          const y = offsetY - e.clientY;
          offsetX = e.clientX;
          offsetY = e.clientY;
          draggableElement.style.top = (draggableElement.offsetTop - y) + 'px';
          draggableElement.style.left = (draggableElement.offsetLeft - x) + 'px';
        }

        function stopDrag() {
          document.removeEventListener('mousemove', elementDrag);
        }
      })

    }
  })
  onMounted(()=>{
    document.onmousewheel = function (e) {
      if(visiable.value){
        if (e.wheelDelta > 0) {
          doScale('big')
        } else {
          doScale('small')
        }

      }
    }
    //火狐浏览器
    document.addEventListener('DOMMouseScroll', (e: any) => {
      if(visiable.value){
        if (e.detail > 0) {
          doScale('big')
        } else {
          doScale('small')
        }
      }
    }, false);
  })
</script>
<style scoped lang="scss">
.absolute{
  position: absolute;
  background: #6e6e6eb3;
  padding:.6em;
  cursor: pointer;
  display: flex;
  align-items: center;
  color: #fff;
  img{
    width: 25px;
  }
}
.preview-box{
  -webkit-user-select: none;   -moz-user-select: none;   -ms-user-select: none;   user-select: none;
  position: fixed;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.6);
  z-index: 200024;
  top: 0;
  bottom: 0;
  left: 0;
  display: flex;
  justify-content: center;
  .preview {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    top:0;
    bottom: 0;
    .image-view{
      transition: .3s transform;
      &:hover{
        cursor: all-scroll;
      }
    }
    .image-none{
      transition: none;
    }
  }
  .closeIcon{
    @extend .absolute;
    top: 2rem;
    right: 2rem;
    i{
      font-size: 25px;
    }
  }
  .leftIcon{
    @extend .absolute;
    left: 2em;
    top: 45%;
    i{
      font-size: 25px;
    }
  }
  .rightIcon{
    @extend .absolute;
    right: 2em;
    top: 45%;
    i{
      font-size: 25px;
    }
  }
  .bottomIcon{
    @extend .absolute;
    display: flex;
    bottom: 5.5rem;
    >div{
      margin:0 .3em;
    }
    .button{
      //background-color: #6e6e6eb3;
      cursor: pointer;
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .base-icon {
      width: 1.7em;
      height: 1.7em;
      vertical-align: -.15em;
      fill: currentColor;
      overflow: hidden;
    }
  }
  .bottomImg{
    @extend .absolute;
    display: flex;
    bottom: .5rem;
    background: none;
    >div{
      width: 50px;
      height: 50px;
      display: inline-flex;
      justify-content: center;
      align-items: center;
      margin-left: 5px;
      margin-right: 5px;
      background-color: #6e6e6eb3;
      cursor: pointer;
      color: #fff;
      img{
        width: 80%;
        height: 80%;
        object-fit: cover;
      }
    }
    .selected{
      background-color: #ef544eb3;
    }
  }
}
</style>