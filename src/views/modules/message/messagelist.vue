<template>
  <div class="mod-menu">
    <!--<el-form :inline="true" :model="searchData" @keyup.enter.native="getDataList()">-->
      <!--<el-form-item>-->
        <!--<el-button @click="getDataList()" type="primary">导出本页</el-button>-->
      <!--</el-form-item>-->
      <!--<el-form-item style="padding-right: 0px">-->
        <!--<el-button @click="getDataList()" type="primary">导出所有</el-button>-->
      <!--</el-form-item>-->
    <!--</el-form>-->
    <el-table
      :data="dataList"
      style="width: 100%;">
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="动态编号：" style="width: 33.3%">
              <span>{{ props.row.messageId }}</span>
            </el-form-item>
            <el-form-item label="用户名：" style="width: 33.3%">
              <span>{{ props.row.authorName }}</span>
            </el-form-item>
            <el-form-item label="发布时间：" style="width: 33.3%">
              <span>{{ props.row.activityCreateTime }}</span>
            </el-form-item>
            <el-form-item label="动态内容：" style="width: 100%">
              <span>{{ props.row.content }}</span>
            </el-form-item>
            <el-form-item label="动态图片路径：" style="width: 100%">
              <span>{{ props.row.activityImg }}</span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        min-width="40%"
        label="用户名">
        <template slot-scope="scope">
          <el-button type="text" size="medium">{{scope.row.authorName}}</el-button>
        </template>
      </el-table-column>
      <el-table-column
        prop="content"
        header-align="center"
        align="center"
        min-width="120%"
        label="动态内容">
      </el-table-column>
      <el-table-column
        prop="activityCreateTime"
        header-align="center"
        align="center"
        min-width="70%"
        label="发布时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        label="操作">
        <template slot-scope="scope">
          <!--<el-button round v-if="isAuth('activity:message:info')" type="primary" size="mini" @click="showinfor(scope.row.messageId)">详情</el-button>-->
          <el-button round v-if="isAuth('activity:message:delete')" type="danger" size="mini" @click="deleteHandle(scope.row.messageId)">删除</el-button>
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
    <!-- 弹窗, 新增 / 修改 -->
    <ShowMessageInfo v-if="ShowMessageInfo" ref="ShowMessageInfo" @refreshDataList="getDataList"></ShowMessageInfo>
  </div>
</template>
<style>
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 120px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 100%;
  }
</style>
<script>
  import ShowMessageInfo from './message-detail'
  export default {
    data () {
      return {
        dataForm: {
          messageId:'',
          authorId:'',
          authorName:'',
          content:'',
          activityImg:'',
          activityCreateTime:''
        },
        dataList: [
          {
            messageId:'',
            authorId:'',
            authorName:'',
            content:'',
            activityImg:'',
            activityCreateTime:''
          }
        ],
        dataListLoading: false,
        addOrUpdateVisible: false,
        ShowMessageInfo: false,
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0
      }
    },
    components: {
      ShowMessageInfo
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/activity/message/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize
          })
        }).then(({data}) => {
          this.dataList = data.page.list
          this.dataListLoading = false
          this.totalPage = data.page.totalCount
        })
      },
      showinfor (messageId) {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl(`/activity/message/info/${messageId}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.dataForm = data.activityMessage
          this.dataListLoading = false
          this.ShowMessageInfo = true
          this.$nextTick(() => {
            this.$refs.ShowMessageInfo.init(this.dataForm)
          })
        })
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
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
        var ids = new Array()
        ids[0] = id
        this.$confirm(`确定要删除吗?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl(`/activity/message/delete`),
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
