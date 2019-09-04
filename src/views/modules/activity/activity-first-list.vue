<template>
  <div class="mod-menu">
    <el-form :inline="true" :model="searchData" @keyup.enter.native="getDataList()" style="margin-left: 50px">
      <el-form-item>
        <el-input v-model="searchData.activityName" placeholder="活动名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()" type="primary">查询</el-button>
        <el-button v-if="isAuth('activity:apply:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="活动编号">
              <span>{{ props.row.activityId }}</span>
            </el-form-item>
            <el-form-item label="活动名称">
              <span>{{ props.row.activityName }}</span>
            </el-form-item>
            <el-form-item label="组织名称">
              <span>{{ props.row.applyer }}</span>
            </el-form-item>
            <el-form-item label="活动负责人">
              <span>{{ props.row.principal }}</span>
            </el-form-item>
            <el-form-item label="负责人联系方式">
              <span>{{ props.row.principalTel }}</span>
            </el-form-item>
            <el-form-item label="活动地点">
              <span>{{ props.row.site }}</span>
            </el-form-item>
            <el-form-item label="活动时间：">
              <span>{{ props.row.starttime }}——{{ props.row.endtime }}</span>
            </el-form-item>
            <el-form-item label="申请时间：">
              <span>{{ props.row.createTime }}</span>
            </el-form-item>
            <el-form-item label="活动介绍" style="width: 100%">
              <span>{{ props.row.description }}</span>
            </el-form-item>
            <el-form-item label="活动预算" style="width: 100%">
              <span>{{ props.row.budget }}</span>
            </el-form-item>
            <el-form-item label="初审人">
              <span>{{ props.row.verifierNameFirst }}</span>
            </el-form-item>
            <el-form-item label="复审人">
              <span>{{ props.row.verifierNameLast }}</span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column
        header-align="center"
        prop="applyer"
        align="center"
        min-width="80"
        label="组织名称">
      </el-table-column>
      <el-table-column
        prop="activityName"
        header-align="center"
        align="center"
        label="活动名称">
      </el-table-column>
      <el-table-column
        prop="verifierNameFirst"
        header-align="center"
        align="center"
        label="初审人">
      </el-table-column>
      <el-table-column
        prop="verifierNameLast"
        header-align="center"
        align="center"
        label="复审人">
      </el-table-column>
      <el-table-column
        prop="state"
        header-align="center"
        align="center"
        min-width="80"
        label="审批状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.state == 0" size="medium" type="info">初审中</el-tag>
          <el-tag v-if="scope.row.state == 1" size="medium" type="danger">初审驳回</el-tag>
          <el-tag v-if="scope.row.state == 2" size="medium">复审中</el-tag>
          <el-tag v-if="scope.row.state == 3" size="medium" type="danger">复审驳回</el-tag>
          <el-tag v-if="scope.row.state == 4" size="medium" type="success">复审通过</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        min-width="100"
        label="申请时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        min-width="100"
        label="操作">
        <template slot-scope="scope">
          <el-button round v-if="isAuth('activity:apply:changeState')" type="primary" size="mini" v-bind:disabled="scope.row.state==2||scope.row.state==3||scope.row.state==4" @click="changeState(scope.row.activityId,2)">通过</el-button>
          <el-button round v-if="isAuth('activity:apply:changeState')" type="danger" size="mini"  v-bind:disabled="scope.row.state==1||scope.row.state==3||scope.row.state==4" @click="changeState(scope.row.activityId,1)">驳回</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!--&lt;!&ndash; 弹窗, 新增 / 修改 &ndash;&gt;-->
    <!--<showActivityInfo v-if="showActivityInfo" ref="showActivityInfo" @refreshDataList="getDataList"></showActivityInfo>-->
  </div>
</template>
<style>
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 150px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
</style>
<script>
  import showActivityInfo from './activity-info'
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
          state: '',
          startTime: '',
          endTime: '',
          desc: '',
          budget: '',
          verifier1: '',
          verifierId1: '',
          verifier2: '',
          verifierId2: '',
          createTime: ''
        },
        dataList: [
          {
            activityId: '',
            activityName: '',
            user: '',
            principal: '',
            principalTel: '',
            activitySite: '',
            state: '',
            startTime: '',
            endTime: '',
            desc: '',
            budget: '',
            verifier1: '',
            verifierId1: '',
            verifier2: '',
            verifierId2: '',
            createTime: ''
          }
        ],
        searchData: {
          activityName: ''
        },
        dataListLoading: false,
        addOrUpdateVisible: false,
        dataListSelections: [],
        showActivityInfo: false,
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0
      }
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/activity/apply/firstlist'),
          method: 'get',
          params: this.$http.adornParams({
            'firstId':this.$store.state.user.id,
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          this.dataList = data.page
          this.dataListLoading = false
          this.totalPage = data.page.totalCount
        })
      },
      // showinfor (activityId) {
      //   this.dataListLoading = true
      //   this.$http({
      //     url: this.$http.adornUrl(`/activity/apply/info/${activityId}`),
      //     method: 'get',
      //     params: this.$http.adornParams()
      //   }).then(({data}) => {
      //     this.dataForm = data.page
      //     this.dataListLoading = false
      //     this.showActivityInfo = true
      //     this.$nextTick(() => {
      //       this.$refs.showActivityInfo.init(this.dataForm)
      //     })
      //   })
      // },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.showActivityInfo.init(id)
        })
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 通过及驳回审批
      changeState (activityId,state) {
        this.$confirm(`确定审核这条活动申请吗？`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() =>{
          this.$http({
            url: this.$http.adornUrl(`/activity/apply/changeState`),
            method: 'post',
            params: this.$http.adornParams({
              'activityId': activityId,
              'state': state
            })
          }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.getDataList()
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            }
          )
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.activityId
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/activity/apply/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      }
    }
  }
</script>
