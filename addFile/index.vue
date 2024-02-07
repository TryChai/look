<template>
  <block :d="getData">
    <AddFileList
        v-model="selectList" :max="9" :isDisabled="type == 'detail' || configData.isDisabled"
         :fileTypeList="configData.fileTypeList" :isShowPreview="configData.isShowPreview"
         :max-size="configData.maxSize"
         :inputName="inputName" :isShowLimitText="configData.isShowLimitText" />
  </block>
</template>
<script setup lang="ts">
import AddFileList from "/@/components/WelcomeContent/addFileList.vue";
import {deepClone} from "/@/utils/other";
const props = defineProps(['modelValue','type','config'])
const {modelValue,type,config} = toRefs(props)
const inputName = 'meteriaManage_'+(Math.random()*1000|0)
const configDefault = {
  fileTypeList:'1,3,4,5,6',
  max:9,
  isShowPreview:1,
  isShowLimitText:1,
  isShowImagePreview:1,
  maxSize:0,
  isDisabled:0,
  dataCollect:'all',
  dataType:'array',
  output:'string'
}
const selectList = ref([])
const configData :any = reactive({})
const getData = computed(()=>{
  let configMap = configDefault
  if(typeof config?.value === 'object'){
    const conf = deepClone(config.value)
    configMap = Object.assign(configDefault,conf)
  }
  const {fileTypeList,max,isShowPreview,isShowLimitText,isShowImagePreview,
    maxSize,isDisabled,dataCollect,dataType,output} = configMap
  configData.fileTypeList = fileTypeList.split(',').map(item=>Number(item))
  configData.max = max
  configData.isShowPreview = Boolean(isShowPreview)
  configData.isShowLimitText = Boolean(isShowLimitText)
  configData.isShowImagePreview = Boolean(isShowImagePreview)
  configData.maxSize = maxSize
  configData.dataCollect = dataCollect
  configData.dataType = dataType
  configData.output = output
  configData.isDisabled = type?.value == 'detail' ? 1 : isDisabled
})
watch(()=>modelValue?.value,()=>{
  if(Array.isArray(modelValue?.value)){
      selectList.value = deepClone(modelValue?.value)
  }else{
    selectList.value = []
  }
},{immediate:true,deep:true})
const submitData = ()=> {
  if(!configData.dataCollect || configData.dataCollect == 'all' ) {
    return deepClone(selectList.value)
  }
  const {dataCollect,dataType,output} = configData
  if(!dataCollect || !dataType || !output) return deepClone(selectList.value)
  const dataArray : any=  []
  const dataJSON: any = {}
  let temp: any = []
  const collect = dataCollect.split(',')
  if(collect.length > 1 && dataType == 'json'){
    collect.forEach(item=>dataJSON[item] =[])
  }
  selectList.value.forEach(select=>{
    if(collect.length == 1 && dataType == 'array'){
      dataArray.push(select[collect[0]])
    }
    if(collect.length > 1 && dataType == 'array'){
      dataArray.push(collect.map(item=>select[item]))
    }
    if(collect.length > 1 && dataType == 'array-json'){
      dataArray.push(collect.map(item=>({[`${item}`]:select[item]})))
    }
    if(collect.length == 1 && dataType == 'json'){
      temp.push(select[collect[0]])
    }
    if(collect.length > 1 && dataType == 'json'){
      collect.forEach(item=>dataJSON[item].push(select[item]))
    }
  })
  if(collect.length == 1 && dataType == 'json'){
    dataJSON[collect[0]] = temp
  }
  if(output == 'string'){
    return dataType == 'array' ? JSON.stringify(dataArray):JSON.stringify(dataJSON)
  }
  return dataType == 'array' ? dataArray:dataJSON
}
defineExpose({
  submitData
});
</script>
<style scoped lang="scss">

</style>