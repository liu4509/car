<template>
  <div class="app-container">
    <!-- 标题 -->
    <Header></Header>
    <!-- 商品列表 -->
    <Goods
      v-for="item in list"
      :key="item.id"
      :id="item.id"
      :title="item.goods_name"
      :price="item.goods_price"
      :pic="item.goods_img"
      :state="item.goods_state"
      :count="item.goods_count"
      @state-change="getNewChange"
    >
      <!-- // 自定义插槽  Counter -->
      <Counter
        :num="item.goods_count"
        @num-change="numNewchange(item, $event)"
        @sub-change="subNewchange(item, $event)"
      ></Counter>
    </Goods>
    <Footer
      :isfull="fullState"
      :amout="amt"
      :all="total"
      @full-Change="inputAllChange"
    ></Footer>
  </div>
</template>

<script>
// 导入 axios 工具
import axios from "axios";

// 导入组件
import Header from "@/components/Header/Header.vue";
import Goods from "@/components/Goods/Goods.vue";
import Footer from "@/components/Footer/Footer.vue";
import bus from "@/components/eventBus.js";

// 导入 插槽组件
import Counter from "@/components/Counter/Counter.vue";

export default {
  data() {
    return {
      list: [],
    };
  },
  methods: {
    // 获取全部数据
    async initCarList() {
      const { data: res } = await axios.get("https://www.escook.cn/api/cart");
      if (res.status === 200) {
        this.list = res.list;
      }
    },
    // 接收 goods 子组件传过来的数据
    // 改变 list 里面的数据
    getNewChange(val) {
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_state = val.flog;
          return true;
        } else {
          return false;
        }
      });
    },
    // 接收 footer 子组件传过来的数据
    // 通过改变 全选多选框状态 而改变商品多选框的状态
    inputAllChange(val) {
      this.list.forEach((item) => (item.goods_state = val));
    },
    //调用子组件的方法
    numNewchange(val, e) {
      val.goods_count = e;
    },
    subNewchange(val, e) {
      val.goods_count = e;
    },
  },
  components: {
    Header,
    Goods,
    Footer,
    Counter,
  },
  computed: {
    // 通过 Array.every 方法 判断 商品全部多选框的状态
    // 全部为 trun 返回 trun 否则返回 false
    fullState() {
      return this.list.every((item) => item.goods_state);
    },
    // 通过 Array.filter() 筛选 item.goods_state 为 trun
    // 为新数组传入 reduce() 累加 总价格 返回 计算属性
    amt() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce(
          (total, item) => (total += item.goods_price * item.goods_count),
          0
        );
    },
    // 通过 Array.filter() 筛选 item.goods_state 为 trun
    // 为新数组传入 reduce() 累加 结算结果 商品数量 返回 计算属性
    total() {
      return this.list
        .filter((item) => item.goods_state)
        .reduce((loa, item) => (loa += item.goods_count), 0);
    },
  },
  // created VUE 生命周期 调用 initCarList() 获取list
  created() {
    this.initCarList();

    // 把bus.$emit()值 传出 通过bus.@on('自定义事件')
    bus.$on("share", (val) => {
      this.list.some((item) => {
        if (item.id === val.id) {
          item.goods_count = val.value;
          return true;
        }
      });
    });
  },
};
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
