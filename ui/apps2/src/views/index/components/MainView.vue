<template>
  <div class="mainView">
    <swap :marketLists="marketLists"
          @listenShowDrawer="handleShowDrawer"/>
    <!-- 弹窗组件 -->
    <el-dialog
      class="mkListDia pcList"
      :show-close="false"
      :visible.sync="showMarketList">
      <market-list :marketLists="marketLists" @listenClose="handleClose"/>
    </el-dialog>
  </div>
</template>

<script>
import MarketList from '@/components/MarketList';
import Swap from '@/views/swap/Index'
export default {
  name: 'mainView',
  components: {
    MarketList,
    Swap,
  },
  data() {
    return {
      sym0: '',
      showMarketList: false,
    }
  },
  props: {
    marketLists: {
      type: Array,
      default: function lists() {
        return []
      }
    }
  },
  methods: {
    handleClose() {
      this.showMarketList = false;
    },
    handleShowDrawer() {
      this.showMarketList = true;
    }
  },
}
</script>

<style lang="scss" scoped>
@import url('../../../assets/css/animal.scss');
.mainView{
  margin: 0 40px;
  .tabView{
    background: #FAFAFA;
    border-radius:30px;
    font-size: 24px;;
    .tabC{
      background:rgba(255,255,255,1);
      border-radius:30px;
      border:2px solid rgba(224,224,224,1);
      padding: 32px 20px;
    }
    .sym0Data{
      padding: 26px 40px 52px;
      border-radius:30px;
      border: 1px solid #F3F3F3;
      &.focus{
        border:1px solid rgba(7,215,155,1);
      }
      &.pdb10{
        padding-bottom: 28px;
      }
      .info{
        font-size: 24px;
        margin-bottom: 24px;
        .type{
          font-size: 28px;
        }
      }
      .iptDiv{
        .coinInfo{
          text-align: left;
          flex: 1;
          .coinImg{
            width: 60px;
            height: 60px;
            margin-right: 10px;
          }
          .coin{
            font-size: 28px;
            font-weight: 500;;
            line-height: 30px;
          }
          .contract{
            line-height: 30px;
          }
          .ableGet{
            color: #2F3F52;
          }
        }
        .inputDiv{
          flex: 2;;
          .elIpt{
            /deep/ .el-input__inner{
              color: $color-black;
              border: 0px;text-align: right;
              font-size: 52px;
              padding: 0 0 0 20px;
              height: 62px;
            }
          }
        }
      }
    }
    .exchange{
      height: 20px;
      position: relative;
      .border{
        width: 100px;
        height: 100px;
        border-radius: 50px;
        // border: 1px solid #07D79B;
        position: absolute;
        background: $color-bgcolor;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-image: url('../../../assets/img/dex/enter_solid_default.svg');
        background-repeat: no-repeat;
        background-size: cover;
        &.payFocus{
          background-image: url('../../../assets/img/dex/enter_solid_up.svg');
          background-repeat: no-repeat;
          background-size: cover;
        }
        &.getFocus{
          background-image: url('../../../assets/img/dex/enter_solid_up.svg');
          background-repeat: no-repeat;
          background-size: cover;
          transform: translate(-50%, -50%) rotate(180deg);
        }
        .iconImg{
          width: 72px;
        }
      }
    }
    .rate{
      color: $color-black;
      padding: 0 20px;
      margin-top: 28px;
      font-size: 28px;
      .iconImg{
        width: 36px;
        margin-left: 12px;
      }
    }
    .tabB{
      padding: 24px 40px;
      font-size: 26px;
      color: $color-black;
      .flexb{
        margin-top: 10px;
        &:first-child{
          margin-top: 0px;
        }
      }
      .ml10{
        width: 28px;
        margin-left: 10px;
      }
      .green{
        color: #02C698;
      }
    }
  }
  .btnDiv{
    margin: 40px;
    font-size: 32px;
    font-weight: 500;
    .btn{
      height: 88px;
      background:rgba(7,215,155,1);
      border-radius:30px;
      color: #fff;
      &:active{
        background:rgba(2,198,152,1);
      }
    }
  }
  .pool{
    font-size: 28px;
    text-align: left;
    padding: 20px 40px;
    background:rgba(255,255,255,1);
    border-radius:20px;
    border:2px solid rgba(224,224,224,1);
    .marketNow{
      margin-left: 20px;
      color: #02C698;
    }
    .poolsNum{
      margin-top: 12px;
      color: $color-black;
    }
  }
  .mkListDia{
    // animation: none;
    /deep/ .el-dialog{
      position: absolute;
      bottom: 0px;
      margin: 0px;
      width: 100%;
      border-radius:30px 30px 0px 0px;
      .el-dialog__body,
      .el-dialog__header{
        padding: 0;
      }
    }
    &.pcList{
      /deep/ .el-dialog{
        position: relative;
        margin: auto;
        width: 670px;
        border-radius:30px;
      }
    }
  }
}
</style>