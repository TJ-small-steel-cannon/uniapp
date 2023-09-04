<template>
	<view class="container">
		<view class="address" v-if="addressList.length>0">
			<view class="item" v-for="(item,index) in addressList" :key="index" @click="CacheItems(item)">
				<view class="name">姓名: {{item.name}}</view>
				<view class="phone">电话: {{item.phone}}</view>
				<view class="floor">楼号: {{item.floor}}</view>
				<view class="dizhi">地址: {{item.address}}</view>
				<view class="operate">
					<view class="defaultAddress" v-if="item.def">
						<u-icon name="checkmark-circle-fill" color="#0098fa" size="20"></u-icon><text>默认地址</text>
					</view>
					<view class="defaultAddress" v-else @click.stop="settingDefultAddress(item)">
						<u-icon name="checkmark-circle" color="#0098fa" size="20"></u-icon><text>设为默认地址</text>
					</view>
					<view class="btn">
						<view class="edit" @click.stop="gotoAddAddress($event,item)">
							<u-icon name="edit-pen" color="#f56c6c" size="20"></u-icon>
							<text>编辑</text>
						</view>
						<view class="delete" @click.stop="deleteAddressState = true;deleteAddressItem=item">
							<u-icon name="trash" color="#f56c6c" size="20"></u-icon>
							<text>删除</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<view v-else>
			<u-empty mode="address">
			</u-empty>
		</view>
		
		<view class="line">
			<u-divider text="分割线" lineColor="#bbb"></u-divider>
		</view>
		<view style="padding-bottom: 100rpx;">
			<view class="addAddressBtn" @click="gotoAddAddress">
				添加地址
			</view>
		</view>
		<u-modal :show="deleteAddressState" content='确定要删除当前地址？' width="300" showCancelButton=true
			@confirm="deleteAddress" @cancel="deleteAddressState=false;deleteAddressItem=''" closeOnClickOverlay
			@close="deleteAddressState=false;deleteAddressItem=''"></u-modal>
	</view>
</template>

<script>
	import {
		myRequest
	} from "@/libs/httpRequest.js"
	// const cityData = require('./address.json');
	export default {
		data() {
			return {
				// 地址列表
				addressList: [],
				// 删除地址弹框状态
				deleteAddressState: false,
				deleteAddressItem: "",
			};
		},
		onShow() {
			this.getAddressList();
		},
		methods: {
			getAddressList() {
				let _self = this;
				let data = {
					openid: uni.getStorageSync("openid"),
				}
				myRequest({
					url: "/address/all",
					method: "post",
					data
				}).then((res) => {
					_self.addressList = res.data.data
				})
			},
			gotoAddAddress(e, item) {
				// item为当前地址对象 
				let url = "/pages/publicPage/addAddress/addAddress"
				// 当item有值的时候为修改地址带参跳转
				if (item) {
					item = JSON.stringify(item);
					url = "/pages/publicPage/addAddress/addAddress?item=" + item
				}
				uni.navigateTo({
					url,
				})
			},
			deleteAddress() {
				// 发起网络请求
				console.log("要删除的地址", this.deleteAddressItem);
				let _self = this;
				myRequest({
					url: "/address/delete?uuid=" + this.deleteAddressItem.uuid
				}).then((res) => {
					_self.deleteAddressState = false;
					_self.getAddressList();
				})
			},
			settingDefultAddress(item) {
				let _self = this;
				myRequest({
					url: "/address/default?uuid=" + item.uuid,
				}).then((res) => {
					if (res.data.code == 1) {
						_self.getAddressList()
					}
				})
			},
			// 对当前地址进行缓存
			CacheItems(item) {
				console.log(item);
				uni.setStorageSync("CacheItem", item);
				uni.navigateBack({
					delta: 1, //返回层数，2则上上页
				})
			}
		},

	}
</script>

<style lang="scss" scoped>
	.container {
		width: 100%;
		height: 100vh;
		background-color: white;
		padding: 20rpx;
		box-sizing: border-box;

		.address {
			width: 100%;

			.item {
				width: 100%;
				height: 360rpx;
				background-color: rgb(213, 225, 239);
				border-radius: 20rpx;
				box-shadow:
					10px 20px 150px rgba(0, 0, 0, 0.15);
				padding: 30rpx;
				box-sizing: border-box;
				margin-top: 20rpx;

				.dizhi,
				.phone,
				.name,
				.floor {
					padding: 10rpx 0rpx;
				}

				.operate {
					width: 100%;
					margin-top: 20rpx;

					.defaultAddress {
						display: flex;
						align-items: center;
						color: rgb(0, 152, 250);
						float: left;

						text {
							margin-left: 10rpx;
						}

						margin-top: 10rpx;
					}

					.btn {
						display: flex;
						align-items: center;
						float: right;

						.edit {
							display: flex;
							align-items: center;
							justify-content: center;
							margin-right: 10rpx;
							padding: 15rpx;
							padding-right: 25rpx;
							background: white;
							border-radius: 10rpx;

							text {
								margin-left: 10rpx;
							}
						}

						.delete {
							color: #f56c6c;
							display: flex;
							align-items: center;
							justify-content: center;
							margin-left: 10rpx;
							padding: 15rpx;
							padding-right: 25rpx;
							background: white;
							border-radius: 10rpx;

							text {
								margin-left: 10rpx;
							}
						}
					}

				}
			}
		}

		.line {
			width: 100%;
			padding: 50rpx 200rpx;
			box-sizing: border-box;
		}

		.addAddressBtn {
			width: 100%;
			color: white;
			height: 80rpx;
			line-height: 80rpx;
			text-align: center;
			background: rgb(96, 148, 225);
			border-radius: 50rpx;
			font-size: 30rpx;
		}
	}
</style>