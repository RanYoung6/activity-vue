<template xmlns:v-popover="">
  <el-dialog
    title="详情查看"
    width="900px"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" :rules="dataRules" label-width="120px">
      <el-row>
        <el-col :span="11">
          <el-form-item label="活动名称：" prop="activityName">
            <el-input v-model="dataForm.activityName"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="11">
          <el-form-item label="申请组织：" prop="applyer">
            <el-input v-model="dataForm.applyer"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="11">
          <el-form-item label="活动负责人：" prop="principal">
            <el-input v-model="dataForm.principal"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="11">
          <el-form-item label="联系方式：" prop="principalTel">
            <el-input v-model="dataForm.principalTel"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-form :rules="dataRules" ref="dataForm" :model="dataForm" label-width="120px">
      <el-row>
        <el-col :span="22">
          <el-form-item label="活动地点：" prop="site">
            <el-input v-model="dataForm.site"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-form label-width="120px" :rules="dataRules" ref="dataForm" :model="dataForm">
      <el-row>
        <el-col :span="11">
          <el-form-item label="开始时间：" prop="starttime">
            <el-input v-model="dataForm.starttime" readonly="readonly"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="11">
          <el-form-item label="结束时间：" prop="endtime">
            <el-input v-model="dataForm.endtime" readonly="readonly"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-form label-width="120px" :rules="dataRules" ref="dataForm" :model="dataForm">
      <el-row>
        <el-col :span="22">
          <el-form-item label="活动介绍：">
            <el-input v-model="dataForm.description" type="textarea" :autosize="{ minRows: 2, maxRows: 4}"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="22">
          <el-form-item label="预算：">
            <el-input v-model="dataForm.budget" type="textarea" :autosize="{ minRows: 2, maxRows: 4}"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="11">
          <el-form-item label="初审人：">
            <el-input v-model="dataForm.verifierNameFirst"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="11">
          <el-form-item label="复审人：">
            <el-input v-model="dataForm.verifierNameLast"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="11">
          <el-form-item label="审核状态：">
            <el-input v-model="dataForm.state"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="11">
          <el-form-item label="申请时间：">
            <el-input v-model="dataForm.createTime"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        roleList: [],
        dataForm: {
          activityId: '',
          activityName: '',
          applyerId: '',
          applyer: '',
          principal: '',
          principalTel: '',
          site: '',
          starttime: '',
          endtime: '',
          description: '',
          budget: '',
          verifierNameFirst: '',
          firstId: '',
          verifierNameLast: '',
          lastId: '',
          state: '',
          createTime: ''
        },
        dataListLoading: false
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.$http({
          url: this.$http.adornUrl('/activity/apply/select'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.menuList = treeDataTranslate(data.page.list, 'typeId' ,'typePid')
        })
      },
      // 菜单树选中
      menuListTreeCurrentChangeHandle (data, node) {
        this.typeForm.categoryId = data.typeId
        this.typeForm.typeName = data.typeName
        console.log("选中的id = "+this.typeForm.categoryId )
        console.log("选择的名称 = "+this.typeForm.typeName)
        this.dataForm.belongTypeid = data.typeId
        this.dataForm.belongTypename = data.typeName
      },

      getDistrictList(){
        this.dataListLoading = true;
        this.$http({
          url:this.$http.adornUrl('/location/districtList'),
          method: 'get',
          params:this.$http.adornParams({
            citycode:"028"
          })
        }).then( ({data}) =>{
          this.districtList = data.page
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/role/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'roleId': this.dataForm.id || undefined,
                'roleName': this.dataForm.roleName,
                'remark': this.dataForm.remark,
                'menuIdList': [].concat(this.$refs.menuListTree.getCheckedKeys(), [this.tempKey], this.$refs.menuListTree.getHalfCheckedKeys())
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>

<style lang="scss">
</style>
