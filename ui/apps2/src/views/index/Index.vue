<template>
  <div>
    <main-view :marketLists="marketLists"/>
    <div class="checkOrder tip">
      <span :class="{'act': act === 1}" @click="handleChangeAct(1)">兑换记录</span>
      <span :class="{'act': act === 2}" @click="handleChangeAct(2)">我的订单</span>
    </div>
    <order :act="act"/>
  </div>
</template>

<script>
import MainView from './components/MainView';
import Order from './components/Order';

export default {
  name: 'index',
  components: {
    MainView,
    Order,
  },
  data() {
    return {
      act: 1,
    }
  },
  computed:{
  },
  props: {
    marketLists: {
      type: Array,
      default: function lists() {
        return []
      }
    }
  },
  watch: {
  },
  mounted() {
  },
  beforeDestroy() {
  },
  methods: {
    handleChangeAct(num) {
      this.act = num;
    }
  }
}
</script>

<style lang="scss" scoped>
.checkOrder{
  font-size: 32px;
  text-align: left;
  margin: 44px 60px;
  &>span{
    margin-right: 60px;;
  }
  .act{
    color: $color-black;
    position: relative;
    &::before{
      content: '';
      position: absolute;
      width:60px;
      height:8px;
      background:rgba(2,198,152,1);
      border-radius:4px;
      bottom: -20px;
      left: 50%;
      transform: translateX(-45%);
    }
  }
}
</style>
