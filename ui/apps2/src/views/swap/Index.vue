<template>
  <div>
    <div class="tabView">
      <div class="tabC">
        <tabs />

        <div class="symData">
          <div class="sym0Data" :class="{'focus': payIptFocus}">
            <div class="info flexb">
              <span>余额: {{ balanceSym0 }} {{ thisMarket0.symbol }}</span>
              <span class="type">支付</span>
            </div>
            <div class="iptDiv flexb">
              <div class="coinInfo flex" @click="listenShowDrawer('start')">
                <div class="coinImg"><img width="100%" :src="thisMarket0.imgUrl" :onerror="errorCoinImg" alt=""></div>
                <div>
                  <div class="coin">{{ thisMarket0.symbol }} <i class="el-icon-arrow-down"></i></div>
                  <div class="contract tip">{{ thisMarket0.contract }}</div>
                </div>
              </div>
              <div class="inputDiv">
                <el-input class="elIpt" type="number" v-model="payNum" placeholder="0.0"
                  @input="handleInBy('pay')"
                  @focus="handleFocus('pay')"
                  @blur="handleBlur('pay')"></el-input>
              </div>
            </div>
          </div>
        </div>
        <div class="exchange">
          <div class="border flexc" :class="{'payFocus': payIptFocus, 'getFocus': getIptFocus}" @click="handleExchange">
            <img class="iconImg" v-if="!direction" src="@/assets/img/dex/switch_down.svg">
            <img class="iconImg" v-else src="@/assets/img/dex/switch_up.svg">
          </div>
        </div>
        <div class="sym0Data pdb10" :class="{'focus': getIptFocus}">
          <div class="info flexb">
            <span class="ableGet">可兑: {{ balanceSym1 }} {{ thisMarket1.symbol }}</span>
            <span class="type">获取</span>
          </div>
          <div class="iptDiv flexb">
            <div class="coinInfo flex" @click="listenShowDrawer('end')">
              <div class="coinImg"><img width="100%" :src="thisMarket1.imgUrl" :onerror="errorCoinImg" alt=""></div>
              <div>
                <div class="coin">{{ thisMarket1.symbol }} <i class="el-icon-arrow-down"></i></div>
                <div class="contract tip">{{ thisMarket1.contract }}</div>
              </div>
            </div>
            <div class="inputDiv">
              <el-input class="elIpt" type="number" v-model="getNum" placeholder="0.0"
                @input="handleInBy('get')"
                @focus="handleFocus('get')"
                @blur="handleBlur('get')"></el-input>
            </div>
          </div>
        </div>

        <div class="rate flexb">
          <span class="tip">兑换比率</span>
          <span class="flex">
            <span v-if="!exRate">1{{ thisMarket1.symbol }} = {{ tradeInfo.aboutPrice || '-' }}{{ thisMarket0.symbol }}</span>
            <span v-else>1{{ thisMarket0.symbol }} = {{ tradeInfo.aboutPriceSym0 || '-' }}{{ thisMarket1.symbol }}</span>
            <span @click="exRate =!exRate">
              <img class="iconImg" v-if="!exRate" src="@/assets/img/dex/price_switch_icon_btn_left.svg" alt="">
              <img class="iconImg" v-else src="@/assets/img/dex/price_switch_icon_btn_right.svg" alt="">
            </span>
          </span>
        </div>
      </div>
      <!-- 价格滑点等 -->
      <el-collapse-transition>
        <div class="tabB" v-show="showDetail">
          <div class="flexb">
            <span class="flexb">
              <span class="tip">至少获取</span>
              <el-popover
                class="flexc"
                popper-class="mypopper"
                placement="top-start"
                trigger="click">
                <div class="qusTip">Your transaction will revert if there is a large, unfavorable price movement before it is confirmed.</div>
                <span slot="reference" class="flexc ml10"><img width="100%" src="@/assets/img/dex/tips_icon_btn.svg" alt=""></span>
              </el-popover>
            </span>
            <span>{{ tradeInfo.minOut }}</span>
          </div>
          <div class="flexb">
            <span class="flex">
              <span class="tip">价格滑点</span>
              <el-popover
                class="flexc"
                popper-class="mypopper"
                placement="top-start"
                trigger="click">
                <div class="qusTip">The difference between the market price and estimated price due to trade size.</div>
                <span slot="reference" class="flexc ml10"><img width="100%" src="@/assets/img/dex/tips_icon_btn.svg" alt=""></span>
              </el-popover>
            </span>
            <span class="green"
              :class="{'yellow': Number(tradeInfo.priceRate) > 5,
                       'red': Number(tradeInfo.priceRate) > 10}">
              {{ tradeInfo.priceRate }}%
            </span>
          </div>
          <div class="flexb fee">
            <span class="flex">
              <span class="tip">手续费</span>
              <el-popover 
                class="flexc"
                popper-class="mypopper"
                placement="top-start"
                trigger="click">
                <div class="qusTip">A portion of each trade (0.30%) goes to liquidity providers as a protocol incentive.</div>
                <span slot="reference" class="flexc ml10"><img width="100%" src="@/assets/img/dex/tips_icon_btn.svg" alt=""></span>
              </el-popover>
            </span>
            <span>{{fees}} {{ thisMarket0.symbol }}</span>
          </div>
        </div>
      </el-collapse-transition>
    </div>

    <div class="btnDiv">
      <div class="btn flexc" @click="handleSwapTrade">兑 换</div>
    </div>

    <div class="pool" v-if="marketLists.length && bestPath">
      <div>
        <span>流动池数量</span>
        <span class="marketNow">立即做市 ></span>
      </div>
      <div class="poolsNum">
        {{ bestPath.reserve0 }} / {{ bestPath.reserve1 }}
      </div>
    </div>
    <div v-else-if="routePath && false">
      多路径兑换： 
      <div>
        <span v-for="(item, i) in routePath" :key="i">
          <span>{{ item }}</span>
          <span class="el-icon-arrow-right"></span>
        </span>
      </div>
    </div>

    <!-- 弹窗组件 -->
    <el-dialog
      class="mkListDia pcList"
      :show-close="false"
      :visible.sync="showMarketList">
      <market-list :marketLists="marketLists" :thisMarket0="thisMarket0"
        :thisMarket1="thisMarket1" :type="type"
        @listenMarketChange="handleMarketChange"
        @listenClose="handleClose"/>
    </el-dialog>
  </div>
