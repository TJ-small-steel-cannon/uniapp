<template>
	<view>
		<u-sticky bgColor="#fff">
			<u-tabs :list="list1" lineWidth="60" itemStyle="padding:15px 23px;" @click="handlerRendering" :current="currentTabIndex"></u-tabs>
		</u-sticky>
		<!-- 主体内容 -->
		<view class="content">
			<view class="box" v-for="(item,index) in orderList" :key="item.dingid" v-if="orderList.length>0">
				<view class="title">
					<u--image :showLoading="true"
						:src="item.avatar||'https://schoolhelpv2.oss-cn-beijing.aliyuncs.com/6c21945b-6e60-4c1d-88f1-c7a4364b35d7.png'"
						width="80rpx" height="80rpx" shape="circle"></u--image>
					<view class="title_right">
						<view class="l1">
							<text style="font-size: 30rpx;font-weight:bold;;">{{item.category}}</text>
							<view class="state" :class="{state1:item.dingdanState==1,state2:item.dingdanState==2}">
								{{orderListArr[item.dingdanState]}}
							</view>
						</view>
						<view class="l2">
							<text>{{item.createTime}}</text>
						</view>
					</view>
				</view>
				<view class="desc">
					<view class="kuaidi">
						<text v-if="item.kuaidiType">快递类型:{{orderTypeArr[item.kuaidiType]}}--</text>
						<text v-if="item.kuaidiNum">快递数量:{{item.kuaidiNum}}个--</text>
						<text v-if="item.notes">{{item.notes}}--</text>
						<text v-if="item.desireTime">期望送达:{{desireTimeArr[item.desireTime]}}--</text>
						<text v-if="item.sex">{{sexArr[item.sex]}}</text>
					</view>
					<view class="determine">
						<view v-if="item.dingdanState!=2&&item.isDelete==0">
							<view class="address" v-if="item.category=='快递代取'">
								<text style="color: #14c896;" v-if="item.kuaidiShangJia.title">起 : {{item.kuaidiShangJia.title}}</text>
								<text style="color: #83a0d3;" v-if="item.shouHuoAddress.address">终 : {{item.shouHuoAddress.address}}</text>
								<!-- <text style="color: rgb(177, 150, 231);" v-if="item.startingPoint">租借时长 : {{item.startingPoint}}</text> -->
								<!-- <text style="color: rgb(231, 142, 79);" v-if="item.end">预计交货时间 : {{item.end}}</text> -->
							</view>
							<view class="address" v-else-if="item.category=='跑腿业务'"> 
								<text style="color: #14c896;" v-if="item.startingPoint">起 : {{item.startingPoint}}</text>
								<text style="color: #83a0d3;" v-if="item.end">终 : {{item.end}}</text>
							</view>
							<view class="address" v-else-if="item.category=='租借服务'">
								<!-- <text style="color: #14c896;" v-if="item.kuaidiShangJia.title">起 : {{item.startingPoint}}</text> -->
								<text style="color: #83a0d3;" v-if="item.shouHuoAddress.address">终 : {{item.shouHuoAddress.address}}</text>
								<text style="color: rgb(177, 150, 231);" v-if="item.startingPoint">租借时长 : {{item.startingPoint}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.end">预计交货时间 : {{item.end}}</text>
							</view>
							<view class="address" v-else-if="item.category=='游戏陪玩'">
								<text style="color: rgb(177, 150, 231);" v-if="item.startingPoint">游戏 : {{item.startingPoint}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.end">游戏ID : {{item.end}}</text>
								<text style="color: rgb(231, 66, 209);" v-if="item.notes">备注 : {{item.notes}}</text>
								<text style="color: rgb(133, 231, 182);" v-if="item.kuaidiNum">时间/盘数 : {{item.kuaidiNum}}</text>
							</view>
							<view class="address" v-else-if="item.category=='代扔垃圾'">
								<text style="color: rgb(177, 150, 231);" v-if="item.notes">垃圾大小 : {{item.notes}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.end">取货地址 : {{item.end}}</text>
								<text style="color: rgb(231, 66, 209);" v-if="item.qujianInformation">备注 : {{item.qujianInformation}}</text>
								<text style="color: rgb(133, 231, 182);" v-if="item.kuaidiNum">垃圾数量 : {{item.kuaidiNum}}</text>
							</view>
							<view class="address" v-else-if="item.category=='其他帮助'">
								<text style="color: rgb(177, 150, 231);" v-if="item.notes">备注 : {{item.notes}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.shouHuoAddress.address">收货地址 : {{item.shouHuoAddress.address}}</text>
							</view>
							
						</view>
						<view class="hiidenAddress" style="color: rgb(131, 160, 211);" v-else>
							{{item.isDelete==0?"订单已完成,相关信息已隐藏":"该订单已被管理员隐藏"}}
						</view>
					</view>
				</view>
				<view class="fun" v-if="item.isDelete==0">
					<view class="money">
						金额￥{{item.money}}
					</view>
					<view class="btn" v-if="currentTabIndex!=2&&currentTabIndex!=1&&item.dingdanState==0"
						@click="jiedan(item)">
						接单
					</view>
					<view class="btn" v-if="currentTabIndex==2&&item.dingdanState==1"
						style="background: #f56c6c; color: white;" @click="cancellation(item)">
						取消
					</view>
					<view class="btn" v-if="currentTabIndex==1&&item.dingdanState==0"
						style="background: #f56c6c; color: white; margin-right: 20rpx;" @click="delOrder(item)">
						删除
					</view>
					<view class="btn" v-if="currentTabIndex==1&&item.dingdanState==1"
						style="background: #35af2f; color: white;" @click="complete(item)">
						完成
					</view>

				</view>
			</view>
			<view v-if="orderList.length==0">
				<u-empty mode="order" >
				</u-empty>
			</view>
		</view>
		<u-notify ref="uNotify"></u-notify>
	</view>
</template>

<script>
	import {
		myRequest
	} from '@/libs/httpRequest.js'
	export default {
		data() {
			return {
				show: false,
				password: '',
				list1: [{
					name: '全部订单',
				}, {
					name: '我的订单',
				}, {
					name: '我帮助的',
				}, {
					name: '正在悬赏',
				}, ],
				// 当前tab索引
				currentTabIndex: 0,
				// 订单列表
				orderList: "",
				// 订单状态数组
				orderListArr: ["待帮助", "已接单", "已完成"],
				// 快递类型数组
				orderTypeArr: ["小件", "中件", "大件"],
				// 期望送达时间时间数组
				desireTimeArr: ["不限制", "上午", "下午"],
				// 性别
				sexArr: ["不限制", "男生", "女生"],
				// 订单操作状态按钮
				btnArr: ["接单"],
			}
		},
		onLoad() {
			
		},
		onShow() {
			this.currentTabIndex=0;
			this.getOrders();

		},
		methods: {
			// 接单
			jiedan(item) {
				if (!uni.getStorageSync("openid")) {
					this.$refs.uNotify.warning('您还未登录! 请先登');
					console.log(this.$refs.uNotify);
					return;
				}
				let _self = this;
				console.log("接单的item", item);
				let data = {
					id: item.dingid,
					openid: uni.getStorageSync("openid")
				}
				myRequest({
					url: "/order/receive",
					method: "post",
					data
				}).then((res) => {
					_self.getOrders()
				})
			},
			// 取消接单
			cancellation(item) {
				let _self = this;
				console.log("取消订单item", item);
				myRequest({
					url: "/order/cancel?id=" + item.dingid
				}).then((res) => {
					_self.getOrders()
				})
			},
			// 完成订单
			complete(item) {
				let _self = this;
				console.log("取消订单item", item);
				myRequest({
					url: "/order/complete?id=" + item.dingid
				}).then((res) => {
					_self.getOrders()
				})
			},
			// 删除订单
			delOrder(item) {
				let _self = this;
				console.log("删除订单item", item);
				myRequest({
					url: "/order/revoke?id=" + item.dingid
				}).then((res) => {
					_self.getOrders()
				})
			},
			// 点击tab栏触发的函数
			handlerRendering(e) {
				console.log("TabIndex", e.index);
				this.currentTabIndex = e.index;
				this.getOrders()
			},
			// 获取订单
			getOrders() {
				let _self = this;
				let openid = uni.getStorageSync("openid");
				switch (this.currentTabIndex) {
					case 0:
						myRequest({
							url: "/order/all",
							method: "GET"
						}).then((res) => {
							_self.orderList = res.data.data
						})
						break;
					case 1:
						myRequest({
							url: "/order/my?openid=" + openid,
							method: "GET"
						}).then((res) => {
							_self.orderList = res.data.data
						})
						break;
					case 2:
						myRequest({
							url: "/order/myHelp?openid=" + openid,
							method: "GET"
						}).then((res) => {
							_self.orderList = res.data.data
						})
						break;
					case 3:
						myRequest({
							url: "/order/online",
							method: "GET"
						}).then((res) => {
							_self.orderList = res.data.data
						})
						break;
				}
			},
			onChange(val) {
				if (this.password.length < 6) {
					this.password += val;
				}

				if (this.password.length >= 6) {
					if (this.password == "123456") {
						this.pay();
					} else {
						uni.showLoading({
							title: '支付中'
						})
						setTimeout(() => {
							uni.hideLoading();
							this.password = ""
							uni.showToast({
								icon: 'error',
								title: '密码错误'
							})
						}, 2000);
					}

				}
			},
			onBackspace(e) {
				if (this.password.length > 0) {
					this.password = this.password.substring(0, this.password.length - 1);
				}
			},
			pay() {
				uni.showLoading({
					title: '支付中'
				})

				setTimeout(() => {
					uni.hideLoading();
					this.show = false;
					uni.showToast({
						icon: 'success',
						title: '支付成功'
					})
				}, 2000);
			},
			showPop(flag = true, source) {
				this.password = '';
				this.show = flag;
				if (source == "x") {
					uni.showToast({
						icon: 'error',
						title: '取消支付'
					})
				}
			},
			finish() {
				console.log(11111)
			},
		}
	}
</script>

<style lang="scss" scoped>
	.money {
		width: 100%;
		font-size: 80rpx;
		color: #f1a532;
		text-align: center;
		margin-bottom: 10rpx;
		position: relative;

		.close {
			position: absolute;
			top: 20rpx;
			right: 20rpx;
			line-height: 28rpx;
			font-size: 14rpx;
		}
	}

	.tips {
		color: #f1a532;
		padding: 20rpx 0rpx;
	}

	.abc {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.content {
		width: 100%;
		padding: 20rpx;
		box-sizing: border-box;

		.box {
			width: 100%;
			background: rgb(232, 235, 244);
			padding: 20rpx;
			box-sizing: border-box;
			border-radius: 20rpx;
			margin-top: 20rpx;

			.title {
				width: 100%;
				display: flex;
				align-items: center;
				justify-content: space-between;

				.title_right {
					margin-left: 20rpx;
					flex: auto;
					display: flex;
					flex-direction: column;
					justify-content: center;

					.l1 {
						display: flex;
						align-items: center;
						justify-content: space-between;

						.state {
							color: rgb(20, 200, 150);
							background: rgb(214, 233, 233);
							padding: 10rpx;
							border-radius: 10rpx;
						}

						.state1 {
							color: rgb(66, 142, 200);
							background: rgb(143, 218, 233);
							padding: 10rpx;
							border-radius: 10rpx;
						}

						.state2 {
							color: rgb(225, 157, 56);
							background: rgb(235, 217, 190);
							padding: 10rpx;
							border-radius: 10rpx;
						}
					}

					.l2 {
						font-size: 24rpx;
						color: #bbb;
						padding: 6rpx 0rpx;
					}
				}
			}

			.desc {
				width: 100%;
				padding-left: 100rpx;
				box-sizing: border-box;

				.kuaidi {
					width: 100%;
					// display: flex;
					// flex-wrap: wrap;
				}

				.determine {
					width: 100%;
					display: flex;
					flex-direction: column;

					.address {
						width: 100%;
						display: flex;
						flex-direction: column;

						text {
							padding: 10rpx 0rpx;
						}
					}

					.hiidenAddress {
						padding: 10rpx 0rpx;
					}
				}

			}

			.fun {
				width: 100%;
				display: flex;
				align-items: center;
				justify-content: flex-end;

				.money {
					font-size: 30rpx;
					width: max-content;
					padding: 10rpx 20rpx;
					margin: 0;
					background: rgb(236, 225, 231);
					color: rgb(223, 66, 66);
					float: right;
					margin-right: 20rpx;
					border-radius: 10rpx;
				}

				.btn {
					padding: 15rpx 45rpx;
					background: linear-gradient(rgb(127, 178, 254), rgb(104, 187, 249));
					border-radius: 20rpx;
					color: white;
					font-size: 30rpx;
					float: right;
				}
			}
		}
	}
</style>