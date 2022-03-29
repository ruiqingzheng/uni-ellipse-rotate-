<template>
  <view class="main">
    <view class="top">
      <view class="stage">
        <image
          mode="scaleToFill"
          src="https://images.unsplash.com/photo-1614642264762-d0a3b8bf3700?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80"
        />
      </view>
    </view>
    <view class="control">
      <view class="ring">
        <view class="centerPoint">
          <view
            v-for="(item, index) in items"
            :key="index"
            :class="[
              'item',
              active === index ? 'active' : '',
              index === active - step ? 'leftBottom' : '',
            ]"
            :style="{
              left: `${initPositions[index].x}px`,
              top: `${initPositions[index].y}px`,
            }"
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
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: "Hello",
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
  beforeDestroy: function () {
    console.log("beforeDestroy")
    this.clear()
  },
  created() {
    this.clear()
    // 顺时针 toRight , 逆时针  toLeft
    this.direction = "toRight"
    // 椭圆大小
    this.radiusShort = 150
    this.radiusLong = 300
    this.duplicateItems()
    this.initPositions = this.initEllipse(
      this.items.length,
      this.radiusLong,
      this.radiusShort
    )
    this.activePosition = this.getActivePosition()
    console.log("activePosition: ", this.activePosition)
    this.animate(this.items.length, this.radiusLong, this.radiusShort)
  },

  computed: {
    step() {
      const _step = Math.floor(this.items.length / 4)
      const _leftBottomIndex = this.active - _step
      const leftBottomIndex =
        _leftBottomIndex < 0
          ? this.items.length - 2 + _leftBottomIndex
          : _leftBottomIndex
      console.log(
        "active:",
        this.active,
        "step",
        _step,
        "leftBottomIndex:",
        leftBottomIndex
      )
      return _step
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
      console.log(theta)

      for (let i = 0; i < count; i++) {
        let x = Math.round(Math.cos(theta[i]) * radiusLong)
        let y = Math.round(Math.sin(theta[i]) * radiusShort)
        result.push({
          x,
          y,
        })
      }

      console.log(JSON.stringify(result))

      return result
    },

    // todo: 判断左右, 根据左右位置进行screw变形
    // 当active 确定的时候, 它的最边上的元素可以通过下标确定最远端元素,
    // 方向也是确定的, 就可以进行动画

    // 确定这个item 在active的左边还是右边
    itemOnLeftOrRight(index) {
      if (index === active) return
      return index > this.active ? "right" : "left"
    },

    getActivePosition() {
      const x = Math.cos(Math.PI / 2) * this.radiusLong
      const y = -Math.sin(Math.PI / 2) * this.radiusShort
      return { x, y }
    },

    // 动画
    animate(count, radiusLong, radiusShort) {
      let theta = this.initRadian(count)
      // 移动弧长
      let move = Math.PI / 60
      console.log("theta:", theta)

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
            if (
              that.active !== i &&
              that.initPositions[i].x - that.activePosition.x < 50 &&
              that.initPositions[i].y - that.activePosition.y < 5
            ) {
              that.active = i
              console.log("active:", that.active)
            }
          })
        }, 150)
      }
      _animate(radiusLong, radiusShort)
    },
    clear() {
      clearInterval(this.animateInterval)
      this.animateInterval = null
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
  // overflow: hidden;
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
    border: solid 1px red;
    margin: 90px auto 10px;
    display: flex;
    justify-content: center;
    align-items: center;
    .stage {
      position: relative;
      width: 400px;
      height: 200px;
      margin: 50px auto 10px;
      border: dashed 3px $orange-100;
      display: flex;
      justify-content: center;
      align-items: center;
      // overflow: hidden;
    }
  }

  .control {
    height: 70%;
    width: 100%;
    border: solid 2px white;
    display: flex;
    flex-direction: column;
    justify-content: end;
    align-items: center;
    .ring {
      position: relative;
      width: 200px;
      height: 200px;
      margin-bottom: -40px;
      border: solid 3px $orange-100;
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
      }
    }
  }
}
</style>
