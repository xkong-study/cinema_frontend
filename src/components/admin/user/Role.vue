<template>
  <div>
    <el-dialog
        title="Modify role information"
        :visible.sync="dialogFormVisible">
      <el-form v-model="selectedRole" style="text-align: left" ref="dataForm">
        <el-form-item label="character name" label-width="120px" prop="name">
          <el-input v-model="selectedRole.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="role description" label-width="120px" prop="nameZh">
          <el-input v-model="selectedRole.nameZh" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Function configuration" label-width="120px" prop="perms">
          <el-checkbox-group v-model="selectedPermsIds">
            <el-checkbox v-for="(perm,i) in perms" :key="i" :label="perm.id">{{perm.nameZh}}</el-checkbox>
          </el-checkbox-group>
        </el-form-item>
        <el-form-item label="menu configuration" label-width="120px" prop="menus">
          <el-tree
              :data="menus"
              :props="props"
              show-checkbox
              :default-checked-keys="selectedMenusIds"
              node-key="id"
              ref="tree">
          </el-tree>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">cancel</el-button>
        <el-button type="primary" @click="onSubmit(selectedRole)">ensure</el-button>
      </div>
    </el-dialog>
    <el-row style="margin: 18px 0px 0px 18px ">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/admin/dashboard' }">control center</el-breadcrumb-item>
        <el-breadcrumb-item>User Management</el-breadcrumb-item>
        <el-breadcrumb-item>role configuration</el-breadcrumb-item>
      </el-breadcrumb>
    </el-row>
    <create-role @onSubmit="listRoles()"></create-role>
    <el-card style="margin: 18px 2%;width: 95%">
      <el-table
          :data="roles"
          stripe
          ref="multipleTable"
          style="width: 100%"
          :max-height="tableHeight">
        <el-table-column
            type="selection"
            width="55">
        </el-table-column>
        <el-table-column
            prop="id"
            label="id"
            width="100">
        </el-table-column>
        <el-table-column
            prop="name"
            label="character name"
            fit>
        </el-table-column>
        <el-table-column
            prop="nameZh"
            label="role description"
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
                type="text"
                size="small"
                @click="editRole(scope.row)">
              edit
            </el-button>
            <el-button
                type="text"
                size="small"
                @click="removeRole(scope.row)"
            >
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
import CreateRole from "@/components/admin/user/CreateRole";
export default {
  name: 'UserRole',
  components: {CreateRole},
  data () {
    return {
      dialogFormVisible: false,
      roles: [],
      perms: [],
      menus: [],
      selectedRole: [],
      selectedPermsIds: [],
      selectedMenusIds: [],
      props: {
        id: 'id',
        label: 'nameZh',
        children: 'children'
      }
    }
  },
  mounted () {
    this.listRoles()
    this.listPerms()
    this.listMenus()
  },
  computed: {
    tableHeight () {
      return window.innerHeight - 320
    }
  },
  methods: {
    listRoles () {
      const _this = this;
      this.$axios.get('/admin/role').then(resp => {
        if (resp && resp) {
          _this.roles = resp.data
        }
      })
    },
    listPerms () {
      const _this = this;
      this.$axios.get('/admin/role/perms').then(resp => {
        if (resp && resp.data) {
          _this.perms = resp.data
        }
      })
    },
    listMenus () {
      const _this = this;
      this.$axios.get('/admin/role/menu').then(resp => {
        if (resp && resp.data) {
          _this.menus = resp.data
        }
      })
    },
    commitStatusChange (value, role) {
      if (role.id !== 1) {
        this.$confirm('Whether to change the role status？', 'reminder', {
          confirmButtonText: 'ensure',
          cancelButtonText: 'cancel',
          type: 'warning'
        }).then(() => {
          this.$axios.put('/admin/role/status', {
            enabled: value,
            id: role.id
          }).then(resp => {
            if (resp && resp.data.code === 200) {
              if (value) {
                this.$message('user [' + role.nameZh + '] activated')
              } else {
                this.$message('user [' + role.nameZh + '] disabled')
              }
            }
          })
        }).catch(() => {
          role.enabled = !role.enabled
          this.$message({
            type: 'info',
            message: 'Cancelled'
          })
        })
      } else {
        role.enabled = true
        this.$alert('Unable to disable sysadmin！')
      }
    },
    editRole (role) {
      this.dialogFormVisible = true
      this.selectedRole = role
      /*
      let permIds = []
      for (let i = 0; i < role.perms.length; i++) {
        permIds.push(role.perms[i].id)
      }*/
      this.selectedPermsIds = role.permIds
      /*
      let menuIds = []
      for (let i = 0; i < role.menus.length; i++) {
        menuIds.push(role.menus[i].id)
        for (let j = 0; j < role.menus[i].children.length; j++) {
          menuIds.push(role.menus[i].children[j].id)
        }
      }*/
      this.selectedMenusIds = role.menuIds
      // 判断树是否已经加载，第一次打开对话框前树不存在，会报错。所以需要设置 default-checked
      if (this.$refs.tree) {
        this.$refs.tree.setCheckedKeys(role.menuIds)
      }
    },
    removeRole(role) {
      this.$confirm('This operation will permanently delete the role information, whether to continue?', 'reminder', {
        confirmButtonText: 'ensure',
        cancelButtonText: 'Cancel',
        type: 'warning'
      }).then(() => {
            this.$axios
                .post('admin/role/delete', {
                  id:role.id,
                  name:role.name,
                  name_zh: role.name_zh,
                  enabled: role.enabled
                }).then(
                    resp => {
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
    onSubmit (role) {
      let _this = this
      // 根据视图绑定的角色 id 向后端传送角色信息
      /*
      let perms = []
      let menus = []
      for (let i = 0; i < _this.selectedPermsIds.length; i++) {
        for (let j = 0; j < _this.perms.length; j++) {
          if (_this.selectedPermsIds[i] === _this.perms[j].id) {
            perms.push(_this.perms[j])
          }
        }
      }
      let selectedIds = _this.$refs.tree.getCheckedKeys().concat(this.$refs.tree.getHalfCheckedKeys())
      for (let i = 0;i < selectedIds.length;i++) {
        for (let j =0;j < _this.menus.length;j++) {
          if (selectedIds[i] === _this.menus[j].id) {
            menus.push(_this.menus[j])
          }
        }
      }*/
      let permIds = _this.selectedPermsIds
      let menuIds = _this.$refs.tree.getCheckedKeys().concat(this.$refs.tree.getHalfCheckedKeys())
      console.log('selected:' + permIds)
      console.log('menus:' + menuIds)
      this.$axios.put('/admin/role/update', {
        id: role.id,
        name: role.name,
        nameZh: role.nameZh,
        enabled: role.enabled,
        permIds: permIds,
        menuIds: menuIds
      }).then(resp => {
        if (resp && resp.data.code === 200) {
          this.$alert(resp.data.message)
          this.dialogFormVisible = false
          this.listRoles()
        }
      })
      /*
      this.$axios.put('/admin/role/menu?rid=' + role.id, {
        menusIds: this.$refs.tree.getCheckedKeys()
      }).then(resp => {
        if (resp && resp.data.code === 200) {
          console.log(resp.data.message)
        }
      })*/
    },
    cancelSelect() {
      this.$refs.multipleTable.clearSelection();
    },
    batchDelete() {
      this.$alert("暂不支持该功能.")
    }
  }
}
</script>

<style scoped>
.add-button {
  float: left;
  margin: 18px 0 18px 10px;
}

/deep/ .el-input__inner {
  color: black !important;
}
</style>
