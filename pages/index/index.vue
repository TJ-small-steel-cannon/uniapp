<template>
	<view class="content">
		<!-- 轮播图 -->
		<view class="Banner">
			<u-swiper :list="bannerList" indicator indicatorMode="dot" circular
				easingFunction="easeInOutCubic"></u-swiper>
		</view>
		<!-- 主题结构 -->
		<view class="main">
			<!-- 滚动通知 -->
			<view class="NoticeBar">
				<u-notice-bar :text="noticeData" mode="closable"></u-notice-bar>
			</view>
			<!-- 功能模块 -->
			<view class="FunModule">
				<u-grid :border="false" @click="selectModule">
					<u-grid-item v-for="(baseListItem,baseListIndex) in baseList" :key="baseListIndex">
						<view class="FunModule_item">
							<view class="wai">
								<view class="nei">
									<image class="icon" :src="baseListItem.image" mode="scaleToFill"></image>
								</view>
							</view>
							<view class="desc">
								{{baseListItem.desc}}
							</view>
						</view>
					</u-grid-item>
				</u-grid>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 轮播图数据
				bannerList: [
					'/static/index/banner02.png',
					'/static/index/banner03.jpg', 
					'/static/index/banner01.png',
				],
				// 滚动通知数据
				noticeData: '校园帮小程序上线啦！共同维护好我们朝夕相处的校园。',
				// 功能模块数据
				baseList: [{
						image: "../../static/index/快递代取.png",
						desc: "快递代取",
					},
					{
						image: "../../static/index/跑腿.png",
						desc: "校园跑腿",
					},
					{
						image: "../../static/index/物品租借.png",
						desc: "租借服务",
					},
					{
						image: "../../static/index/游戏.png",
						desc: "游戏陪玩",
					},
					{
						image: "../../static/index/垃圾筒.png",
						desc: "代扔垃圾",
					},
					{
						image: "../../static/index/其他服务业.png",
						desc: "其他帮助",
					},

				]
			}
		},
		onLoad() {

		},
		methods: {
			// 功能模块选中的索引
			selectModule(index) {
				console.log(index);
				switch (index) {
					case 0:
						uni.navigateTo({
							url: "/pages/index/kuaidi/kuaidi"
						});
						break;
					case 1:
						uni.navigateTo({
							url: "/pages/index/paotui/paotui"
						});
						break;
					case 2:
						uni.navigateTo({
							url: "/pages/index/zujie/zujie"
						});
						break;
					case 3:
						uni.navigateTo({
							url: "/pages/index/peiwan/peiwan"
						});
						break;
					case 4:
						uni.navigateTo({
							url: "/pages/index/laji/laji"
						});
						break;
					case 5:
						uni.navigateTo({
							url: "/pages/index/qita/qita"
						});
						break;
				}
			},
		}
	}
</script>

<style lang="scss" scoped>
	.content {
		width: 100%;
		height: 100vh;
		background: rgb(247, 248, 247);

		// 主题结构样式
		.main {
			width: 100%;
			box-sizing: border-box;
			padding: 0rpx 20rpx;

			// 滚动通知样式
			.NoticeBar {
				width: 100%;
				margin-top: 10rpx;
				margin-bottom: 20rpx;
				border-radius: 30rpx;
				overflow: hidden;
			}

			// 功能模块样式
			.FunModule {
				.FunModule_item {
					width: 80%;
					height: 150rpx;
					background-color: white;
					margin-top: 80rpx;
					border-radius: 25rpx;

					.wai {
						width: 100rpx;
						height: 100rpx;
						border-radius: 50rpx;
						background-color: white;
						margin: 0 auto;
						margin-top: -50rpx;
						box-shadow:
							10px 20px 25px rgba(0, 0, 0, 0.15);
						display: flex;
						align-items: center;
						justify-content: center;

						.nei {
							width: 90rpx;
							height: 90rpx;
							border-radius: 50rpx;
							display: flex;
							align-items: center;
							justify-content: center;
							background: #6490e6;
						}

						.icon {
							width: 50rpx;
							height: 50rpx;
							display: block;
							margin: 0 auto;
						}
					}
				}

				.desc {
					color: #6490e6;
					text-align: center;
					font-size: 35rpx;
					margin-top: 40rpx;
				}
			}
		}
	}
</style>