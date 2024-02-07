<template>
<el-dialog
    v-model="visiable"
    title="添加链接"
    width="43%">
    <div class="addlink">
<!--      <div class="warning">选择链接后请勿手动更改，手动更改可能造成活动链接类转化数据的丢失，如需重新选择请选择弹窗内的链接</div>-->
      <el-form class="form" :model="form" ref="elFormRef" :rules="rules">
        <el-form-item label="标题" prop="title">
            <el-input placeholder="请填写标题" v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="跳转链接" class="line" prop="linkUrl">
            <el-input placeholder="请输入跳转链接" v-model="form.linkUrl"></el-input>
<!--            <el-button class="addlink-btn">选择链接</el-button>-->
        </el-form-item>
        <el-form-item label="描述" prop="desc">
            <el-input type="textarea" v-model="form.desc" :rows="6" placeholder="请输入描述文字" maxlength="150"></el-input>
        </el-form-item>
        <el-form-item label="图片" prop="img">
          <AddFileList :max="1" :file-type-list="[1]" :is-show-preview="false" inputName="addLink"
                       :max-size="2" :type-reg="['jpg','JPEG','png']"
                       :is-show-limit-text="false" :is-show-image-preview="true" v-model="form.img"  />
        </el-form-item>
        <el-form-item >
          <p class="mark">图片大小不能超过2M，支持JPG,JPEG,PNG格式</p>
        </el-form-item>
      </el-form>
    </div>
  <template #footer>
      <span class="dialog-footer">
        <el-button @click="visiable = false">取消</el-button>
        <el-button type="primary" @click="submit">确认</el-button>
      </span>
  </template>
</el-dialog>
</template>
<script setup>
import AddFileList from "/@/components/WelcomeContent/addFileList.vue";
import {deepClone} from "/@/utils/other";
const emit = defineEmits(['addList'])
const visiable = ref(false)
const elFormRef = ref()
const form = reactive({
  title:'',
  linkUrl:'',
  desc:'',
  img:[],
  mediaType:5,
  msgType:'link'
})
const rules = {
  title: [ { required: true, message: '请填写标题', trigger: ['blur'], } ],
  linkUrl: [ { required: true, message: '请输入跳转链接', trigger: ['blur'], },
    {
      type: 'string',
      asyncValidator: (rule, value) => {
        return new Promise((resolve, reject) => {
          const reg = /^(?:http(s)?:\/\/)?[\w.-]+(?:\.[\w\.-]+)+[\w\-\._~:/?#[\]@!\$&'\*\+,;=.]+$/g
          if (!reg.test(value)) {
            reject('链接格式不对');
          } else {
            resolve();
          }
        });
      },
      trigger:'blur'
      },],
  img: [ { required: true, message: '请上传图片', trigger: ['change'], } ],
}
const clearForm = ()=>{
  form.title = ''
  form.linkUrl = ''
  form.desc = ''
  form.img = []
}
watch(()=>visiable.value,()=>{
  if(!visiable.value){
    clearForm()
    elFormRef.value?.clearValidate()
  }
})
const submit = ()=> {
  elFormRef.value?.validate((valid) => {
    if (valid) {
      const formTemp = deepClone(form)
      const fileData = deepClone(formTemp.img[0])
      delete formTemp.img
      const data = Object.assign({},fileData,formTemp)
      emit('addList',deepClone([data]))
      clearForm()
      visiable.value = false
    } else {
      console.log('error submit!!');
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
defineExpose({
  openDialog
});
</script>
<style scoped lang="scss">
.addlink{
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
