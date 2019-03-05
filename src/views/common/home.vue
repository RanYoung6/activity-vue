<template>
  <el-tabs v-model="activeName2" type="card" @tab-click="handleClick">
    <el-tab-pane label="组织动态" name="organization">组织动态</el-tab-pane>
    <el-tab-pane label="学生动态" name="student">学生动态</el-tab-pane>
    <el-tab-pane label="教师动态" name="teacher">教师动态</el-tab-pane>
    <el-tab-pane label="我的动态" name="personal">我的动态</el-tab-pane>
    <el-tab-pane label="管理员动态" name="administrator">管理员动态</el-tab-pane>
  </el-tabs>
</template>

<script>
  export default {
    data () {
      return {
        activeName2: 'organization'
      }
    },
    methods: {
      handleClick (tab, event) {
        console.log(tab, event)
      }
    }
  }
</script>

<style>
</style>

