<template xmlns:v-popover="">
  <div class="mod-menu">
    <el-form :model="dataForm"  ref="dataForm" :rules="dataRules" label-width="140px">
      <el-row>
        <el-col :span="12">
            <el-form-item label="活动名称：" prop="activityName">
              <el-input v-model="dataForm.activityName" placeholder="请输入活动名称" ></el-input>
            </el-form-item>
        </el-col>
        <el-col :span="12">
            <el-form-item label="申请组织：" prop="user">
              <el-input  v-model="dataForm.user" readonly></el-input>
            </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="7">
          <el-form-item label="活动负责人：" prop="principal">
            <el-input v-model="dataForm.principal" placeholder="输入负责人姓名"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="负责人联系方式：" prop="principalTel">
            <el-input v-model="dataForm.principalTel" placeholder="输入联系方式"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="9">
          <el-form-item label="活动地点：" prop="activitySite">
            <el-input v-model="dataForm.activitySite" placeholder="输入活动地点"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label="活动时间：" prop="activityTime">
        <el-col :span="11">
          <el-date-picker type="datetime" placeholder="选择开始日期" format="yyyy-MM-dd HH:mm:ss" value-format="yyyy-MM-dd HH:mm:ss" v-model="dataForm.startTime" style="width: 100%;"></el-date-picker>
        </el-col>
        <el-col class="line" :span="2" style="padding-left: 25px">一一</el-col>
        <el-col :span="11">
          <el-date-picker type="datetime" placeholder="选择结束日期" format="yyyy-MM-dd HH:mm:ss" value-format="yyyy-MM-dd HH:mm:ss" v-model="dataForm.endTime" style="width: 100%;"></el-date-picker>
        </el-col>
      </el-form-item>
     <el-form-item label="活动介绍：" prop="desc">
       <el-input type="textarea" :rows="5" v-model="dataForm.desc"></el-input>
      </el-form-item>
      <el-form-item label="活动预算：" prop="budget">
        <el-input type="textarea" :rows="5" v-model="dataForm.budget"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="12">
          <el-form-item label="学生干部初审：" prop="verifier1">
            <el-select v-model="dataForm.verifier1"
                       placeholder="请选择初审人"
                       style="width: 100%"
                       @change="selectverifier1()">
              <el-option  v-for="(item,index) in verifierList1"
                          :key="item.userId"
                          :value="item.username">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="老师复审：" prop="verifier2">
            <el-select v-model="dataForm.verifier2"
                       placeholder="请选择复审人"
                       style="width: 100%"
                       @change="selectverifier2()">
              <el-option
                v-for="(item,index) in verifierList2"
                :key="item.userId"
                :value="item.username">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item>
          <el-button type="primary"  @click="save()" >提交申请</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
 export default {
   data () {
     return {
       dataForm: {
         activityId: '',
         activityName: '',
         userId: '',
         user: this.$store.state.user.name,
         principal: '',
         principalTel: '',
         activitySite: '',
         status: '',
         startTime: '',
         endTime: '',
         desc: '',
         budget: '',
         verifier1: '',
         verifierId1: '',
         verifier2: '',
         verifierId2: ''
       },
       dataList: [
         {
           activityId: '',
           activityName: '',
           user: '',
           principal: '',
           principalTel: '',
           activitySite: '',
           status: '',
           startTime: '',
           endTime: '',
           desc: '',
           budget: '',
           verifier1: '',
           verifierId1: '',
           verifier2: '',
           verifierId2: ''
         }
       ],
       verifierList1: [],
       verifierList2: [],
       dataRules: {
         activityName: [
            { required: true, message: '活动名称不能为空', trigger: 'blur' }
         ],
         principal: [
            { required: true, message: '联系人不能为空', trigger: 'blur' }
         ],
         principalTel: [
            { required: true, message: '联系方式不能为空', trigger: 'blur' }
         ],
         activitySite: [
            { required: true, message: '活动地点不能为空', trigger: 'blur' }
         ],
         desc: [
            { required: true, message: '活动简介不能为空', trigger: 'blur' }
         ],
         budget: [
            { required: true, message: '活动预算不能为空', trigger: 'blur' }
         ],
         verifier1: [
            { required: true, message: '请选择初审人', trigger: 'blur' }
         ],
         verifier2: [
           { required: true, message: '请选择复审人', trigger: 'blur' }
         ]
       }
     }
   },
   components: {
   },
   activated () {
     this.getverifierList1()
     this.getverifierList2()
   },
   methods: {
     // 获取初审人的下拉列表
     getverifierList1 () {
       this.$http({
         url: this.$http.adornUrl('/sys/user/verifierList'),
         method: 'get',
         params: this.$http.adornParams({
           'roleId': 6
         })
       }).then(({data}) => {
           this.verifierList1=data.page
         console.log(data.page)
       })
     },
     // 获取复审人的下拉列表
     getverifierList2 () {
       this.$http({
         url: this.$http.adornUrl('/sys/user/verifierList'),
         method: 'get',
         params: this.$http.adornParams({
           'roleId': 7
         })
       }).then(({data}) => {
         this.verifierList2 = data.page
       })
     },
     // 获取选中初审人的id
     selectverifier1(){
       let verif1 = {};
       var index =this.verifierList1.findIndex((item) =>{
         return (item.username == this.dataForm.verifier1)
       })
       this.dataForm.verifierId1 = this.verifierList1[index].userId
     },
     // 获取选中初审人的id
     selectverifier2(){
       let verif2 = {};
       var index =this.verifierList2.findIndex((item) =>{
         return (item.username == this.dataForm.verifier2)
       })
       this.dataForm.verifierId2 = this.verifierList2[index].userId
     },
     // 表单提交保存
     save () {
       this.$refs['dataForm'].validate((valid) => {
         if (valid) {
           this.dataListLoading = true
           this.$http({
             url: this.$http.adornUrl('/activity/apply/save'),
             method: 'post',
             data: this.$http.adornData({
               'activityId': this.dataForm.activityId,
               'activityName': this.dataForm.activityName,
               'applyerId': this.dataForm.userId,
               'applyer': this.dataForm.user,
               'principal': this.dataForm.principal,
               'principalTel': this.dataForm.principalTel,
               'site': this.dataForm.activitySite,
               'starttime': this.dataForm.startTime,
               'endtime': this.dataForm.endTime,
               'description': this.dataForm.desc,
               'state':0,
               'budget': this.dataForm.budget,
               'verifierNameFirst': this.dataForm.verifier1,
               'firstId': this.dataForm.verifierId1,
               'verifierNameLast': this.dataForm.verifier2,
               'lastId': this.dataForm.verifierId2}
                , false)
           }).then(({data}) => {
             if (data && data.code === 0) {
               this.$message({
                 message: '操作成功',
                 type: 'success',
                 duration: 1500,
                 onClose: () => {
                   this.dataFormCancel()
                 }
               })
             } else {
               this.$message.error(data.msg)
             }
           })
         }
       })
     },
     // 表单重置
     dataFormCancel () {
       this.dataForm = {}
     }

   }
 }
</script>
