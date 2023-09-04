<template>
	<view class="container">
		<view class="public">
			<view class="desc">
				快递点
			</view>
		</view>
		<view class="public" style="margin-top: 30rpx;">
			<view class="item" v-for="(item,index) in list" @click="CacheShangJia(item)">
				{{item.title}}<text>{{item.desc}}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
					title: "菜鸟驿站",
					desc: "-正大门"
				},{
					title: "顺丰快递",
					desc: "-原乐收快递-正大门"
				}]
			}
		},
		methods: {
			CacheShangJia(item) {
				uni.setStorageSync("CacheShangJia", item);
				uni.navigateBack({
					delta: 1, //返回层数，2则上上页
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		width: 100%;
		height: 100vh;
		background: rgb(243, 243, 243);

		.public {
			width: 100%;
			padding: 0rpx 30rpx;
			background: white;
			box-sizing: border-box;

			.desc {
				font-weight: 800;
				padding: 15rpx 0rpx;
				border-bottom: 2px solid black;
				width: max-content;
				box-sizing: border-box;
			}

			.item {
				width: 100%;
				border-bottom: 2px solid #ccc;
				padding: 25rpx 0rpx;
				box-sizing: border-box;

				text {
					color: #ccc;
				}
			}
		}
	}
</style>