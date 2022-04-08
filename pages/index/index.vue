<template>
  <view
    class="main"
    @touchstart="(e) => mainTouchstart(e)"
    @touchmove="(e) => debouncedMainTouchMove(e)"
    @touchend="(e) => mainTouchend(e)"
  >
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
              leftBottomIndex_right === index ? 'leftBottom-right' : '',
              leftBottomIndex_left === index ? 'leftBottom-left' : '',
              rightBottomIndex === index ? 'rightBottom' : '',
              rightBottomIndex_left === index ? 'rightBottom-left' : '',
              rightBottomIndex_right === index ? 'rightBottom-right' : '',
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

    <view
      class="modal-mask"
      @click="closeModal()"
      :style="!modalShow && { display: 'none' }"
    >
      <view class="modal-content" @click="clickModalContent">
        {{ items[active].name }}
      </view>
    </view>

    <view class="bottom-box">
      <view class="bottom-box-center">
        <view class="a-button left-button" @tap="tapButton('left')"></view>
        <view class="a-button right-button" @tap="tapButton('right')"></view>
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
        // {
        //   index: 7,
        //   name: "item8",
        //   image:
        //     "https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80",
        // },
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
    // console.log("beforeDestroy")
    this.clear()
  },
  created() {
    this.clear()
    // 顺时针 toRight , 逆时针  toLeft
    this.direction = "toRight"
    // this.direction = "toLeft"
    // 椭圆大小
    this.radiusShort = 300
    this.radiusLong = 500
    this.duplicateItems()
    // control items 的位置
    // 用来存角度
    this.currentItemsRadian = this.initRadian(this.items.length, "init")
    this.initPositions = this.initEllipse(
      this.items.length,
      this.radiusLong,
      this.radiusShort
    )
    this.activePosition = this.getActivePosition()
    // console.log("activePosition: ", this.activePosition)
    // control items 是否开始手势滑动
    this.itemTouchStarted = false
    // main touch 手势
    this.mainTouchStarted = false
    // main touch x 初始坐标, 只判断横移
    this.mainTouchInitPos = 0
    // main Touch x 移动了多少
    this.mainTouchMovedX = 0
    // item Touch x 移动了多少
    this.itemTouchMovedX = 0
    // main touch 实时x位置
    this.currentMainTouchPos = 0
    // 一个格子的弧度
    this.stepRadian = (2 * Math.PI) / this.items.length
    // 屏幕宽度
    this.windowWidth = 0
    // 滑动减速
    this.slowTimes = 10
    // 自转减速
    this.slowTimesForAutoSpin = 200
    // 是否自动动画
    this.autoAnimate = false
    // 是否暂停动画
    this.paused = false
    // 开始动画, 动画包括自动动画和手势移动时候动画
    this.startAnimate()
    // item 滑动开始位置
    this.initTouch = { x: null, y: null }
    // item 手势拖动实时位置
    this.currentItemTouchPos = {
      x: null,
      y: null,
    }
    // control 椭圆中心位置
    this.centerPoint = { x: null, y: null }

    this.debouncedMainTouchMove = this.debounce(this.mainTouchmove, 500)
    this.debouncedItemTouchMove = this.debounce(this.itemTouchmove, 500)
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

    uni.getSystemInfo({
      success: (res) => {
        this.windowWidth = res.windowWidth
      },
    })

    this.initPositions.forEach((itemPosition, i) => {
      console.log("itemPosition", itemPosition)
      if (this.active !== i && this.isActive(itemPosition)) {
        this.active = i
      }
    })
  },

  computed: {
    rightBottomIndex() {
      const _step = Math.floor(this.items.length / 4)
      const _rightBottomIndex = (this.active + _step) % this.items.length

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

    leftBottomIndex() {
      let leftBottomIndex = this.rightBottomIndex + this.items.length / 2
      if ((this.items.length / 2) % 2 == 1) {
        leftBottomIndex += 1
      }
      // console.log("leftBottomIndex:", leftBottomIndex)
      return leftBottomIndex % this.items.length
    },

    rightBottomIndex_left() {
      return (this.rightBottomIndex + this.items.length - 1) % this.items.length
    },

    rightBottomIndex_right() {
      return (this.rightBottomIndex + 1) % this.items.length
    },

    leftBottomIndex_right() {
      return (this.leftBottomIndex + 1) % this.items.length
    },

    leftBottomIndex_left() {
      return (this.leftBottomIndex + this.items.length - 1) % this.items.length
    },
  },
  methods: {
    debounce(fn, delay) {
      let timer = null //借助闭包
      return function (...args) {
        if (timer) {
          clearTimeout(timer) //进入该分支语句，说明当前正在一个计时过程中，并且又触发了相同事件。所以要取消当前的计时，重新开始计时
          timer = setTimeout(fn(...args), delay)
        } else {
          timer = setTimeout(fn(...args), delay) // 进入该分支说明当前并没有在计时，那么就开始一个计时
        }
      }
    },

    mainTouchstart(e) {
      console.log("mainTouchstart, ", e)
      this.mainTouchInitPos = e.touches[0].clientX
      this.startUpdateTouchMovePrevPosition()
    },

    mainTouchmove(e) {
      const currentPosition = e.touches[0].clientX
      // console.log("mainTouchmove", e, "currentPosition", currentPosition)

      e.preventDefault()

      // 滑动太少不处理
      // if (Math.abs(currentPosition - this.mainTouchInitPos) < 1) {
      //   console.log("mainTouch滑动移动量太小, 取消处理...")
      //   this.mainTouchStarted = false
      //   return
      // }

      // 如果item 也在滑动, 则也不处理

      if (this.itemTouchStarted) {
        console.log("mainTouch滑动移动 , itemTouch中,  取消mainTouch处理")
        this.mainTouchStarted = false
        return
      }

      this.mainTouchStarted = true
      this.paused = true
      // e.preventDefault()
      if (this.mainTouchStarted) {
        // 根据横移滑动计算角度
        const touchMove = currentPosition - this.mainTouchInitPos
        this.mainTouchMovedX = touchMove
        this.currentMainTouchPos = currentPosition

        let touchMoveRadian = (touchMove / this.windowWidth) * 2 * Math.PI

        // console.log(
        //   "currentPosition:",
        //   currentPosition,
        //   "mainTouchInitPos:",
        //   this.mainTouchInitPos,
        //   "this.windowWidth",
        //   this.windowWidth,
        //   "touchMove",
        //   touchMove,
        //   "touchMoveRadian:",
        //   touchMoveRadian
        // )

        // todo: 角度更新方式
        // const step = (2 * Math.PI) / this.items.length
        // if (Math.abs(touchMoveRadian) > step) {
        //   touchMoveRadian = touchMoveRadian > 0 ? step : -step
        // }

        touchMoveRadian = touchMoveRadian / this.slowTimes
        for (let i = 0; i < this.currentItemsRadian.length; i++) {
          this.currentItemsRadian[i] =
            (this.currentItemsRadian[i] + touchMoveRadian) % (2 * Math.PI)
        }
      }
    },
    mainTouchend(e) {
      this.paused = false
      if (!this.mainTouchStarted) return
      console.log("mainTouchend", e)
      this.fixedRadians()
      this.mainTouchStarted = false
      this.mainTouchMovedX = 0
    },

    // 无论是main touch  还是 item touch 都让其角度为定格的角度
    fixedRadians() {
      // todo: 判断移动了多少, 直接角度切换一个格子 ???
      // 判断左右, 直接把defaultRadians中的元素移动位置
      const step = (2 * Math.PI) / this.items.length
      const defaultRadians = this.initRadian(this.items.length)

      // 找出最接近0的偏移量, 也就是找出绝对值最小的元素
      let min_r = null
      let min_index = null

      if ((this.items.length / 2) % 2 == 1) {
        this.currentItemsRadian.forEach((r, index) => {
          let tmp = r - -Math.PI / 2
          if (min_r === null) {
            min_r = tmp
            min_index = index
          }

          if (Math.abs(tmp) < Math.abs(min_r)) {
            min_r = tmp
            min_index = index
          }
        })
      } else {
        this.currentItemsRadian.forEach((r, index) => {
          if (!min_r) {
            min_r = r
            min_index = index
          }

          if (Math.abs(r) < Math.abs(min_r)) {
            min_r = r
            min_index = index
          }
        })
      }

      // console.log("min_r", min_r, "min_index:", min_index)

      let count = Math.abs(this.items.length - min_index) % this.items.length

      // console.log("this.mainTouchMovedX", this.mainTouchMovedX)

      // todo : 无论如何都无法阻止main Touch 的执行 , 所以只需要保留main touch 即可, 完全可以删除 item touch
      // 当只是item touch的时候,  在mainTouch中希望通过判断item Touch start来阻止mainTouchMove的执行
      // 但是因为mainTouch 每次都会先触发 , 于是item touch end 后执行到这里的时候
      // 每次也能正常判断mainTouchMovedX
      if (this.mainTouchMovedX > 0) {
        console.log("右边移动....")
        if (min_r > 0 && Math.abs(min_r) > step / 9) count += 1
        for (let i = 0; i < count; i++) {
          let _r = defaultRadians.shift()
          defaultRadians.push(_r)
          // let _r = defaultRadians.pop()
          // defaultRadians.unshift(_r)
        }
      } else if (this.mainTouchMovedX < 0) {
        console.log("left边移动....")
        // if (min_r < 0 && Math.abs(min_r) > step / 10) count += 1
        // if (min_r > 0) min_index -= 1
        for (let i = 0; i < count; i++) {
          let _r = defaultRadians.shift()
          defaultRadians.push(_r)
        }
      }
      // console.log("main touch end defaultRadians: ", defaultRadians)
      this.currentItemsRadian = defaultRadians
      this._onTouchMoveUpdateItemPosition()
    },

    duplicateItems() {
      this.items = [...this.items, ...this.items]
      // this.items = [...this.items]
    },

    // 初始化弧度
    initRadian(count, type) {
      let averageAngle = 360 / count
      let theta = []
      for (let i = 0; i < count; i++) {
        let radian = (i * averageAngle * Math.PI) / 180
        // todo
        if ((this.items.length / 2) % 2 === 1) {
          radian = radian - Math.PI / 2
        }
        theta.push(radian)
      }
      return theta
    },

    // 椭圆位置
    initEllipse(count, radiusLong, radiusShort) {
      let result = []
      // let theta = this.initRadian(count)
      // this.currentItemsRadian = theta
      let theta = this.currentItemsRadian
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

    // 开始动画

    startAnimate() {
      // interval 存在, 动画已经在运行, 则退出
      if (this.animateInterval) return
      this.animate()
    },

    // 及时更新上次touch的位置, mainTouch 和 itemTouch
    // 单独一个interval 是因为动画的间隔时间短, 而更新上次touch位置需要的间隔长不少
    startUpdateTouchMovePrevPosition() {
      if (this.touchMovePrevPositionInterval) return
      this.touchMovePrevPositionInterval = setInterval(() => {
        // mainTouch 开始了且有初始值
        if (this.mainTouchStarted && this.currentMainTouchPos) {
          console.log("updating mainTouchInitPos", this.currentMainTouchPos)
          this.mainTouchInitPos = this.currentMainTouchPos
        }

        if (this.itemTouchStarted && this.currentItemTouchPos.x) {
          this.initTouch = this.currentItemTouchPos
        }

        // itemTouch
        // if (this.itemTouchStarted && this.currentMainTouchPos)
      }, 200)
    },

    // 滑动开始 , 初始化坐标
    itemTouchstart(e, item) {
      // 以mainTouch 为主, 如果mainTouch 手势已经启动, 那么不执行itemTouch
      if (this.mainTouchstart) {
        // console.log("itemTouchstart, mainTouch running , return ")
        // return
      }
      console.log("itemTouchStart", e, "item:", item)
      e.stopPropagation()
      e.preventDefault()
      this.itemTouchStarted = true
      this.initTouch = {
        x: e.touches[0].pageX,
        y: e.touches[0].pageY,
      }
    },

    // 滑动中同样持续更新保存角度
    itemTouchmove(e, item) {
      console.log("itemTouchMove", e, "item:", item)
      e.stopPropagation()
      e.preventDefault()
      // 错误处理, 初始位置等没有初始化都不进行改变操作直接退出
      if (!this.itemTouchStarted) return

      const currentTouch = {
        x: e.changedTouches[0].pageX,
        y: e.changedTouches[0].pageY,
      }

      this.currentItemTouchPos = currentTouch

      if (
        !this.initTouch.x ||
        !this.initTouch.y ||
        !this.centerPoint.x ||
        !this.centerPoint.y
      ) {
        console.warn("滑动事件获取初始坐标有误...")
        return
      }

      if (!this.currentItemsRadian || this.currentItemsRadian.length < 1) {
        console.warn(
          "滑动事件获取初始位置有误...",
          this.initTouch,
          this.currentItemsRadian
        )
      }

      // e.preventDefault()
      // 滑动太短取消操作
      if (
        Math.abs(currentTouch.x - this.initTouch.x) < 2 ||
        Math.abs(currentTouch.y - this.initTouch.y) < 2
      ) {
        console.log("滑动移动量太小, 取消处理...")
        this.itemTouchStarted = false
        return
      }

      this.itemTouchMovedX = currentTouch.x - this.initTouch.x
      // itemTouchMove 开始, 暂停动画
      this.paused = true

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
        const dist =
          Math.atan2(
            (this.centerPoint.x - currentTouch.x) *
              (this.centerPoint.y - this.initTouch.y) -
              (this.centerPoint.y - currentTouch.y) *
                (this.centerPoint.x - this.initTouch.x),
            (this.centerPoint.x - currentTouch.x) *
              (this.centerPoint.x - this.initTouch.x) +
              (this.centerPoint.y - currentTouch.y) *
                (this.centerPoint.y - this.initTouch.y)
          ) / this.slowTimes
        // let _angle = -dist * (180 / Math.PI)
        // console.log("dist", dist)
        this.currentItemsRadian[i] =
          (this.currentItemsRadian[i] - dist) % (2 * Math.PI)
      }

      // this._onTouchMoveUpdateItemPosition()
    },

    _onTouchMoveUpdateItemPosition() {
      if (!this.currentItemsRadian || this.currentItemsRadian.length < 0) {
        return
      }

      for (let i = 0; i < this.currentItemsRadian.length; i++) {
        const radian = this.currentItemsRadian[i]
        // console.log("radian:", radian)
        this.initPositions[i].x = Math.round(Math.cos(radian) * this.radiusLong)
        this.initPositions[i].y = Math.round(
          Math.sin(radian) * this.radiusShort
        )
        // 手势移动位置后, 判断item是否是active
        if (this.active !== i && this.isActive(this.initPositions[i])) {
          this.active = i
        }
      }

      // console.log("this.currentItemsRadian:", this.currentItemsRadian)
      // console.log("this.initPositions :", this.initPositions)
    },

    // 滑动结束,调用fixedRadians
    itemTouchend(e, item) {
      // console.log("itemTouchEnd", e, "item:", item)
      this.paused = false
      if (!this.itemTouchStarted) return
      e.stopPropagation()
      e.preventDefault()
      this.fixedRadians()
      this.itemTouchStarted = false
      this.itemTouchMovedX = 0
    },

    // 将自动的动画和手势移动时候的角度到位置的移动整合在一起
    animate() {
      const _animate = () => {
        this.animateInterval = setInterval(() => {
          // 自动动画
          if (this.autoAnimate && !this.itemTouchStarted && !this.paused) {
            this._autoMove()
          } else if (this.itemTouchStarted || this.mainTouchStarted) {
            // 根据角度改变来更新位置
            this._onTouchMoveUpdateItemPosition()
          }
        }, 20)
      }
      _animate()
    },

    _autoMove() {
      const count = this.items.length
      let theta = this.currentItemsRadian
        ? this.currentItemsRadian
        : this.initRadian(count)
      // 移动弧长
      let move = Math.PI / (2 * this.slowTimesForAutoSpin)

      let that = this
      theta.forEach((item, i, arr) => {
        arr[i] =
          that.direction === "toRight"
            ? (item + move) % (2 * Math.PI)
            : (item - move) % (2 * Math.PI)
        that.initPositions[i].x = Math.round(
          Math.cos(theta[i]) * that.radiusLong
        )
        that.initPositions[i].y = Math.round(
          Math.sin(theta[i]) * that.radiusShort
        )
        // 判断是否到达中间位置
        if (that.active !== i && that.isActive(that.initPositions[i])) {
          that.active = i
          // console.log("active:", that.active)
        }
      })

      // 实时保留角度
      that.currentItemsRadian = theta
    },

    // 根据传递的item position 判断是否到达active中间位置
    // todo better way
    isActive(itemPosition) {
      return (
        itemPosition.x - this.activePosition.x < 50 &&
        itemPosition.y - this.activePosition.y < 5
      )
    },

    clear() {
      clearInterval(this.animateInterval)
      this.animateInterval = null
      clearInterval(this.touchMovePrevPositionInterval)
      this.touchMovePrevPositionInterval = null
    },

    // 弹窗关闭
    closeModal() {
      this.modalShow = false
    },

    // 打开弹窗
    showModal() {
      this.modalShow = true
    },

    clickModalContent(e) {
      e.stopPropagation()
      e.preventDefault()
    },

    tapButton(direction) {
      console.log("tapButton direction", direction)
      const _step = (2 * Math.PI) / this.items.length
      console.log(
        "step",
        _step,
        this.currentItemsRadian[0] - this.currentItemsRadian[1]
      )
      // for (let i = 0; i < this.currentItemsRadian.length; i++) {
      //   if (direction === "left") {
      //     this.currentItemsRadian[i] -= _step
      //   } else if (direction === "right") {
      //     this.currentItemsRadian[i] += _step
      //   }
      // }
      if (direction === "left") {
        const _r = this.currentItemsRadian.pop()
        this.currentItemsRadian.unshift(_r)
      } else {
        const _r = this.currentItemsRadian.shift()
        this.currentItemsRadian.push(_r)
      }
      this._onTouchMoveUpdateItemPosition()
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
// 底部定位, 底部内容区域高度
$bottom-box-height: 80px;

// 底部按钮定位, 按钮位置根据圆半径来定位
$radianLong: 500px;
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
  // background-position: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
  position: relative;

  .top {
    height: 100%;
    // height: auto;
    width: 70%;
    // border: solid 1px red;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    justify-content: start;
    align-items: center;
    .stage {
      position: relative;
      width: 400px;
      height: 200px;
      margin: 10% auto 0;
      // border: dashed 3px $orange-100;
      display: flex;
      justify-content: center;
      align-items: center;
      // overflow: hidden;
    }
  }

  .control {
    height: 100%;
    width: 100vw;
    // border: solid 1px yellow;
    // overflow: hidden;
    display: flex;
    flex-direction: column;
    justify-content: end;
    align-items: center;

    .ring {
      position: relative;
      width: 200px;
      height: 200px;
      // 显示ring border  调整底部可见位置
      // border: solid 3px $orange-100;
      transform: translateY(50%);
      margin-bottom: $bottom-box-height;
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
          border: solid 1px red;
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
          width: 80px;
          height: 80px;
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
          width: 100px !important;
          height: 110px !important;
          transition-property: width, height;
          // transition-duration: $duration-long;
          // transform: scale(1.1);
        }
        .leftBottom {
          width: 50px !important;
          height: 50px !important;
          transition-property: width, height;
          // transform: skew(-10deg) rotateY(10deg);
          // transition-duration: $duration-short;
        }
        .leftBottom-left {
          width: 40px !important;
          height: 40px !important;
          // transform: skew(-15deg) rotateY(15deg);
          transition-property: width, height;
        }
        .leftBottom-right {
          width: 60px !important;
          height: 60px !important;
          // transform: skew(-5deg) rotateY(5deg);
          transition-property: width, height;
          // transition-delay: 0.2s;
          // transition-duration: $duration-short;
        }
        .rightBottom {
          width: 50px !important;
          height: 50px !important;
          transition-property: width, height;
          // transform: skew(10deg) rotateY(-10deg);
          // transition-duration: $duration-short;
        }
        .rightBottom-left {
          width: 60px !important;
          height: 60px !important;
          transition-property: width, height;
          // transform: skew(5deg) rotateY(-5deg);
          // transition-delay: $duration-short;
          // transition
        }
        .rightBottom-right {
          width: 40px !important;
          height: 40px !important;
          transition-property: width, height;
          // transform: skew(15deg) rotateY(-15deg);
          // transition-delay: $duration-short;
          // transition
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
  .bottom-box {
    position: absolute;
    // overflow: hidden;
    left: 0;
    bottom: 0;
    width: 100%;
    height: $bottom-box-height;
    background: rgba(0, 0, 0, 0.8);
    // display: grid;
    // place-items: center;
    color: white;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;

    .bottom-box-center {
      position: relative;
      height: 100%;
      // border: solid 1px red;
      width: 0;
      height: 0;
      .a-button {
        margin-top: -20px;
      }

      .left-button {
        position: absolute;
        right: 500px;
        transform: translateX(50%);
        // margin-left: 50px;
        border: solid 50px transparent;
        width: 0;
        height: 0;
        border-top: 30px solid #38bdf8;
        // transform: skew(20deg) rotateY(45deg);
      }

      .right-button {
        position: absolute;
        transform: translateX(-50%);
        left: 500px;
        // margin-right: 50px;
        border: solid 50px transparent;
        width: 0;
        height: 0;
        border-top: 30px solid #38bdf8;
        // transform: skew(20deg) rotateY(45deg);
      }
    }
  }
}
</style>
