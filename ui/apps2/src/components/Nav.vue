<template>
  <div class="nav">
    <el-dialog
      class="dialog"
      :show-close="false"
      :visible.sync="showNav">
      <div class="navList">
        <div class="list login" v-if="!scatter.identity" @click="handleLogin">连接钱包</div>
        <div class="list">教程</div>
        <div class="list">质押</div>
        <div class="list">
          <span v-if="language !== 'en'" @click="handleChangeLang('en')">EN</span>
          <span v-else @click="handleChangeLang('zh-CN')">CN</span>
        </div>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { login } from '@/utils/public';

export default {
  data() {
    return {
      showNav: false,
    }
  },
  computed: {
    ...mapState({
      language: state => state.app.language,
      scatter: state => state.app.scatter,
    })
  },
  methods: {
    handleChangeLang(type) {
      this.showNav = false;
      this.$i18n.locale = type;
      this.$store.dispatch('setLanguage', type);
    },
    // 登录
    handleLogin() {
      login(this, () => {
        this.showNav = false;
      })
    },
  }
}
</script>

<style lang="scss" scoped>
.nav{
  position: relative;
  font-size: 24px;
  /deep/ .el-dialog{
    position: absolute;
    border-radius: 24px 0px 24px 24px;
    right: 40px;
    top: 120px;
    width: 240px;
    margin-top: 0 !important;
    .el-dialog__header{
      padding: 0;
    }
    .el-dialog__body{
      padding: 35px 5px 10px;
    }
  }
  .navList{
    color: #000;
    padding: 0 24px;
    .list{
      font-size: 28px;
      font-weight: 500;
      height: 70px;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .login{
      padding: 8px 20px;
      box-sizing: border-box;
      border: 1px solid #E0E0E0;
      border-radius: 8px;
    }
  }
}
</style>
