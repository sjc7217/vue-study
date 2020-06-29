<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card class="box-card">
        <!-- 用户信息列表区 -->
        <el-table :data="roleLists" border stripe>
        <el-table-column type="expand" ></el-table-column>
        <el-table-column type="index" ></el-table-column>
        <el-table-column label="角色名称" prop="roleName"></el-table-column>
        <el-table-column label="角色描述" prop="roleDesc" ></el-table-column>
        <el-table-column label="操作" width="380px">
            <template >
                <!-- 修改按钮 -->
                <el-button type="primary" icon="el-icon-edit" size="mini" >编辑</el-button>
                <!-- 删除按钮 -->
                <el-button type="danger" icon="el-icon-delete" size="mini" >删除</el-button>
                <!-- 分配角色按钮 -->
                <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
                <el-button type="warning" icon="el-icon-setting" size="mini">分配权限</el-button>
                </el-tooltip>
          </template>
        </el-table-column>
        </el-table>
    </el-card>
  </div>
  
</template>

<script>
export default {
    data(){
        return {
            roleLists: []
        }
    },
    methods: {
        async getRolesList(){
            const {data: res} = await this.$http.get('roles')
            if(res.meta.status !== 200){
                return this.$message("获取角色列表失败")
            }
            this.roleLists = res.data
            //console.log(this.roleLists)
        }
    },
    created(){
        this.getRolesList()
    }
}
</script>

<style>

</style>