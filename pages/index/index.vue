<template>
  <view class="main">
    <view class="top">
      <view
        class="stage"
        @click="
          () => {
            showModal()
          }
        "
      >
        <image mode="scaleToFill" :src="items[active].image" />
      </view>
    </view>
    <view class="control">
      <view class="ring">
        <view id="centerPoint" ref="centerPoint" class="centerPoint">
          <view
            v-for="(item, index) in items"
            :key="index"
            :class="[
              'item',
              active === index ? 'active' : '',
              leftBottomIndex === index ? 'leftBottom' : '',
              rightBottomIndex === index ? 'rightBottom' : '',
            ]"
            :style="{
              left: `${initPositions[index].x}px`,
              top: `${initPositions[index].y}px`,
            }"
            @touchstart="(e) => itemTouchstart(e, item)"
            @touchmove="(e) => itemTouchmove(e, item)"
            @touchend="(e) => itemTouchend(e, item)"
          >
            <image class="itemBackground" :src="item.image" />
            {{ item.name }}
          </view>
          <view
            class="activePosition"
            :style="{
              left: `${activePosition.x}px`,
              top: `${activePosition.y}px`,
            }"
            >activePosition</view
          >
        </view>
      </view>
    </view>

    <view
      class="modal-mask"
      @click="closeModal()"
      :style="!modalShow && { display: 'none' }"
    >
      <view class="modal-content">
        {{ items[active].name }}
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: "Hello",
      modalShow: false,
      active: 0,
      initPositions: [],
      items: [
        {
          index: 0,
          name: "item1",
          image:
            "https://images.unsplash.com/photo-1614642264762-d0a3b8bf3700?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        },
        {
          index: 1,
          name: "item2",
          image:
            "https://images.unsplash.com/photo-1589225529399-8705282f98e2?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        },
        {
          index: 2,
          name: "item3",
          image:
            "https://images.unsplash.com/photo-1590647030647-0bc9c30cc07e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        },
        {
          index: 3,
          name: "item4",
          image:
            "https://images.unsplash.com/photo-1592029383200-73fb26a5b925?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        },
        {
          index: 4,
          name: "item5",
          image:
            "https://images.unsplash.com/photo-1511553677255-ba939e5537e0?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=928&q=80",
        },
        {
          index: 5,
          name: "item6",
          image:
            "https://images.unsplash.com/photo-1503939313441-d753b6c7eb91?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        },
        {
          index: 6,
          name: "item7",
          image:
            "https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        },
        // { index: 7, name: 'item8', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
      ],
    }
  },

  onLoad() {},
  onUnload() {
    console.log("onUnload")
    this.clear()
  },
  // 组件销毁前清理interval
  beforeDestroy: function () {
    console.log("beforeDestroy")
    this.clear()
  },
  created() {
    this.clear()
    // 顺时针 toRight , 逆时针  toLeft
    this.direction = "toRight"
    // this.direction = "toLeft"
    // 椭圆大小
    this.radiusShort = 150
    this.radiusLong = 500
    this.duplicateItems()
    // control items 的位置
    // 用来存角度
    this.currentItemsRadian = this.initRadian(this.items.length)
    this.initPositions = this.initEllipse(
      this.items.length,
      this.radiusLong,
      this.radiusShort
    )
    this.activePosition = this.getActivePosition()
    console.log("activePosition: ", this.activePosition)
    // control items 是否开始手势滑动
    this.itemTouchStarted = false
    // 开始椭圆轮播动画
    // todo
    this.startAnimate()
    // 滑动开始位置
    this.initTouch = { x: null, y: null }
    this.centerPoint = { x: null, y: null }
  },

  mounted() {
    const query = uni.createSelectorQuery()
    // 获取中心点的坐标
    query
      .select("#centerPoint")
      .boundingClientRect((rect) => {
        //rect是包含元素大小、位置等数据的对象
        // console.log("center point rect:", rect)
        this.centerPoint = {
          x: rect.left,
          y: rect.top,
        }
      })
      .exec(() => {
        //节点信息获取成功后执行的回调函数
      })
  },

  computed: {
    leftBottomIndex() {
      const _step = Math.floor(this.items.length / 4)
      const _leftBottomIndex = this.active - _step
      const leftBottomIndex =
        _leftBottomIndex < 0
          ? this.items.length - 2 + _leftBottomIndex
          : _leftBottomIndex
      // console.log(
      //   "active:",
      //   this.active,
      //   "step",
      //   _step,
      //   "leftBottomIndex:",
      //   leftBottomIndex
      // )
      return leftBottomIndex
    },

    rightBottomIndex() {
      const _step = Math.floor(this.items.length / 4)
      const _rightBottomIndex = (this.active + _step + 1) % this.items.length

      // console.log(
      //   "active:",
      //   this.active,
      //   "step",
      //   _step,
      //   "_rightBottomIndex:",
      //   _rightBottomIndex
      // )
      return _rightBottomIndex
    },
  },
  methods: {
    duplicateItems() {
      this.items = [...this.items, ...this.items]
      // this.items = [...this.items]
    },

    // 初始化弧度
    initRadian(count) {
      let averageAngle = 360 / count
      let theta = []
      for (let i = 0; i < count; i++) {
        theta.push((i * averageAngle * Math.PI) / 180)
      }
      return theta
    },

    // 椭圆位置
    initEllipse(count, radiusLong, radiusShort) {
      let result = []
      let theta = this.initRadian(count)
      this.currentItemsRadian = theta
      console.log("initEllipse:", theta, this.currentItemsRadian)

      for (let i = 0; i < count; i++) {
        let x = Math.round(Math.cos(theta[i]) * radiusLong)
        let y = Math.round(Math.sin(theta[i]) * radiusShort)
        result.push({
          x,
          y,
        })
      }

      // console.log(JSON.stringify(result))

      return result
    },

    // todo: 判断左右, 根据左右位置进行screw变形?
    // 当active 确定的时候, 它的最边上的元素可以通过下标确定最远端元素,
    // 方向也是确定的, 就可以进行动画

    // 确定这个item 在active的左边还是右边
    itemOnLeftOrRight(index) {
      if (index === active) return
      return index > this.active ? "right" : "left"
    },

    // 获取中间active的位置
    getActivePosition() {
      const x = Math.cos(Math.PI / 2) * this.radiusLong
      const y = -Math.sin(Math.PI / 2) * this.radiusShort
      return { x, y }
    },

    // 停止动画

    stopAnimate() {
      this.clear()
    },

    // 开始动画

    startAnimate() {
      // interval 存在, 动画已经在运行, 则退出
      if (this.animateInterval) return
      this.animate(this.items.length, this.radiusLong, this.radiusShort)
    },

    // 滑动开始 , 初始化坐标
    itemTouchstart(e, item) {
      // console.log("itemTouchStart", e, "item:", item)
      this.itemTouchStarted = true
      this.initTouch = {
        x: e.touches[0].pageX,
        y: e.touches[0].pageY,
      }
    },

    // 滑动中同样持续更新保存角度
    itemTouchmove(e, item) {
      // console.log("itemTouchMove", e, "item:", item)

      // 错误处理, 初始位置等没有初始化都不进行改变操作直接退出
      if (!this.itemTouchStarted) return
      if (
        !this.initTouch.x ||
        !this.initTouch.y ||
        !this.centerPoint.x ||
        !this.centerPoint.y
      ) {
        console.warn("滑动事件获取初始坐标有误...")
        return
      }

      const currentTouch = {
        x: e.changedTouches[0].pageX,
        y: e.changedTouches[0].pageY,
      }

      if (!this.currentItemsRadian || this.currentItemsRadian.length < 1) {
        console.warn(
          "滑动事件获取初始位置有误...",
          this.initTouch,
          this.currentItemsRadian
        )
      }

      e.preventDefault()
      // 滑动太短取消操作
      if (
        Math.abs(currentTouch.x - this.initTouch.x) < 2 ||
        Math.abs(currentTouch.y - this.initTouch.y) < 2
      ) {
        console.log("滑动移动量太小, 取消处理...")
        return
      }
      // 手势滑动, 先取消动画
      this.stopAnimate()
      // console.log(
      //   "currentTouch:",
      //   currentTouch,
      //   "initTouch:",
      //   this.initTouch,
      //   "centerPoint:",
      //   this.centerPoint,
      //   "currentItemsRadian",
      //   this.currentItemsRadian
      // )
      // 计算角度 , 需要更新所有item的角度
      for (let i = 0; i < this.currentItemsRadian.length; i++) {
        const dist = Math.atan2(
          (this.centerPoint.x - currentTouch.x) *
            (this.centerPoint.y - this.initTouch.y) -
            (this.centerPoint.y - currentTouch.y) *
              (this.centerPoint.x - this.initTouch.x),
          (this.centerPoint.x - currentTouch.x) *
            (this.centerPoint.x - this.initTouch.x) +
            (this.centerPoint.y - currentTouch.y) *
              (this.centerPoint.y - this.initTouch.y)
        )
        // let _angle = -dist * (180 / Math.PI)
        // console.log("dist", dist)
        this.currentItemsRadian[i] =
          (this.currentItemsRadian[i] - dist) % (2 * Math.PI)
      }

      // 根据角度改变来更新位置
      this._onTouchMoveUpdateItemPosition()
    },

    _onTouchMoveUpdateItemPosition() {
      if (!this.currentItemsRadian || this.currentItemsRadian.length < 0) {
        return
      }

      for (let i = 0; i < this.currentItemsRadian.length; i++) {
        const radian = this.currentItemsRadian[i]
        console.log("radian:", radian)
        this.initPositions[i].x = Math.round(Math.cos(radian) * this.radiusLong)
        this.initPositions[i].y = Math.round(
          Math.sin(radian) * this.radiusShort
        )
        // 手势移动位置后, 判断item是否是active
        if (this.active !== i && this.isActive(this.initPositions[i])) {
          this.active = i
        }
      }

      console.log("this.currentItemsRadian:", this.currentItemsRadian)
      console.log("this.initPositions :", this.initPositions)
    },

    // 滑动结束, 开始动画
    itemTouchend(e, item) {
      // console.log("itemTouchEnd", e, "item:", item)
      //todo
      this.startAnimate()
      this.itemTouchStarted = false
    },

    // 动画
    animate(count, radiusLong, radiusShort) {
      // 如果有上次的角度则继续上次的角度
      let theta = this.currentItemsRadian
        ? this.currentItemsRadian
        : this.initRadian(count)
      // 移动弧长
      let move = Math.PI / 60
      // console.log("theta:", theta)

      const that = this

      const _animate = function (_radiusLong, _radiusShort) {
        that.animateInterval = setInterval(() => {
          theta.forEach((item, i, arr) => {
            arr[i] =
              that.direction === "toRight"
                ? (item + move) % (2 * Math.PI)
                : (item - move) % (2 * Math.PI)
            that.initPositions[i].x = Math.round(
              Math.cos(theta[i]) * _radiusLong
            )
            that.initPositions[i].y = Math.round(
              Math.sin(theta[i]) * _radiusShort
            )
            // 判断是否到达中间位置
            if (that.active !== i && that.isActive(that.initPositions[i])) {
              that.active = i
              // console.log("active:", that.active)
            }
          })

          // 实时保留角度
          that.currentItemsRadian = theta
        }, 150)
      }
      _animate(radiusLong, radiusShort)
    },

    // 根据传递的item position 判断是否到达active中间位置
    isActive(itemPosition) {
      return (
        itemPosition.x - this.activePosition.x < 50 &&
        itemPosition.y - this.activePosition.y < 5
      )
    },

    clear() {
      clearInterval(this.animateInterval)
      this.animateInterval = null
    },

    // 弹窗关闭
    closeModal() {
      this.modalShow = false
      this.startAnimate()
    },

    // 打开弹窗
    showModal() {
      this.modalShow = true
      this.stopAnimate()
    },
  },
}
</script>

<style lang="scss">
page {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

$orange: #ff7849;

$orange-100: #fdba74;
$orange-500: #f97316;
$yellow-300: #fde047;
.main {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  display: grid;
  grid-auto-flow: column;
  grid-template-columns: repeat(1, minmax(0, 1fr));
  grid-template-rows: 70% 30%;
  grid-auto-columns: auto;
  // align-items: center;
  // // justify-content: center;
  background-image: url("https://images.unsplash.com/photo-1464802686167-b939a6910659?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1450&q=80");
  background-position: cover;
  background-repeat: no-repeat;
  position: relative;

  .top {
    height: 80%;
    width: 70%;
    // border: solid 1px red;
    margin: 90px auto 10px;
    display: flex;
    justify-content: center;
    align-items: center;
    .stage {
      position: relative;
      width: 400px;
      height: 200px;
      margin: 50px auto 10px;
      // border: dashed 3px $orange-100;
      display: flex;
      justify-content: center;
      align-items: center;
      // overflow: hidden;
    }
  }

  .control {
    height: 70%;
    width: 100%;
    // border: solid 2px white;
    // overflow: hidden;
    display: flex;
    flex-direction: column;
    justify-content: end;
    align-items: center;
    position: relative;
    // cut-half

    .ring {
      position: relative;
      width: 200px;
      height: 200px;
      // 显示ring border  调整底部可见位置
      // border: solid 3px $orange-100;
      margin-bottom: -180px;
      border-radius: 50%;
      display: flex;
      justify-content: center;
      align-items: center;

      .centerPoint {
        position: relative;
        width: 1px;
        height: 1px;
        border-radius: 50%;
        .activePosition {
          position: absolute;
          width: 50px;
          height: 50px;
          z-index: 2;
          border: solid 1px $yellow-300;
          background-color: red;
          border-radius: 50%;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 11px;
          transform: translate(-50%, -50%);
          display: none;
        }
        .item {
          position: absolute;
          width: 50px;
          height: 50px;
          z-index: 2;
          border: solid 1px $yellow-300;
          background-color: white;
          border-radius: 5px;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 11px;
          transform: translate(-50%, -50%);
          .itemBackground {
            border-radius: 5px;
            position: absolute;
            top: 0;
            left: 0;
            overflow: hidden;
            width: 100%;
            height: 100%;
          }
        }
        .active {
          background-color: $orange-500;
          color: white;
          border: solid 3px $orange-100;
          font-size: 12px;
          font-weight: bold;
          width: 60px;
          height: 60px;
          transition-property: width, height;
          // transition-duration: 0.1s;
          // transform: scale(1.1);
        }
        .leftBottom {
          width: 40px;
          height: 40px;
          transition-property: width, height;
        }
        .rightBottom {
          width: 40px;
          height: 40px;
          transition-property: width, height;
        }
      }
    }
  }
  .modal-mask {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    z-index: 3;
    background-color: rgba(0, 0, 0, 0.5);
    .modal-content {
      // position: absolute;
      width: 600rpx;
      height: 800rpx;
      border-radius: 20rpx;
      background-color: white;
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
    }
  }
}
</style>
