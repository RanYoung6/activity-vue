<template>
  <el-tabs v-model="activeName2"  @tab-click="handleClick">
    <el-tab-pane label="组织动态" name="organization" :data="organizationList">
      <article class="articleStyle" v-for="message in organizationList" >
        <h3 class="articleStyle_username">{{message.authorName}}<date class="articleStyle_time">{{message.activityCreateTime}}</date></h3>
        <div class="articleStyle_content">
          {{message.content}}
        </div>
        <div class="">
          {{message.activityImg}}
        </div>
      </article>
    </el-tab-pane>
    <el-tab-pane label="学生动态" name="student" :data="studentList">
      <article class="articleStyle" v-for="message in studentList" >
        <h3 class="articleStyle_username">{{message.authorName}}<date class="articleStyle_time">{{message.activityCreateTime}}</date></h3>
        <div class="articleStyle_content">
          {{message.content}}
        </div>
        <div class="">
          {{message.activityImg}}
        </div>
      </article>
    </el-tab-pane>
    <el-tab-pane label="教师动态" name="teacher" :data="teacherList">
      <article class="articleStyle" v-for="message in teacherList" >
        <h3 class="articleStyle_username">{{message.authorName}}<date class="articleStyle_time">{{message.activityCreateTime}}</date></h3>
        <div class="articleStyle_content">
          {{message.content}}
        </div>
        <div class="">
          {{message.activityImg}}
        </div>
      </article>
    </el-tab-pane>
    <el-tab-pane label="我的动态" name="personal" :data="personalList">
      <article class="articleStyle" v-for="message in personalList" >
        <h3 class="articleStyle_username">{{message.authorName}}<date class="articleStyle_time">{{message.activityCreateTime}}</date></h3>
        <div class="articleStyle_content">
          {{message.content}}
        </div>
        <div class="">
          {{message.activityImg}}
        </div>
      </article>
    </el-tab-pane>
    <el-tab-pane label="管理员动态" name="administrator" :data="adminList">
      <article class="articleStyle" v-for="message in adminList" >
        <h3 class="articleStyle_username">{{message.authorName}}<date class="articleStyle_time">{{message.activityCreateTime}}</date></h3>
        <div class="articleStyle_content">
          {{message.content}}
        </div>
        <div class="">
          {{message.activityImg}}
        </div>
      </article>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
  export default {
    data () {
      return {
        activeName2: 'organization',
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
        // 测试部分
        organizationList: [],
        studentList: [],
        teacherList: [],
        personalList: [],
        adminList: []
      }
    },
    activated () {
      this.getOrganizationList()
      this.getStudentList()
      this.getTeacherList()
      this.getAdminList()
      this.getPersonalList()
    },
    methods: {
      getOrganizationList () {
        // this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/activity/message/messagelist'),
          method: 'get',
          params: this.$http.adornParams({
            'roleId': 3
          })
        }).then(({data}) => {
          this.organizationList = data.page
        })
      },
      getStudentList () {
        // this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/activity/message/messagelist'),
          method: 'get',
          params: this.$http.adornParams({
            'roleId': 4
            // 'page': this.pageIndex,
            // 'limit': this.pageSize
          })
        }).then(({data}) => {
          this.studentList = data.page
        })
      },
      getTeacherList () {
        // this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/activity/message/messagelist'),
          method: 'get',
          params: this.$http.adornParams({
            'roleId': 5
          })
        }).then(({data}) => {
          this.teacherList = data.page
        })
      },
      getAdminList () {
        this.$http({
          url: this.$http.adornUrl('/activity/message/messagelist'),
          method: 'get',
          params: this.$http.adornParams({
            'roleId': 2
          })
        }).then(({data}) => {
          this.adminList = data.page
          console.log("adminList:"+data.page)
        })
      },
      getPersonalList () {
        this.$http({
          url: this.$http.adornUrl('/activity/message/personallist'),
          method: 'get',
          params: this.$http.adornParams({
            'userId':this.$store.state.user.id,
            // 'page': this.pageIndex,
            // 'limit': this.pageSize
          })
        }).then(({data}) => {
          this.personalList = data.page
        })
      },
      handleClick (tab, event) {
        console.log(tab, event)
      }
    }
  }
</script>

<style>
  .articleStyle{
    margin: 5px;
    padding: 10px 15px 5px;
  }
  .articleStyle_time{
    font-weight: normal;
    font-family: Calibri;
    font-size: 0.9em;
    color: #8c939d;
    margin-left: 20px;
  }
  .articleStyle_username,.articleStyle_content{
    font-weight: normal;
    font-family: Calibri;
    font-size: 1.3em;
  }
  .articleStyle_img{
    width: 100px;
    height: 100px;
  }
</style>

