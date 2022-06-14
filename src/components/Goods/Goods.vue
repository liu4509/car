<template>
  <div class="goods-container">
    <!-- 左侧图片 -->
    <div class="thumb">
      <div class="custom-control custom-checkbox">
        <!-- 复选框 -->
        <input
          type="checkbox"
          class="custom-control-input"
          :id="'cd' + id"
          :checked="state"
          @change="statusChange"
        />
        <label class="custom-control-label" :for="'cd' + id">
          <!-- 商品的缩略图 -->
          <img :src="pic" alt="" />
        </label>
      </div>
    </div>
    <!-- 右侧信息区域 -->
    <div class="goods-info">
      <!-- 商品标题 -->
      <h6 class="goods-title">{{ title }}</h6>
      <div class="goods-info-bottom">
        <!-- 商品价格 -->
        <span class="goods-price">￥{{ price }}</span>
        <!-- 商品的数量 -->
        <!-- <Counter :num="count" :id="id"></Counter> -->
        <!-- 使用自定义插槽实现 -->
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
// 导入组件
// import Counter from "@/components/Counter/Counter.vue";

export default {
  // 获取父组件传过来的自定义数据
  props: {
    id: {
      type: Number,
      required: true,
    },
    title: {
      type: String,
      default: "",
    },
    price: {
      type: Number,
      default: 0,
    },
    pic: {
      type: String,
      default: "",
    },
    state: {
      type: Boolean,
      default: true,
    },
    count: {
      type: Number,
      default: 1,
    },
  },
  methods: {
    //多选框的自定义事件
    statusChange(e) {
      // console.log(this);
      let flog = e.target.checked;
      this.$emit("state-change", { id: this.id, flog: flog });
    },
  },
  components: {
    // Counter,
  },
};
</script>

<style lang="less" scoped>
.goods-container {
  + .goods-container {
    border-top: 1px solid #efefef;
  }
  padding: 10px;
  display: flex;
  .thumb {
    display: flex;
    align-items: center;
    img {
      width: 100px;
      height: 100px;
      margin: 0 10px;
    }
  }

  .goods-info {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    flex: 1;
    .goods-title {
      font-weight: bold;
      font-size: 12px;
    }
    .goods-info-bottom {
      display: flex;
      justify-content: space-between;
      .goods-price {
        font-weight: bold;
        color: red;
        font-size: 13px;
      }
    }
  }
}
</style>
