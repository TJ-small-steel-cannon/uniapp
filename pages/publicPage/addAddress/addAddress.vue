<template>
	<view class="container">
		<view class="publicStyle">
			<view class="main">
				<view class="box">
					<view class="title">
						<u-icon name="home" color="#0098fa" size="25"></u-icon>
						<text>楼栋</text>
					</view>
					<view class="desc">
						<u-input inputAlign="right" type="text" placeholder="请输入楼号" border="none"
							v-model="louhao"></u-input>
					</view>
				</view>
				<u-line></u-line>
				<view class="box">
					<view class="title">
						<u-icon name="bell" color="#fa6d5a" size="25"></u-icon>
						<text>地址</text>
					</view>
					<view class="desc">
						<u-input inputAlign="right" type="text" placeholder="请输入详细地址" border="none"
							v-model="address"></u-input>
					</view>
				</view>
				<u-line></u-line>
				<view class="box">
					<view class="title">
						<u-icon name="account" color="#fad5bb" size="25"></u-icon>
						<text>姓名</text>
					</view>
					<view class="desc">
						<u-input inputAlign="right" type="text" placeholder="请输入姓名" border="none"
							v-model="name"></u-input>
					</view>
				</view>
				<u-line></u-line>
				<view class="box">
					<view class="title">
						<u-icon name="phone" color="#65a867" size="25"></u-icon>
						<text>电话</text>
					</view>
					<view class="desc">
						<u-input inputAlign="right" type="text" placeholder="请输入电话" border="none"
							v-model="phone"></u-input>
					</view>
				</view>
				<u-line></u-line>
			</view>
		</view>
		<view class="publicStyle">
			<view class="main">
				<view class="box">
					<view class="title">
						<u-icon name="map" color="#fa6d5a" size="25"></u-icon>
						<text>默认地址</text>
					</view>
					<view class="desc">
						<u-switch v-model="defaultAddress"></u-switch>
					</view>
				</view>
				<u-line></u-line>
				<view class="box" @click="addressDescriptionState=true">
					<view class="title">
						<u-icon name="bell" color="#fa6d5a" size="25"></u-icon>
						<text>地址说明</text>
					</view>
					<view class="desc">
						<u-text text=">" color="#bbb"></u-text>
					</view>
				</view>
				<u-line></u-line>
			</view>
		</view>

		<view class="addAddressBtn" @click="updateAddress" v-if="updateAddressItem">
			修改地址
		</view>
		<view class="addAddressBtn" @click="saveAddress" v-else>
			保存地址
		</view>

		<u-modal width="300px" :show="addressDescriptionState" title="地址说明" :content='addressDescriptionContent'
			@confirm="addressDescriptionState=false" closeOnClickOverlay
			@close="addressDescriptionState=false"></u-modal>
	</view>
</template>

<script>
	import {
		myRequest
	} from "@/libs/httpRequest.js"
	export default {
		data() {
			return {
				// 楼栋号
				louhao: "",
				// 门牌号
				address: "",
				// 姓名
				name: "",
				// 电话
				phone: "",
				// 是否默认地址
				defaultAddress: true,
				// 地址说明弹框显示状态
				addressDescriptionState: false,
				// 地址说明数据
				addressDescriptionContent: "啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊",
				updateAddressItem: "",
			}
		},
		onLoad(options) {
			if (options.item) {
				console.log("修改的地址为", JSON.parse(options.item));
				this.updateAddressItem = JSON.parse(options.item)
				this.louhao = JSON.parse(options.item).floor
				this.address = JSON.parse(options.item).address
				this.name = JSON.parse(options.item).name
				this.phone = JSON.parse(options.item).phone
				this.defaultAddress = JSON.parse(options.item).def
			}
		},
		methods: {
			// 保存地址发起网络请求
			saveAddress() {
				let _self = this;
				let data = {
					address: this.address,
					def: this.defaultAddress,
					floor: this.louhao,
					name: this.name,
					openid: uni.getStorageSync("openid"),
					phone: this.phone

				}
				myRequest({
					url: "/address/add",
					method: "post",
					data
				}).then((res) => {
					if (res.data.code == 1) {
						console.log("保存成功");
						uni.showToast({
							title: '添加地址成功',
							icon: 'success', //如果要纯文本，不要icon，将值设为'none'
							duration: 1000 //持续时间为 2秒
						})
						uni.navigateBack({
							delta: 1, //返回层数，2则上上页
						})
					}
				})

			},
			updateAddress() {
				let _self = this;
				let data = {
					address: this.address,
					def: this.defaultAddress,
					floor: this.louhao,
					name: this.name,
					openid: uni.getStorageSync("openid"),
					phone: this.phone,
					uuid: this.updateAddressItem.uuid
				}
				myRequest({
					url: "/address/update",
					method: "post",
					data
				}).then((res) => {
					if (res.data.code == 1) {
						console.log("修改成功");
						uni.showToast({
							title: '修改地址成功',
							icon: 'success', //如果要纯文本，不要icon，将值设为'none'
							duration: 1000 //持续时间为 2秒
						})
						uni.navigateBack({
							delta: 1, //返回层数，2则上上页
						})
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
		box-sizing: border-box;
		padding: 20rpx;
		background: rgb(247, 248, 247);

		.publicStyle {
			width: 100%;
			background: white;
			border-radius: 25rpx;
			box-shadow:
				10px 20px 50px rgba(0, 0, 0, 0.15);
			margin-top: 20rpx;

			.main {
				width: 100%;
				box-sizing: border-box;
				padding: 20rpx;
				padding-top: 0rpx;

				.box {
					width: 100%;
					padding: 20rpx 30rpx;
					box-sizing: border-box;
					display: flex;
					align-items: center;
					justify-content: space-between;

					.title {
						display: flex;
						align-items: center;

						text {
							margin-left: 10rpx;
						}
					}

					.desc {}
				}
			}

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
			margin-top: 50rpx;
		}
	}
</style>