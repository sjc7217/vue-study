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
      <el-aside :width = "isCollapse ? '64px':'200px'">
        <div class="toggle-button" @click="toggleCollapse">
            |||
        </div>
        <!-- 侧边栏菜单区域 -->
        <el-menu background-color="#333744" 
            text-color="#fff" 
            active-text-color="#409eff" 
            unique-opened 
            :collapse="isCollapse"
            :collapse-transition="false"
            router
            :default-active="activePath">
          <!-- 一级菜单 -->
          <el-submenu :index="item.id+''" v-for="item in menulist" :key="item.id">
            <!-- 一级菜单模版区域 -->
            <template slot="title">
              <!-- 图标 -->
              <i class="el-icon-location"></i>
              <!-- 文字 -->
              <span>{{ item.authName }}</span>
            </template>

            <!-- 二级菜单 -->
            <el-menu-item 
                :index="'/' + subItem.path" 
                v-for="subItem in item.children" 
                :key="subItem.id"
                @click="saveNaviState('/' + subItem.path)">
              <!-- 二级菜单模版区域 -->
              <template slot="title">
                <!-- 图标 -->
                <i class="el-icon-menu"></i>
                <!-- 文字 -->
                <span>{{ subItem.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 右侧区域 -->
      <el-main>
          <!-- 路由占位符 -->
          <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data: function(){
      return {
          menulist:[],
          isCollapse: false,
          activePath: ''
      }
  },
  created: function(){
      this.getMenuList()
      this.activePath = window.sessionStorage.getItem('activePath')
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
    },
    toggleCollapse: function(){
        this.isCollapse = !this.isCollapse
    },
    //保存路由状态
    saveNaviState: function(activePath){
        window.sessionStorage.setItem("activePath", activePath)
        this.activePath = activePath
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
  .el-menu{
      border-right: none;
  }
}

.el-main {
  background-color: #eaedf1;
}

.toggle-button{
    background-color: #4a5064;
    font-size: 10px;
    line-height: 24px;
    text-align: center;
    color: #fff; 
    letter-spacing: 0.2em;
    cursor: pointer;
}
</style>