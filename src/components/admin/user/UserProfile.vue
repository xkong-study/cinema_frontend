<template>
  <div>
    <el-dialog
        title="Assign user roles"
        :visible.sync="dialogFormVisible">
      <el-form v-model="selectedUser" style="text-align: left" ref="dataForm">
        <el-form-item label="username" label-width="120px" prop="username">
          <el-tag>{{selectedUser.username}}</el-tag>
        </el-form-item>
        <el-form-item label="Role Assignments" label-width="120px" prop="roles">
          <el-checkbox-group v-model="selectedRolesIds">
            <el-checkbox v-for="(role,i) in roles" :key="i" :label="role.id">{{role.nameZh}}</el-checkbox>
          </el-checkbox-group>
        </el-form-item>
        <el-form-item label="phone number" label-width="120px" prop="phone">
          <el-tag>{{selectedUser.phone}}</el-tag>
        </el-form-item>
        <el-form-item label="email" label-width="120px" prop="email">
          <el-tag>{{selectedUser.email}}</el-tag>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">cancel</el-button>
        <el-button type="primary" @click="onSubmit(selectedUser)">ensure</el-button>
      </div>
    </el-dialog>
    <el-row style="margin: 18px 0px 0px 18px ">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/admin/dashboard' }">control center</el-breadcrumb-item>
        <el-breadcrumb-item>User Management</el-breadcrumb-item>
        <el-breadcrumb-item>User Info</el-breadcrumb-item>
      </el-breadcrumb>
    </el-row>
    <el-card style="margin: 18px 2%;width: 95%">
      <el-table
          :data="users"
          stripe
          ref="multipleTable"
          :default-sort = "{prop: 'id', order: 'ascending'}"
          style="width: 100%"
          :max-height="tableHeight">
        <el-table-column
            type="selection"
            width="55">
        </el-table-column>
        <el-table-column
            prop="id"
            label="id"
            sortable
            width="100">
        </el-table-column>
        <el-table-column
            prop="username"
            label="username"
            fit>
        </el-table-column>
        <el-table-column
            label="description"
            fit>
          <template slot-scope="scope">
            <el-tag v-for="(role,i) in scope.row.roles" :key="i">{{role.nameZh}}</el-tag>
          </template>
        </el-table-column>
        <el-table-column
            prop="phone"
            label="phone number"
            fit>
        </el-table-column>
        <el-table-column
            prop="email"
            label="email"
            show-overflow-tooltip
            fit>
        </el-table-column>
        <el-table-column
            label="state"
            width="100">
          <template slot-scope="scope">
            <el-switch
                v-model="scope.row.enabled"
                @change="(value) => commitStatusChange(value, scope.row)">
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column
            label="operate"
            width="120">
          <template slot-scope="scope">
            <el-button
                @click="editUser(scope.row)"
                type="text"
                size="small">
              edit
            </el-button>
            <el-button
                @click="deleteUser(scope.row)"
                type="text"
                size="small">
              remove
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="margin: 20px 0 20px 0;float: left">
        <el-button @click="cancelSelect">cancel selection</el-button>
        <el-button @click="batchDelete">batch deletion</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'UserProfile',
  data () {
    return {
      users: [],
      roles: [],
      dialogFormVisible: false,
      selectedUser: [],
      selectedRolesIds: []
    }
  },
  mounted () {
    this.listUsers()
    this.listRoles()
  },
  computed: {
    tableHeight () {
      return window.innerHeight - 320
    }
  },
  methods: {
    listUsers () {
      const _this = this
      this.$axios.get('/admin/user').then(resp => {
        if (resp && resp.data) {
          _this.users = resp.data
        }
      })
    },
    listRoles () {
      const _this = this
      this.$axios.get('/admin/role').then(resp => {
        if (resp && resp.data) {
          _this.roles = resp.data
        }
      })
    },
    commitStatusChange (value, user) {
      if (user.username !== 'admin') {
        this.$axios.put('/admin/user/status', {
          enabled: value,
          username: user.username
        }).then(resp => {
          if (resp && resp.data.code === 200) {
            if (value) {
              this.$message('user [' + user.username + '] activated')
            } else {
              this.$message('user [' + user.username + '] disabled')
            }
          }
        })
      } else {
        user.enabled = true
        this.$alert('Administrator account cannot be disabled')
      }
    },
    onSubmit (user) {
      let _this = this
      // 根据视图绑定的角色 id 向后端传送角色信息
      let roles = []
      for (let i = 0; i < _this.selectedRolesIds.length; i++) {
        for (let j = 0; j < _this.roles.length; j++) {
          if (_this.selectedRolesIds[i] === _this.roles[j].id) {
            roles.push(_this.roles[j])
          }
        }
      }
      this.$axios.put('/admin/user/update', {
        id: user.id,
        username: user.username,
        phone: user.phone,
        email: user.email,
        roles: roles
      }).then(resp => {
        if (resp && resp.data) {
          this.$alert('User information modified successfully')
          this.dialogFormVisible = false
          // 修改角色后重新请求用户信息，实现视图更新
          this.listUsers()
        } else {
          this.$alert(resp.data.message)
        }
      })
    },
    editUser (user) {
      this.dialogFormVisible = true
      this.selectedUser = user
      let roleIds = []
      for (let i = 0; i < user.roles.length; i++) {
        roleIds.push(user.roles[i].id)
      }
      this.selectedRolesIds = roleIds
    },
    deleteUser(user) {
      this.$confirm('This operation will permanently delete the user information, continue?', 'reminder', {
        confirmButtonText: 'ensure',
        cancelButtonText: 'cancel',
        type: 'warning'
      }).then(() => {
            this.$axios
                .post('admin/user/delete', {id:user.id}).then(resp => {
              if (resp && resp.status === 200) {
                this.listUsers();
              }
            })
          }
      ).catch(() => {
        this.$message({
          type: 'info',
          message: 'Undeleted'
        })
      })
    },
    cancelSelect() {
      this.$refs.multipleTable.clearSelection();
    },
    batchDelete() {
      this.$alert("This feature is currently not supported.")
    }
  }
}
</script>

<style scoped>
</style>
