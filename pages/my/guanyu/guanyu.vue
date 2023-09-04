<template>
	<view>
		<view class="contnner">
			<view class="content">
				<view class="image">
					<image src="/static/index/banner02.png" mode="scaleToFill"></image>
				</view>
				<view class="desc">
					校园帮专注于为全国高校提供最方便快捷的校园生活综合服务平台。我们的理念一-互利共存，共建“开放、合作、共赢”的校园服务产业互联网生态。
					你可以成为个发布者，发布你的需求;也可以做一个接收者，接收任务并完成他人的需求。
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			};
		}
	}
</script>

<style lang="scss" scoped>
	.contnner {
		width: 100%;
		height: 100vh;
		background: #eee;
		padding: 20rpx;
		box-sizing: border-box;

		.content {
			width: 100%;
			.image{
				width: 100%;
				height: 300rpx;
				overflow: hidden;
				padding: 0rpx 15rpx;
				box-sizing: border-box;
				image{
					width: 100%;
					height: 100%;
					border-radius: 25rpx;
				}
			}
			.desc{
				width: 100%;
				background: white;
				border-radius: 25rpx;
				margin-top: 10rpx;
				padding:25rpx 15rpx;
				box-sizing: border-box;
				font-weight: 500;
			}
		}
	}
</style>