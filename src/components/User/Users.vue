<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card class="box-card">
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="dialogVisible = true">添加用户</el-button>
        </el-col>
      </el-row>

      <!-- 用户信息列表区 -->
      <el-table :data="userlist" border stripe>
        <el-table-column type="index" ></el-table-column>
        <el-table-column prop="username" label="姓名" ></el-table-column>
        <el-table-column prop="email" label="邮箱" ></el-table-column>
        <el-table-column prop="mobile" label="电话" ></el-table-column>
        <el-table-column prop="role_name" label="角色" ></el-table-column>
        <el-table-column label="状态" >
            <!-- 作用域插槽，父组件使用子组件的数据 -->
            <!-- 参考:https://juejin.im/post/5a69ece0f265da3e5a5777ed -->
            <template slot-scope="scope">
                <el-switch
                    v-model="scope.row.mg_state"
                    active-color="#13ce66"
                    inactive-color="#ff4949"
                    @change="userStateChanged(scope.row)"
                    >
                </el-switch>
            </template>
        </el-table-column>
        <el-table-column label="操作" width="180px">
          <template >
            <!-- 修改按钮 -->
            <el-button type="primary" icon="el-icon-edit" size="mini"></el-button>
            <!-- 删除按钮 -->
            <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
            <!-- 分配角色按钮 -->
            <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
              <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[1, 2, 5, 10]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
    </el-card>

    <!-- 添加用户对话框 -->
    <el-dialog title="添加用户" :visible.sync="dialogVisible" width="50%">
      <!-- 内容主题区域 -->
      <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px" >
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      queryInfo: {
        query: "",
        //当前页数
        pagenum: 1,
        //当前每页数据数
        pagesize: 2
      },
      userlist: [], 
      total: 0,
      //控制对话框是否可见
      dialogVisible: false,
      //添加用户数据
      addForm: {
        username: '',
        password: '',
        email:'',
        mobile:''
      },
      //添加用户时的数据验证规则
      addFormRules: {
        username:[
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password:[
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
        ],
        email:[
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
        ],
        mobile:[
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
        ]
        
      }
    };
  },
  created() {
    this.getUserList();
  },
  methods: {
    //更新页面数据
    async getUserList() {
      const {data: res} = await this.$http.get("users", { params: this.queryInfo });
      //console.log(res)
      if(res.meta.status !== 200) return this.$message.error("获取用户列表失败！")
      this.userlist = res.data.users
      //console.log(this.userlist)
      this.total = res.data.total
    },
    //监听page size 改变
    handleSizeChange: function(newSize){
      //console.log(newSize)
      this.queryInfo.pagesize = newSize
      this.getUserList()
    },
    //监听页码值改变事件
    handleCurrentChange: function(newPage){
      //console.log(newPage)
      this.queryInfo.pagenum = newPage
      this.getUserList()
    },
    //监听switch状态改变
    userStateChanged: async function(row){
      //console.log(row)
      const {data: res} = await this.$http.put(`users/${row.id}/state/${row.mg_state}`)
      if(res.meta.status !== 200){
        row.mg_state = !row.mg_state
        return this.$message.error("更新用户状态失败！")
      }else{
        this.$message.success("更新用户状态成功！")
      }
    }

  }
};
</script>

<style lang="less" scoped>
</style>