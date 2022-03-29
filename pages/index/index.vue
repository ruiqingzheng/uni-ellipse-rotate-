<template>
  <view class="main">
    <view class="top"></view>
    <view class="control"></view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: 'Hello',
    }
  },
  onLoad() {},
  methods: {},
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
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
  }

  .control {
    height: 70%;
    width: 100%;
    border: solid 2px white;
  }
}
</style>
