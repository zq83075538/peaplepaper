
<template>
<el-container style="height: 100%; border: 1px solid #eee">
  
  <el-container>
    <el-aside width="200px"  style="background-color: rgb(238, 241, 246);overflow: auto">
      <div v-if="updateTiming!==-1" class="transition-box" style="padding: 20px;box-shadow: 0 0 3px #ccc;margin-bottom:10px;background:#fff">
        <p style="line-height:30px;font-size: 14px;margin-bottom: 10px;">定时更新</p>
        <el-switch
          v-model="updateTiming"
          active-color="#13ce66"
          active-text="开启"
          inactive-text="关闭"
          @change="timingChange"
        >
        </el-switch>
      </div>
      <Menu @menuClick="menuClick"></Menu>
    </el-aside>    
    <Main :api="api"></Main>
  </el-container>
</el-container>
</template>
<style>
.el-header {
  background-color: #b3c0d1;
  color: #333;
  line-height: 60px;
}

.el-aside {
  color: #333;
}
</style>

<script>
import { API } from "../config";
import Menu from "./left";
import Main from "./right";
export default {
  name: "appmain",
  data() {
    let { api } = this.$route.params;
    return {
      api,
      updateTiming: -1
    };
  },
  methods: {
    menuClick(api, menuNames) {
      this.api = api;
      // console.log(menuNames)
    },
    timingChange(e){
      $.post(API.host + 'task?type=' + (e ? 0 : 1), res => {
        if(res.code !== 200){
          this.updateTiming = !this.updateTiming;
          this.$message({ message: "服务器异常，请稍后再试", type: "error" })
        }
      })
      
    }
  },
  created(){
    $.get(API.host + 'taskinfo', res => {
      this.updateTiming = res.msg===1 ? false : true;
    })
  },
  components: {
    Menu,
    Main
  }
};
</script>