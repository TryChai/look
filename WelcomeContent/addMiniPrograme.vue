<template>
  <el-dialog
      v-model="visiable"
      title="添加小程序"
      width="43%">
    <div class="addlink">
      <div class="error"><el-icon><WarningFilled /></el-icon>请先在企业微信后台绑定小程序，否则将无法发送小程序卡片</div>
<!--      <div class="warning">选择链接后请勿手动更改，手动更改可能造成活动链接类转化数据的丢失，如需重新选择请选择弹窗内的链接</div>-->
      <el-form class="form" :model="form" ref="elFormRef" :rules="rules">
        <el-form-item label="标题" prop="title">
          <el-input placeholder="请填写标题" v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="小程序ID" class="line" prop="tpAuthorizeAppId">
          <el-input placeholder="请输入小程序ID" v-model="form.tpAuthorizeAppId"></el-input>
          <el-button class="addlink-btn" @click="openMiniDialog">选择小程序ID</el-button>
        </el-form-item>
        <el-form-item label="跳转链接" class="line" prop="page">
          <el-input placeholder="请输入跳转链接" v-model="form.page"></el-input>
<!--          <el-button class="addlink-btn">选择页面</el-button>-->
        </el-form-item>

        <el-form-item label="图片" prop="img">
          <AddFileList :max="1" :file-type-list="[1]" :is-show-preview="false" inputName="addMiniProgram"
                       :max-size="2" :type-reg="['jpg','JPEG','png']"
                       :is-show-limit-text="false" :is-show-image-preview="true" v-model="form.img"  />
        </el-form-item>
        <el-form-item >
          <p class="mark">图片大小不能超过2M，支持JPG,JPEG,PNG格式，建议尺寸为 520x416px</p>
        </el-form-item>
      </el-form>
    </div>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="visiable = false">取消</el-button>
        <el-button type="primary" @click="submit">确认</el-button>
      </span>
    </template>
    <el-dialog
        v-model="miniVisiable"
        title="选择小程序ID"
        width="33%">
      <div class="addMini">
        <el-form class="miniForm" :mode="miniForm">
          <el-form-item label="小程序ID">
            <el-select placeholder="请选择小程序" v-model="miniForm.id">
              <el-option v-for="item in MiniProgramIdList" :value="item.maAppId" :label="item.maAppName" :key="item.maAppId"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
      </div>
      <template #footer>
      <span class="dialog-footer">
        <el-button @click="miniVisiable = false">取消</el-button>
        <el-button type="primary" @click="miniSubmit">确认</el-button>
      </span>
      </template>
    </el-dialog>
  </el-dialog>
</template>
<script setup>
import AddFileList from "/@/components/WelcomeContent/addFileList.vue";
import {deepClone} from "/@/utils/other";
import request from "/@/utils/request";
import {useApiData} from "/@/hooks/apiStore";
const emit = defineEmits(['addList'])
const visiable = ref(false)
const miniVisiable = ref(false)
const elFormRef = ref()
const form = reactive({
  title:'',
  page:'',
  desc:'',
  tpAuthorizeAppId:'',
  img:[],
  mediaType:6,
  msgType:'miniprogram'
})
const miniForm = reactive({
  id:''
})
const rules = {
  title: [ { required: true, message: '请填写标题', trigger: ['blur'], } ],
  page: [ { required: true, message: '请输入跳转链接', trigger: ['blur'], } ,
    {
      type: 'string',
      asyncValidator: (rule, value) => {
        return new Promise((resolve, reject) => {
          const reg = /^[^\u4e00-\u9fa5]+$/g
          if (!reg.test(value)) {
            reject('链接格式不对');
          } else {
            resolve();
          }
        });
      },
      trigger:'blur'
    }],
  tpAuthorizeAppId: [ { required: true, message: '请输入小程序ID', trigger: ['change','blur'], } ],
  img: [ { required: true, message: '请上传图片', trigger: ['change'], } ],
}
const clearForm = ()=>{
  form.title = ''
  form.page = ''
  form.desc = ''
  form.tpAuthorizeAppId = ''
  form.img = []
}
watch(()=>visiable.value,()=>{
  if(!visiable.value){
    clearForm()
    elFormRef.value?.clearValidate()
  }
})
const params = {
  url: `HUDSON/actionExecs/get`,
  method: 'post',
  data:{
    "param": {
      "campId": "64"
    },
    "action": "60"
  }
}
const MiniProgramIdList = ref(useApiData(params.url,()=>request(params)))
const submit = async ()=>{
  elFormRef.value?.validate((valid) => {
    if (valid) {
      const formTemp = deepClone(form)
      const fileData = deepClone(formTemp.img[0])
      delete formTemp.img
      const data = Object.assign({},fileData,formTemp)
      emit('addList',deepClone([data]))
      visiable.value = false
      clearForm()
    } else {
      return false;
    }
  })
}
const openDialog = (item) => {
  if(item){
    for(let i in item){
      form[i] = item[i]
    }
    const temp = deepClone(form)
    delete temp.img
    form.img = [temp]
  }
  visiable.value = true
}
const openMiniDialog = ()=>{
  miniForm.id = form.tpAuthorizeAppId
  miniVisiable.value = true
}
const miniSubmit = ()=>{
  form.tpAuthorizeAppId = miniForm.id
  miniVisiable.value = false
}
defineExpose({
  openDialog
});
</script>
<style scoped lang="scss">
.addlink{
  .error{
    display: flex;
    align-items: center;
    background: #FFFAF2;
    padding: 6px 10px;
    margin-bottom: 10px;
    font-size: 13px;
    border: 1px solid #FDDA9B;
    border-radius: 2px;
    i{
      color: #FBA203;
      margin-right: 5px;
      margin-top: -2px;
    }
  }
  .warning{
    background: #F2F7FF;
    padding: 11px 12px;
    border: 1px solid #C7DFFF;
    border-radius: 3px;
    margin-bottom: 20px;
    font-weight: 500;
    font-size: 13px;
  }
  .form{
    padding: 10px 30px;
    padding-right: 108px;
    :deep(.el-form-item){
      margin-bottom: 18px;
    }
    .line{
      :deep(.el-form-item__content){
        display: flex;
        flex-wrap: nowrap;
      }
      .addlink-btn{
        margin-left: 10px;
        width: 153px;
        overflow: hidden;
      }
    }
    .mark{
      font-size: 13px;
      color: var(--el-color-info);
      margin-left: 102px;
      margin-top: -2px;
      line-height: 1.5;
    }
    :deep(.el-form-item--default .el-form-item__label){
      width: 100px;
      text-align: right;
    }
  }

}
</style>