</template>

<script>
import { mapState } from 'vuex';
import { SwapRouter } from '@/utils/swap_router';
import Tabs from '../index/components/Tabs';
import { toFixed, accMul, accDiv, accSub } from '@/utils/public';
import { EosModel } from '@/utils/eos';
import MarketList from '@/components/MarketList';

export default {
  name: 'swap',
  components: {
    Tabs,
    MarketList
  },
  data() {
    return {
      errorCoinImg: 'this.src="https://ndi.340wan.com/eos/eosio.token-eos.png"',
      payNum: '',
      getNum: '',
      payIptFocus: false,
      getIptFocus: false,
      exRate: false,
      coinList: [],
      tradeInfo: {},
      slipPointUser: '1',
      direction: false,
      routeArr: [],
      balanceSym0: '0.0000',
      balanceSym1: '0.0000',
      timer: null,
      showMarketList: false,
      type: 'pay',
      thisMarket0: {
        mid: 1,
        last_update: "2020-05-14T06:49:27",
        liquidity_token: 2509980,
        price_cumulative_last: "10524156827",
        price_last: "2.52539999999999987",
        contract: "eosio.token",
        decimal: "4",
        reserve: "157.9329 EOS",
        sym: "4,EOS",
        symbol: "EOS",
      }, // 当前选中的做市池子
      thisMarket1: {
        mid: 7,
        last_update: "2020-05-14T06:49:27",
        liquidity_token: 2509980,
        price_cumulative_last: 1513579653,
        price_last: "0.39589999999999997",
        contract: "bankofusddv1",
        decimal: "4",
        reserve: "398.7956 USDD",
        sym: "4,USDD",
        symbol: "USDD",
      },
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
  computed: {
    ...mapState({
      scatter: state => state.app.scatter,
      slipPoint: state => state.app.slipPoint,
      baseConfig: state => state.sys.baseConfig,
    }),
    showDetail() {
      return Number(this.payNum) && Number(this.getNum)
    },
    fees() {
      let fee = accMul(Number(this.payNum), 0.003);
      return fee
    },
    bestPath() {
      const sym0 = this.thisMarket0;
      const sym1 = this.thisMarket1;
      const haspool = this.marketLists.find(v => (v.contract0 === sym0.contract || v.contract0 === sym1.contract)
                                  && (v.contract1 === sym0.contract || v.contract1 === sym1.contract))
      // console.log(haspool)
      return haspool;
    },
    routePath() {
      const m0 = this.thisMarket0
      const m1 = this.thisMarket1
      const params0 = `${m0.contract}:${m0.symbol}`
      const params1 = `${m1.contract}:${m1.symbol}`
      const path = SwapRouter.get_paths(params0, params1, true)
      if(!path) {
        return false
      }
      const pathArr = path.split('-')
      return pathArr
    }
  },
  watch: {
    slipPoint: {
      handler: function sl(newVal) {
        this.slipPointUser = newVal;
        this.handleInBy(this.tradeInfo.type)
      },
      immediate: true,
    },
    marketLists: {
      handler: function marketList(newVal) {
        // console.log(newVal)
        if (!newVal.length) {
          return
        }
        SwapRouter.init(newVal)
        const arr = this.handleDealSymArr(newVal)
        this.coinList = arr;
        if (!arr.length) {
          return;
        }
        const market0 = arr.find(v => v.contract === this.thisMarket0.contract && v.symbol === this.thisMarket0.symbol) || arr[0]
        this.thisMarket0 = market0;
        const market1 = arr.find(v => v.contract === this.thisMarket1.contract && v.symbol === this.thisMarket1.symbol) || arr[1]
        this.thisMarket1 = market1;
        this.handleInBy(this.tradeInfo.type, 'first')
      },
      immediate: true,
      deep: true
    },
    scatter: {
      handler: function listen(newVal) {
        if (newVal.identity) {
          this.handleBalanTimer();
        }
      },
      deep: true,
      immediate: true,
    },
    payNum(newVal, oldVal) {
      if (Number(newVal) < 0) {
        this.payNum = oldVal;
      }
    }
  },
  mounted() {
    // console.log(this.thisMarket0)
    // console.log(this.thisMarket1)
  },
  beforeDestroy() {
    clearInterval(this.timer)
  },
  methods: {
    handleClose() {
      this.showMarketList = false
    },
    listenShowDrawer(type) {
      this.type = type;
      // this.$emit('listenShowDrawer', false)
      this.showMarketList = true
    },
    // 处理所有币种
    handleDealSymArr(lists = []) {
      const resArr = [];
      lists.forEach((v) => {
        resArr.push(v.sym0Data, v.sym1Data)
      })
      // 删除重复项
      const newArr = resArr.filter((item, index, self) => {
        const i = self.findIndex(v => v.contract === item.contract && v.symbol === item.symbol);
        return self.indexOf(item) === i;
      })
      return newArr
    },
    handleInBy(type = 'pay', status) {
      const inData = {
        direction: this.direction,
        type,
        slipPointUser: accDiv(Number(this.slipPointUser), 100), // 滑点保护
      }
      if (type === 'pay') {
        inData.payNum = this.payNum || `${toFixed(1, this.thisMarket0.decimal)}`;
      } else {
        inData.getNum = this.getNum || `${toFixed(1, this.thisMarket1.decimal)}`;
      }
      try {
        // console.log(inData)
        const outData = this.handleDealAmountOut(inData);
        // console.log(outData)
        // in & out 都是0，非焦点ipt置空
        if (!Number(outData.payNum) && !Number(outData.getNum)) {
          if (type === 'pay') {
            this.getNum = '';
          } else {
            this.payNum = '';
          }
          return;
        }
        this.tradeInfo = outData;
        // 第一次计算价格
        if (status === 'first') {
          return;
        }
        // 支付框输入为空 || 获取框输入为空，两个输入框直接清空
        if ((type === 'pay' && this.payNum === '') || (type !== 'pay' && this.getNum === '')) {
          this.payNum = '';
          this.getNum = '';
          return;
        }
        type === 'pay' ? this.getNum = toFixed(outData.getNum, this.thisMarket1.decimal) :
                                      this.payNum = toFixed(outData.payNum, this.thisMarket0.decimal);
      } catch (error) {
        // console.log(error)
        this.tradeInfo = {}
        this.tradeInfo.aboutPrice = toFixed(0, this.thisMarket0.decimal)
        // console.log(error)
      }
    },
    // 计算得到多少 - 正序 - 输入支付
    handleDealAmountOut(inData) {
      // 没有输入支付数量 & 得到数量时 - 返回默认配置
      if (!Number(inData.payNum) && !Number(inData.getNum)) {
        const outData = {
          type: inData.type,
        }
        return outData
      }
      const m0 = this.thisMarket0
      const m1 = this.thisMarket1
      const params0 = `${m0.contract}:${m0.symbol}`
      const params1 = `${m1.contract}:${m1.symbol}`
      const path = SwapRouter.get_paths(params0, params1)
      const params = [
        path,
        params0,
      ]
      if (inData.type === 'pay') {
        params.push(accMul(inData.payNum, 10 ** this.thisMarket0.decimal))
      } else {
        params.push(accMul(inData.getNum, 10 ** this.thisMarket1.decimal))
        params.push(inData.type)
      }
      const res = SwapRouter.get_amounts_out(...params)
      const payNum = inData.type === 'pay' ? inData.payNum : res.quantity_out.split(' ')[0];
      const getNum = inData.type === 'pay' ? res.quantity_out.split(' ')[0] : inData.getNum;
      let minOut = 0;
      minOut = res.price * (1 - inData.slipPointUser) * payNum;
      minOut = toFixed(minOut, this.thisMarket1.decimal)

      let aboutPrice = payNum / getNum;
          aboutPrice = toFixed(aboutPrice, this.thisMarket0.decimal)
      let aboutPriceSym0 = getNum / payNum;
          aboutPriceSym0 = toFixed(aboutPriceSym0, this.thisMarket1.decimal)
      let priceRate = accDiv(aboutPriceSym0, res.price);
          priceRate = accSub(1, priceRate)
          priceRate = accMul(priceRate, 100)
          priceRate = toFixed(priceRate, 2)
      const obj = {
        payNum,
        getNum,
        aboutPrice,
        aboutPriceSym0,
        type: inData.type,
        minOut,
        price: res.price,
        priceRate,
      }
      // console.log(obj)
      return obj
    },
    handleReg() {
      if (!Number(this.payNum)) {
        return false;
      }
      const balance = !this.direction ? Number(this.balanceSym0) : Number(this.balanceSym1);
      if (Number(this.payNum) > balance) {
        this.$message({
          type: 'error',
          message: this.$t('public.balanLow')
        })
        return false;
      }
      return true
    },
    // swap交易
    handleSwapTrade() {
      if (!this.handleReg()) {
        return
      }
      const m0 = this.thisMarket0
      const m1 = this.thisMarket1
      const params0 = `${m0.contract}:${m0.symbol}`
      const params1 = `${m1.contract}:${m1.symbol}`
      const path = SwapRouter.get_paths(params0, params1)

      const tradeCoin = this.direction ? this.thisMarket1.symbol : this.thisMarket0.symbol;
      const minOutDecimal = this.direction ? this.thisMarket1.decimal : this.thisMarket0.decimal;
      const params = {
        code: this.direction ? this.thisMarket1.contract : this.thisMarket0.contract,
        toAccount: this.baseConfig.toAccountSwap,
        memo: `swap:${path}:${accMul(toFixed(this.tradeInfo.minOut, minOutDecimal), (10 ** minOutDecimal))}`,
        quantity: `${this.payNum} ${tradeCoin}`
      }
      // console.log(params)
      EosModel.transfer(params, (res) => {
        if(res.code) {
          this.$message({
            message: res.message,
            type: 'error'
          });
          return
        }
        this.handleBalanTimer();
        this.$message({
          message: this.$t('public.success'),
          type: 'success'
        });
      })
    },
    handleExchange() {
      this.direction = !this.direction;
      const t = this.thisMarket0;
      this.thisMarket0 = this.thisMarket1;
      this.thisMarket1 = t;
      this.payNum = '';
      this.getNum = '';
      this.tradeInfo = {};
      const b = this.balanceSym0;
      this.balanceSym0 = this.balanceSym1;
      this.balanceSym1 = b;
      this.handleInBy(this.tradeInfo.type, 'first')
    },
    handleFocus(type = 'pay') {
      type === 'pay' ? this.payIptFocus = true : this.getIptFocus = true
      const num = type === 'pay' ? Number(this.payNum) : Number(this.getNum);
      if (!num) {
        type === 'pay' ? this.payNum = '' : this.getNum = '';
        return
      }
      type === 'pay' ? this.payNum = Number(this.payNum) : this.getNum = Number(this.getNum);
    },
    handleBlur(type = 'pay') {
      type === 'pay' ? this.payIptFocus = false : this.getIptFocus = false
      if (type === 'pay' && this.payNum === '') {
        return
      }
      if (type !== 'pay' && this.getNum === '') {
        return
      }
      type === 'pay' ? this.payNum = toFixed(Number(this.payNum), this.thisMarket0.decimal)
                     : this.getNum = toFixed(Number(this.getNum), this.thisMarket1.decimal);
    },
    // 重启余额定时器
    handleBalanTimer() {
      clearInterval(this.timer);
      this.handleGetBalance();
      this.handleGetBalance('next');
      this.timer = setInterval(() => {
        this.handleGetBalance();
        this.handleGetBalance('next');
      }, 20000)
    },
    // 获取账户余额
    async handleGetBalance(next) {
      const params = {
        code: this.thisMarket0.contract,
        coin: this.thisMarket0.symbol,
        decimal: this.thisMarket0.decimal
      };
      if (next) {
        params.code = this.thisMarket1.contract;
        params.coin = this.thisMarket1.symbol;
        params.decimal = this.thisMarket1.decimal;
      }
      await EosModel.getCurrencyBalance(params, res => {
        let balance = toFixed('0.000000001', params.decimal);
        (!res || res.length === 0) ? balance : balance = res.split(' ')[0];
        if (next) {
          this.balanceSym1 = balance;
          return;
        }
        this.balanceSym0 = balance;
      })
    },
    handleMarketChange(item, type) {
      if (type === 'start') {
        this.thisMarket0 = item;
      } else {
        this.thisMarket1 = item;
      }
      this.handleBalanTimer();
      this.handleClose()
    }
  },
}
</script>

<style lang="scss" scoped>
.mypopper{
  .qusTip{
    padding: 10px;
    width: 240px;
    font-size: 24px;
  }
}
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
      background-image: url('../../assets/img/dex/enter_solid_default.svg');
      background-repeat: no-repeat;
      background-size: cover;
      &.payFocus{
        background-image: url('../../assets/img/dex/enter_solid_up.svg');
        background-repeat: no-repeat;
        background-size: cover;
      }
      &.getFocus{
        background-image: url('../../assets/img/dex/enter_solid_down.svg');
        background-repeat: no-repeat;
        background-size: cover;
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
    margin: 24px 0;
    padding: 0 40px;
    font-size: 26px;
    color: $color-black;
    .flexb{
      margin-top: 10px;
      &:first-child{
        margin-top: 0px;
      }
    }
    .fee{
      padding-bottom: 24px;
    }
    .ml10{
      width: 28px;
      margin-left: 10px;
    }
    .green{
      color: #02C698;
    }
    .yellow{
      color: #f29949;
    }
    .red{
      color: rgb(255, 104, 113);;
    }
  }
}
.btnDiv{
  margin: 40px 0;
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
</style>