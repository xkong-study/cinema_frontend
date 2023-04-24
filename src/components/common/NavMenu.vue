<template>
  <div class="nav" style="padding: 0;">
    <div class="nav-logo" style="width: 20%;">
    </div>
    <div class="nav-left" style="width: 30%;padding-right: 30px">
      <el-menu
          theme="dark"
          mode="horizontal"
          router
          @select="handleSelect"
          background-color=transparent
          text-color=white
          active-text-color=white>
        <el-menu-item index="/movieset">movie set</el-menu-item>
        <el-menu-item index="/recommand">recommend to me</el-menu-item>
        <el-menu-item index="/movie">movie news</el-menu-item>
      </el-menu>
    </div>
    <div class="nav-search" style="width: 35%;padding-top: 15px;margin-left: 30px">
      <el-input
          @keyup.enter.native="searchClick"
          placeholder="Enter movie, director, actor name..."
          prefix-icon="el-icon-search"
          size="small"
          style="width: 70%;margin-right: 10px"
          v-model="keywords">
      </el-input>
      <el-button size="small" type="primary" icon="el-icon-search" @click="searchClick">search</el-button>
    </div>
    <div class="nav-sep" style="width: 1%;">
      <h3 style="color: white">|</h3>
    </div>
    <div v-if="!$store.state.user.username" class="nav-right" style="width: 15%">
      <el-menu
          theme="dark"
          class="nav-right"
          router
          mode="horizontal"
          background-color=transparent
          text-color=white
          active-text-color=white>
        <el-menu-item index="/register">register</el-menu-item>
        <el-menu-item index="/login">login</el-menu-item>
      </el-menu>
    </div>
    <div v-else class="user-profile" style="width: 15%">
        <el-dropdown
            @command="handleCommand"
        >
          <span class="el-dropdown-link">{{$store.state.user.username }}</span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="enterBg">enter backend</el-dropdown-item>
            <el-dropdown-item command="logout">
              <el-button type="primary" @click="logout">logout</el-button>
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      <el-avatar :size="40" icon="el-icon-user-solid"></el-avatar>
    </div>
  </div>
</template>

<script>
import '../../assets/my-ele-css/my-input.css'
export default {
  name: 'NavMenu',
  data() {
    return {
      keywords: '',
      activeIndex: '1',
      activeIndex2: '1'
    };
  },
  methods: {
    handleSelect(index) {
      if (this.$store.state.user.name) {
        if (index === '/recommand') {
          const loading = this.$loading({
            lock: true,
            text: 'Recommendations are being generated for you',
            spinner: 'el-icon-loading',
            background: 'rgba(0, 0, 0, 1)'
          });
          setTimeout(() => {
            loading.close();
          }, 1000);
        }
      }
    },
    searchClick() {
      this.$emit("showSearchResult")
    },
    logout() {
      this.$axios.get('/logout').then(resp=>{
        if (resp && resp.data.code === 200) {
          this.$store.commit('logout')
          this.$router.replace('/login')
        }
      })
    },
    handleCommand(command) {
      if (command === 'enterBg') {
        this.$router.replace('/admin')
      } else if (command !== 'logout') {
        this.$alert('function is not yet developed')
      }
    }
  },
}
</script>

<style scoped>

      /deep/ ul.el-menu--popup-bottom-start{
        background-color: rgba(2, 10, 14, 1) !important;
      }

      .nav {
      width:100%;
      height: 60px;
      display: flex;
      display: -webkit-flex;
      /* 从左端开始沿水平轴防止flex item */
      flex-direction: row;
      /* 强制 flex item不换行，沿着同一行堆叠 */
      flex-wrap: nowrap;
      /* flex item在主轴上的对齐方式，这里定义左对齐 */
      justify-content: flex-start;
      /* 定义交叉轴对其方式 */
      align-items: flex-start
    }

    .el-menu {
      border: none !important;
    }

    .el-menu-item:hover {
      background-color: #3a91ba !important;
    }

    .el-menu-item {
      border-bottom: none !important;
      background-color: transparent !important;
    }
    .logo img{
      background:no-repeat;
    }

    .nav-search {
      display: -webkit-flex;
      display: flex;
      -webkit-align-items: center;
      align-items: center;
      -webkit-justify-content: center;
      justify-content: center;
    }

    .el-dropdown-menu__item {
      text-align: center;
    }

    .user-profile {
      height: 60px;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .el-dropdown-link {
      color:white;
      font-size: 15px;
      padding-right: 10px;
      cursor: pointer;
    }

</style>

