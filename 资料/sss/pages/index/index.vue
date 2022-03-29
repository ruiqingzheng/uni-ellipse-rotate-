<template>
	<view class="content">
		<view class="mask" v-if="show">
			<view>弹窗内容</view>
			<view  style="float:right;margin-right:20px"  @click="close">关闭弹窗</view>
		</view>
		<view class="top">
			顶部区域内容xxxx
			<view>轮播选中对应的索引--{{index}}</view>
			<view class="btn" @click="open">按钮</view>
		</view>
		<view class="middle">
			中间轮播区域
		</view>
		<view class="foot">底部</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				show: false,
				index:0
			}
		},
		onLoad() {

		},
		methods: {
			open() {
				this.show = true
			},
			close(){
				this.show = false
			}
		}
	}
</script>

<style>
	.mask {
		position: absolute;
		top: 50%;
		left: 50%;
		width: 80%;
		height: 80%;
		background: #fff;
		transform: translate(-50%, -50%);
	}

	.content {
		background: yellow;
		height: 100vh;
		display: flex;
		flex-direction: column;
	}

	.btn {
		width: 100px;
		height: 40px;
		line-height: 40px;
		background-color: #DD524D;
		margin: auto;
		text-align: center;
	}

	.top {
		height: 200px;
		background-color: #4CD964;
	}

	.middle {
		flex: 1;
		background: #007AFF
	}

	.foot {
		width: 100%;
		line-height: 40px;
		height: 40px;
		text-align: center;
		background-color: pink;
	}
</style>
