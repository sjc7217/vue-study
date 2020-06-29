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
        <el-table-column type="expand" >
            <template slot-scope="scope">
                <el-row :class="['vcenter','bdbottom', i === 0? 'bdtop':'']" v-for="(item, i) in scope.row.children" :key="item.id">
                    <!-- 渲染一级权限 -->
                    <el-col :span="5">
                        <el-tag closable @close="removeRoleById(scope.row, item.id)">{{ item.authName }}</el-tag>
                        <i class="el-icon-caret-right"></i>
                    </el-col>
                    <!-- 渲染二级权限 -->
                    <el-col :span="19" >
                        <el-row :class="['vcenter','bdbottom', i2 === 0? 'bdtop':'']" v-for="(item2, i2) in item.children" :key="i2">
                            <el-col :span="6">
                                <el-tag type="success" closable @close="removeRoleById(scope.row, item2.id)">{{ item2.authName }}</el-tag>
                                <i class="el-icon-caret-right"></i>
                            </el-col>
                            <!-- 渲染三级权限 -->
                            <el-col :span="18">
                                <el-tag type="warning" v-for="(item3, i3) in item2.children" :key="i3" closable @close="removeRoleById(scope.row, item3.id)">{{ item3.authName }}</el-tag>
                            </el-col>
                        </el-row>
                    </el-col>
                </el-row>
            </template>
        </el-table-column>
        <el-table-column type="index" ></el-table-column>
        <el-table-column label="角色名称" prop="roleName"></el-table-column>
        <el-table-column label="角色描述" prop="roleDesc" ></el-table-column>
        <el-table-column label="操作" width="380px">
            <template slot-scope="scope">
                <!-- 修改按钮 -->
                <el-button type="primary" icon="el-icon-edit" size="mini" >编辑</el-button>
                <!-- 删除按钮 -->
                <el-button type="danger" icon="el-icon-delete" size="mini" >删除</el-button>
                <!-- 分配角色按钮 -->
                <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
                <el-button type="warning" icon="el-icon-setting" size="mini" @click="showSetRightDialog(scope.row)">分配权限</el-button>
                </el-tooltip>
          </template>
        </el-table-column>
        </el-table>
    </el-card>

    <!-- 修改用户权限对话框 -->
    <el-dialog title="分配权限" :visible.sync="setRightDialogVisable" width="50%" >
      <!-- 树形控件 -->
      <el-tree :data="rightsList" :props="treeProps" show-checkbox node-key="id" default-expand-all :default-checked-keys="defKeys"></el-tree>
      <!-- 底部区域 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRightDialogVisable = false">取 消</el-button>
        <el-button type="primary" >确 定</el-button>
      </span>
    </el-dialog>
  </div>
  
</template>

<script>
export default {
    data(){
        return {
            roleLists: [],
            setRightDialogVisable: false,
            rightsList: [],
            //树形结构描述信息
            treeProps: {
                children: 'children',
                label: 'authName'
            },
            //默认勾选数组
            defKeys:[]
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
        },
        //根据id删除role
        async removeRoleById(role, rightId){
            const confirmResult = await this.$confirm('此操作将永久删除该权限, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).catch(err=>err)
            if(confirmResult !== 'confirm'){
                return this.$message.info("已取消")
            }
            const {data: res} = await this.$http.delete(`roles/${role.id}/rights/${rightId}`)
            if(res.meta.status !== 200){
                this.$message.error("删除权限失败！")
            }
            this.$message.success("删除权限成功！")
            //this.getRolesList()
            //仅更新部分数据
            role.children = res.data
        },
        //展示分配权限的对话框
        async showSetRightDialog(role){
            const {data: res} = await this.$http.get('rights/tree')
            this.rightsList = res.data
            //console.log(this.rightsList)
            this.getLeafKeys(role, this.defKeys)
            this.setRightDialogVisable = true
        },
        //获得所有三级节点id
        getLeafKeys(node, arr){
            if(!node.children){
                return arr.push(node.id)
            }
            node.children.forEach(item=>{
                this.getLeafKeys(item, arr)
            })
        }

    },
    created(){
        this.getRolesList()
    }
}
</script>

<style>

</style>