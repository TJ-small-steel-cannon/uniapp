<template>
	<view class="container">
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
						<view v-if="item.dingdanState!=2">
							<view class="address" v-if="item.category=='快递代取'">
								<text style="color: #14c896;" v-if="item.kuaidiShangJia.title">起 :
									{{item.kuaidiShangJia.title}}</text>
								<text style="color: #83a0d3;" v-if="item.shouHuoAddress.address">终 :
									{{item.shouHuoAddress.address}}</text>
							</view>
							<view class="address" v-else-if="item.category=='跑腿业务'">
								<text style="color: #14c896;" v-if="item.startingPoint">起 :
									{{item.startingPoint}}</text>
								<text style="color: #83a0d3;" v-if="item.end">终 : {{item.end}}</text>
							</view>
							<view class="address" v-else-if="item.category=='租借服务'">
								<!-- <text style="color: #14c896;" v-if="item.kuaidiShangJia.title">起 : {{item.startingPoint}}</text> -->
								<text style="color: #83a0d3;" v-if="item.shouHuoAddress.address">终 :
									{{item.shouHuoAddress.address}}</text>
								<text style="color: rgb(177, 150, 231);" v-if="item.startingPoint">租借时长 :
									{{item.startingPoint}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.end">预计交货时间 : {{item.end}}</text>
							</view>
							<view class="address" v-else-if="item.category=='游戏陪玩'">
								<text style="color: rgb(177, 150, 231);" v-if="item.startingPoint">游戏 :
									{{item.startingPoint}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.end">游戏ID : {{item.end}}</text>
								<text style="color: rgb(231, 66, 209);" v-if="item.notes">备注 : {{item.notes}}</text>
								<text style="color: rgb(133, 231, 182);" v-if="item.kuaidiNum">时间/盘数 :
									{{item.kuaidiNum}}</text>
							</view>
							<view class="address" v-else-if="item.category=='代扔垃圾'">
								<text style="color: rgb(177, 150, 231);" v-if="item.notes">垃圾大小 : {{item.notes}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.end">取货地址 : {{item.end}}</text>
								<text style="color: rgb(231, 66, 209);" v-if="item.qujianInformation">备注 :
									{{item.qujianInformation}}</text>
								<text style="color: rgb(133, 231, 182);" v-if="item.kuaidiNum">垃圾数量 :
									{{item.kuaidiNum}}</text>
							</view>
							<view class="address" v-else-if="item.category=='其他帮助'">
								<text style="color: rgb(177, 150, 231);" v-if="item.notes">备注 : {{item.notes}}</text>
								<text style="color: rgb(231, 142, 79);" v-if="item.shouHuoAddress.address">收货地址 :
									{{item.shouHuoAddress.address}}</text>
							</view>

						</view>
						<view class="hiidenAddress" style="color: rgb(131, 160, 211);" v-else>
							订单已完成，相关信息已隐藏
						</view>
					</view>
				</view>
				<view class="fun">
					<view class="money">
						金额￥{{item.money}}
					</view>
					<view class="showbtn" @click="updateOrderState(item)" v-if="item.isDelete==1">
						显示订单
					</view>
					<view class="hidbtn" @click="updateOrderState(item)" v-else>
						隐藏订单
					</view>
					
				</view>
			</view>
			<view v-if="orderList.length==0">
				<u-empty mode="order">
				</u-empty>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		myRequest
	} from '@/libs/httpRequest.js'
	export default {
		data() {
			return {
				// 全部订单
				orderList: [],
				orderListArr: ["待帮助", "已接单", "已完成"],
			};
		},
		onShow() {
			this.getOrders();
		},
		methods: {
			getOrders() {
				let _self = this;
				let openid = uni.getStorageSync("openid");
				myRequest({
					url: "/order/all",
					method: "GET"
				}).then((res) => {
					_self.orderList = res.data.data
				})
			},
			updateOrderState(item){
				let _self = this;
				let openid = uni.getStorageSync("openid");
				myRequest({
					url: "/order/isDelete?id="+item.dingid,
				}).then((res) => {
					if(res.data.code==1){
						_self.getOrders()
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		width: 100%;
		height: 100vh;
		padding: 20rpx;
		box-sizing: border-box;

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

					.showbtn {
						padding: 15rpx 45rpx;
						background: linear-gradient(rgb(127, 178, 254), rgb(104, 187, 249));
						border-radius: 20rpx;
						color: white;
						font-size: 30rpx;
						float: right;
					}
					.hidbtn{
						padding: 15rpx 45rpx;
						background: linear-gradient(rgb(254, 47, 47), rgb(249, 155, 123));
						border-radius: 20rpx;
						color: white;
						font-size: 30rpx;
						float: right;
						margin-left: 10rpx;
					}
				}
			}
		}
	}
</style>