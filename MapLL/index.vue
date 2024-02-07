<template>
  <el-select ref="selectRef" v-if="!showLngLat" @click="showMap"/>
  <div style="width: 100%;cursor: pointer" v-if="showLngLat" @click="showMap">{{showLngLat}}</div>
  <el-dialog
      v-model="mapVisible"
      title="地图"
      width="60%"
      destroy-on-close
  >
    <div style="position: relative">
      <div class="search" style="position:absolute; top: 10px;left: 10px;z-index: 32">
        <el-form class="l-f-r-w" style="height: 34px;" :inline="true" @keyup.enter="search" @submit.prevent>
          <el-form-item class="l-f-r-w l-f-nw">
            <el-row style="flex-wrap: nowrap;">
              <el-input v-model="key" ref="keyRef" />
              <el-button @click="search">搜索</el-button>
            </el-row>
          </el-form-item>
        </el-form>
        <ul class="searchList">
          <li :key="item" v-for="item in searchList" @click="selectItem(item)">
            <div class="title">{{item.name}}</div>
            <p v-if="typeof item.address == 'string'" class="address">{{item.address}}</p>
          </li>
        </ul>
      </div>
      <div ref="mapRef" style="height:50vh;overflow: hidden;width:100%"></div>
    </div>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="mapVisible = false">取消</el-button>
        <el-button type="primary" @click="submit">确定</el-button>
      </span>
    </template>
  </el-dialog>
</template>
<script setup lang="ts">
import AMapLoader from '@amap/amap-jsapi-loader';
const mapRef = ref(null)
const mapVisible = ref(false)
const key = ref('')
const map = ref()
const selectRef = ref()
const searchList = ref([])
const selected = ref([])
const props = defineProps(['dataName','modelValue'])
const emit = defineEmits(['update:modelValue'])
const keyRef = ref(null)
const {dataName,modelValue} = toRefs(props)

import poiIcon from '/@/assets/poi-marker-default.png'

let mapInstance: any= null
let Geolocation = null
let autoComplete: any = null
let AMAP: any = null
let MSearch:any
let geocoder: any = null
const lngPos = ref(null),latPos = ref(null)
const showLngLat = computed(()=>{
  const showArr = []
  if(modelValue?.value){
    dataName?.value.forEach(name=>{
      modelValue.value[name] && showArr.push(modelValue.value[name])
    })
  }

  return showArr.join(',')
})
const addMark = (lng,lat)=>{
  var marker = new AMAP.Marker({
    position: [lng,lat],
    icon: poiIcon,
    draggable:true,
    anchor: 'center',
  });

  marker.on('dragend',(e)=>{
    const {lnglat} = e
    marker.setPosition(lnglat);
    const {lat,lng} = lnglat
    lngPos.value = lng
    latPos.value = lat

    geocoder.getAddress(lnglat, function(status, result) {
      if (status === 'complete'&&result.regeocode) {
        const  {regeocode} = result
        const {formattedAddress} = regeocode
        marker.setLabel({content:formattedAddress,offset:'top'})
      }
    })
  })
  mapInstance.add(marker);
  mapInstance.setFitView()
  // mapInstance.setZoomAndCenter(16,[lng, lat],true)
}
const initAMap = ()=> {
  // window._AMapSecurityConfig = {
  //   securityJsCode:'f696410fb90ead38a21e50fb4af3a7aa',
  // }
  AMapLoader.load({
    key: "3306de7f91c3ef8a5c5ca3c057dcedf1", // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: ['AMap.PlaceSearch','AMap.AutoComplete','AMap.ElasticMarker','AMap.Geocoder','AMap.Geolocation'], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
  })
    .then((AMap) => {
      AMAP = AMap
      mapInstance = new AMap.Map(mapRef?.value, {
        // 设置地图容器id
        viewMode: "3D", // 是否为3D地图模式
        zoom: 14, // 初始化地图级别
      });
       autoComplete = new AMap.AutoComplete({});
       MSearch = new AMap.PlaceSearch(); //构造PlaceSearch类
       Geolocation = new AMap.Geolocation
       geocoder = new AMap.Geocoder()
      if(mapInstance && showLngLat.value){
        mapInstance.setCenter(showLngLat.value.split(','));
      }else{
          // Geolocation.getCurrentPosition((status,result)=>{
          //   if(status === 'complete'){
          //     const {position} = result
          //     const {lng,lat} = position
          //     addMark(lng,lat)
          //   }
          // })
        // nextTick(()=>{
        //
        // })

      }
    })
      .catch((e) => {
        console.log(e);
      });
}

// watch(()=>key?.value,()=>{
//   if(key?.value){
//     search()
//   }
// })
const selectItem = (poi)=>{
  selected.value = poi
  const {location} = poi
  const {lng,lat} = location
  lngPos.value = lng
  latPos.value = lat
  searchList.value = []
  addMark(lng,lat)
}
const search = ()=>{
  searchList.value = []
  MSearch.search(key?.value,(status, result)=>{
    if(status == 'complete'){
        searchList.value = result.poiList.pois
      }
  }); //关键字查询
  // autoComplete.search(key.value,function(status, result){
  //   console.log(status,result)
  //   if(status == 'complete'){
  //     searchList.value = result.tips
  //   }
  // })

}
onUnmounted(() => {
  mapInstance?.destroy();
});
const showMap = ()=>{
  mapVisible.value = true
  nextTick(()=>{
    initAMap()
    selectRef.value.blur()
  })
}
const submit = ()=>{
  mapVisible.value = false
  const data = [lngPos.value,latPos.value]
  dataName?.value?.forEach((name,index)=>{
    modelValue.value[name] = data[index]
  })
  emit('update:modelValue',modelValue.value)
}
</script>
<style scoped lang="scss">
.searchList{
  background: #fff;
  max-height: 300px;
  overflow:auto;
  box-shadow: 2px 2px 3px #ccc;
  border-radius:2px;
  li{
    padding:3px 8px 3px 15px;
    cursor: pointer;
    .title{
      font-weight: 600;
      font-size: 15px;
      margin-top: 4px;
    }
    .address{
      font-size:13px;
      margin-top: -9px;
    }
    &:hover{
      background:#4d65f61a
    }
  }
}
</style>