<template>
  <view class="main">
    <view class="top">
      <view class="stage">
        <image mode="scaleToFill" src="https://images.unsplash.com/photo-1614642264762-d0a3b8bf3700?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80" />
      </view>
    </view>
    <view class="control">
      <view class="ring">
        <view class="centerPoint">
          <view v-for="(item, index) in items" :key="index" :class="['item', active === index ? 'active' : '']" :style="{ left: `${initPositions[index].x}px`, top: `${initPositions[index].y}px` }"> {{ item.name }} </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: 'Hello',
      active: 0,
      initPositions: [],
      items: [
        { index: 0, name: 'item1', image: 'https://images.unsplash.com/photo-1614642264762-d0a3b8bf3700?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 1, name: 'item2', image: 'https://images.unsplash.com/photo-1589225529399-8705282f98e2?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 2, name: 'item3', image: 'https://images.unsplash.com/photo-1590647030647-0bc9c30cc07e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 3, name: 'item4', image: 'https://images.unsplash.com/photo-1592029383200-73fb26a5b925?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 4, name: 'item5', image: 'https://images.unsplash.com/photo-1511553677255-ba939e5537e0?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=928&q=80' },
        { index: 5, name: 'item6', image: 'https://images.unsplash.com/photo-1503939313441-d753b6c7eb91?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 6, name: 'item7', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 7, name: 'item8', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 8, name: 'item9', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 9, name: 'item10', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 10, name: 'item11', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 11, name: 'item12', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 12, name: 'item13', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
        { index: 13, name: 'item14', image: 'https://images.unsplash.com/photo-1590821695525-1e86ef70a7ee?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=880&q=80' },
      ],
    }
  },

  onLoad() {},
  onUnload() {
    console.log('onUnload')
    this.clear()
  },

  created() {
    this.initPositions = this.initEllipse(this.items.length)
    this.animate(this.items.length)
  },
  methods: {
    initEllipse(count, radiusLong = 300, radiusShort = 80) {
      let result = []
      // 测试角度
      let theta = [0, Math.PI / 3, (2 * Math.PI) / 3, Math.PI, Math.PI + Math.PI / 3, Math.PI + (2 * Math.PI) / 3]

      let averageAngle = 360 / count
      let _theta = []
      for (let i = 0; i < count; i++) {
        _theta.push((i * averageAngle * Math.PI) / 180)
      }

      theta = _theta

      console.log(theta)

      for (let i = 0; i < count; i++) {
        let x = Math.round(Math.cos(theta[i]) * radiusLong)
        let y = Math.round(Math.sin(theta[i]) * radiusShort)
        result.push({
          x,
          y,
        })
      }

      return result
    },

    animate(count, radiusLong = 300, radiusShort = 80) {
      let theta = [0, Math.PI / 3, (2 * Math.PI) / 3, Math.PI, Math.PI + Math.PI / 3, Math.PI + (2 * Math.PI) / 3]

      let averageAngle = 360 / count
      let _theta = []
      for (let i = 0; i < count; i++) {
        _theta.push((i * averageAngle * Math.PI) / 180)
      }

      theta = _theta
      // 移动弧长
      let move = Math.PI / 60
      console.log('theta:', theta)

      const that = this

      const _animate = function () {
        that.animateInterval = setInterval(() => {
          theta.forEach((item, i, arr) => {
            arr[i] = (item - move) % (2 * Math.PI)
            that.initPositions[i].x = Math.round(Math.cos(theta[i]) * radiusLong)
            that.initPositions[i].y = Math.round(Math.sin(theta[i]) * radiusShort)
          })
        }, 200)
      }
      _animate()
    },
    clear() {
      clearInterval(this.animateInterval)
      this.animateInterval = null
    },
  },
}
</script>

<style lang="scss">
* {
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
  display: grid;
  grid-auto-flow: column;
  grid-template-columns: repeat(1, minmax(0, 1fr));
  grid-template-rows: 70% 30%;
  grid-auto-columns: auto;
  // align-items: center;
  // // justify-content: center;
  background-image: url('https://images.unsplash.com/photo-1464802686167-b939a6910659?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1450&q=80');
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
        .item {
          position: absolute;
          width: 50px;
          height: 50px;
          z-index: 2;
          border: solid 1px $yellow-300;
          background-color: white;
          border-radius: 50%;
          display: flex;
          justify-content: center;
          align-items: center;
          font-size: 11px;
          transform: translate(-50%, -50%);
        }
      }
    }
  }
}
</style>
