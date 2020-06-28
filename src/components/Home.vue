<template>
  <el-container class="home-container">
    <el-header>
      <div>
        <img src="../assets/heima.png" />
        <span>电商后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <el-container>
      <!-- 侧边栏 -->
      <el-aside width="200px">
        <!-- 侧边栏菜单区域 -->
        <el-menu background-color="#333744" text-color="#fff" active-text-color="#ffd04b">
          <!-- 一级菜单 -->
          <el-submenu index="1">
            <!-- 一级菜单模版区域 -->
            <template slot="title">
              <!-- 图标 -->
              <i class="el-icon-location"></i>
              <!-- 文字 -->
              <span>导航一</span>
            </template>

            <!-- 二级菜单 -->
            <el-menu-item index="1-4-1">
              <!-- 二级菜单模版区域 -->
              <template slot="title">
                <!-- 图标 -->
                <i class="el-icon-location"></i>
                <!-- 文字 -->
                <span>导航一</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 右侧区域 -->
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data: function(){
      return {
          menulist:[]
      }
  },
  created: function(){
      this.getMenuList()
  },
  methods: {
    logout: function() {
      window.sessionStorage.clear();
      this.$router.push("/login");
    },
    getMenuList: async function(){
        const { data:res } = await this.$http.get('menus')
        //console.log(data)
        if(res.meta.status !== 200){
            return this.$message.error(res.meta.msg)
        }else{
            this.menulist = res.data
            //console.log(this.menulist)
        }
    }
  }
};
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
}

.el-header {
  background-color: #373d41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 20px;
  > div {
    display: flex;
    align-items: center;
    span {
      margin-left: 15px;
    }
  }
}

.el-aside {
  background-color: #333744;
}

.el-main {
  background-color: #eaedf1;
}
</style>