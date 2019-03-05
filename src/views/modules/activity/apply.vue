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
              <el-input v-model="dataForm.user"></el-input>
            </el-form-item>
        </el-col>
      </el-row>
      <!--<el-form-item label="选择分类：" prop="belongTypename">-->
        <!--<el-popover-->
          <!--ref="menuListPopover"-->
          <!--placement="bottom-start"-->
          <!--trigger="click">-->
          <!--<el-tree-->
            <!--:data="menuList"-->
            <!--:props="menuListTreeProps"-->
            <!--node-key="categoryId"-->
            <!--@current-change="menuListTreeCurrentChangeHandle"-->
            <!--:default-expand-all="true"-->
            <!--:highlight-current="true"-->
            <!--:expand-on-click-node="false">-->
          <!--</el-tree>-->
        <!--</el-popover>-->
        <!--<el-input v-model="dataForm.belongTypename" v-popover:menuListPopover :readonly="true" placeholder="点击选择所属分类" class="menu-list__input">-->
        <!--</el-input>-->
      <!--</el-form-item>-->
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
          <el-date-picker type="datetime" placeholder="选择开始日期" v-model="dataForm.startTime" style="width: 100%;" format="yyyy-MM-dd:hh:mm:ss"></el-date-picker>
        </el-col>
        <el-col class="line" :span="2" style="padding-left: 25px">一一</el-col>
        <el-col :span="11">
          <el-date-picker type="datetime" placeholder="选择结束日期" v-model="dataForm.endTime" style="width: 100%;"></el-date-picker>
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
            <el-select v-model="dataForm.verifier1" placeholder="请选择初审人" style="width: 100%">
              <el-option  v-for="item in options2" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="老师复审：" prop="verifier2">
            <el-select v-model="dataForm.verifier2" placeholder="请选择复审人" style="width: 100%">
              <el-option v-for="item in options2" :key="item.value" :label="item.label" :value="item.value"></el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item>
          <el-button type="primary"  @click="save()">提交申请</el-button>
      </el-form-item>
    </el-form>
    <!-- 弹窗, 新增 / 修改 -->
  <!--  <ShowLocationInfo v-if="showLocationInfo" ref="showLocationInfo" @refreshDataList="getDataList"></ShowLocationInfo>
-->
  </div>
</template>

<script>
 import { treeDataTranslate } from '@/utils'
 export default {
   // firstStatus/secondStatus分别为初审状态和复审状态
   data () {
     return {
       dataForm: {
         activityId: '',
         activityName: '',
         user: '',
         principal: '',
         principalTel: '',
         activitySite: '',
         firstStatus: '',
         secondStatus: '',
         startTime: '',
         endTime: '',
         desc: '',
         budget: '',
         verifier1: '',
         verifierId1: '',
         verifier2: '',
         verifierId2: ''
       },
       locationList: [
         {
           locationId: '',
           locationName: '',
           province: '',
           city: '',
           citycode: '',
           district: '',
           adcode: '',
           street: '',
           streetNumber: '',
           lat: '',
           lng: '',
           status: '',
           gmtCreate: ''
         }
       ],
       dataListLoading: false,
       addOrUpdateVisible: false,
       showLocationInfo: false,
       menuList: [],
       menuListTreeProps: {
         label: 'typeName',
         children: 'children'
       },
       typeForm: {
         categoryId: '',
         typeName: '',
         typePid: '',
         typePname: '',
         typeId: ''
       },
       districtList: [],
       dataRules: {
         activityName: [
            { required: true, message: '活动名称不能为空', trigger: 'blur' }
         ],
         principal: [
            { required: true, message: '联系人不能为空', trigger: 'change' }
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
     this.gettypelist()
     this.getDistrictList()
   },
   methods: {
     gettypelist () {
       this.$http({
         url: this.$http.adornUrl('/category/list'),
         method: 'get',
         params: this.$http.adornParams()
       }).then(({data}) => {
         this.menuList = treeDataTranslate(data.page.list, 'typeId', 'typePid')
       })
     },
      // 菜单树选中
     menuListTreeCurrentChangeHandle (data, node) {
       this.typeForm.categoryId = data.typeId
       this.typeForm.typeName = data.typeName
       console.log('选中的id = ' + this.typeForm.categoryId)
       console.log('选择的名称 = ' + this.typeForm.typeName)
       this.dataForm.belongTypeid = data.typeId
       this.dataForm.belongTypename = data.typeName
     },

     getDistrictList () {
       this.dataListLoading = true
       this.$http({
         url: this.$http.adornUrl('/location/districtList'),
         method: 'get',
         params: this.$http.adornParams({
           citycode: '028'
         })
       }).then(({data}) => {
         this.districtList = data.page
       })
     },
     getlocationList (district) {
       this.dataListLoading = true
       this.$http({
         url: this.$http.adornUrl('/location/locationList'),
         method: 'get',
         params: this.$http.adornParams({
           district: district
         })
       }).then(({data}) => {
         this.locationList = data.page
       })
     },
     setlocaltionInfor () {
       let obj = {
         locationId: '',
         locationName: '',
         province: '',
         city: '',
         citycode: '',
         district: '',
         adcode: '',
         street: '',
         streetNumber: '',
         lat: '',
         lng: '',
         status: '',
         gmtCreate: ''
       }
       var index = this.locationList.findIndex((item) => {
        // 根据item中的id属性来判断这个item是否是上面id中
        // 对应的数据，如果是返回一个true ,否返回false,继续下面的一条数据的遍历，以此类推
         return (item.locationName == this.dataForm.locationName) // 如果返回true，那么findIndex方法会将这个item对应的id返回到外面接受
       })
       console.log('选中的位置id = ' + this.locationList[index].locationId)
       this.dataForm.localtionId = this.locationList[index].locationId
       console.log('选中的位置id = ' + this.dataForm.localtionId)
       console.log('选择的位置名称 = ' + this.dataForm.locationName)
     },
     getUUID () {
       return (((1 + Math.random()) * 1000000)).toString(16)
     },
     save () {
       this.$refs['dataForm'].validate((valid) => {
         if (valid) {
           this.dataListLoading = true
           this.$http({
             url: this.$http.adornUrl('/equipment/save'),
             method: 'post',
             data: this.$http.adornData({
               'equipmentId': this.dataForm.equipmentId,
               'equipmentName': this.dataForm.equipmentName,
               'belongTypeid': this.dataForm.belongTypeid,
               'belongTypename': this.dataForm.belongTypename,
               'equipmentPrice': this.dataForm.equipmentPrice,
               'equipmentNum': this.dataForm.equipmentNum,
               'localtionId': this.dataForm.localtionId,
               'locationName': this.dataForm.locationName,
               'community': this.dataForm.community,
               'unit': this.dataForm.unit,
               'floor': this.dataForm.floor,
               'corridor': this.dataForm.corridor,
               'roomNumber': this.dataForm.roomNumber,
               'administrator': this.dataForm.administrator,
               'phone': this.dataForm.phone}
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
     dataFormCancel () {
       this.dataForm = {}
     }

   }
 }
</script>
