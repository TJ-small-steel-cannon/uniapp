<template>
	<view class="wrap">
		<view class="key-input">
			<view class="title">输入验证码</view>
			<view class="tips">验证码已发送至 +{{startPhone}}****{{endPhone}}</view>
			<u-code-input :hairline="true" :focus="true" v-model="value" @change="change" @finish="finish"
				:maxlength="maxlength" borderColor="#000" mode="line"></u-code-input>
			<text :class="{ error: error }">验证码错误，请重新输入</text>
			<view class="captcha">
				<text :class="{ noCaptcha: show }" @tap="noCaptcha">收不到验证码点这里</text>
				<text :class="{ regain: !show }">{{ second }}秒后重新获取验证码</text>
			</view>
		</view>
		<u-notify ref="uNotify" message="Hi uView"></u-notify>
		<button class="loginBtn" @click="Login" :disabled="loginBtnState">登入</button>
	</view>
</template>

<script>
	import {
		myRequest
	} from '../../libs/httpRequest';
	export default {
		data() {
			return {
				maxlength: 4,
				value: "",
				second: 10,
				show: false,
				error: false,
				toolsState: true,
				code: "",
				loginBtnState: true,
				startPhone: "",
				endPhone: "",
			};
		},
		computed: {},
		onReady() {
			// 也可以通过主题形式调用，如：
			this.$refs.uNotify.primary('验证码为 : ' + this.code)
			// 关闭 notify
			// this.$refs.uNotify.close()
		},
		onLoad(option) {
			console.log("option", option);
			let phone = option.phone;
			this.startPhone = phone.substring(0, 3);
			this.endPhone = phone.substring(7, phone.length);
			let interval = setInterval(() => {
				this.second--;
				if (this.second <= 0) {
					this.show = true;
					// if (this.value.lenth != 4) {
					// 	this.error = true;
					// }
					this.code = ""
					clearInterval(interval);
				}
			}, 1000);
			for (let i = 0; i < 4; i++) {
				this.code += Math.floor(Math.random() * 10)
			}
		},
		methods: {
			// 收不到验证码选择时的选择
			noCaptcha() {
				let _self = this;
				uni.showActionSheet({
					itemList: ['重新获取验证码'],
					success: function(res) {
						for (let i = 0; i < 4; i++) {
							_self.code += Math.floor(Math.random() * 10)
						}
						_self.second = 10;
						_self.show = false;
						_self.$refs.uNotify.primary('验证码为 : ' + _self.code);
						let interval = setInterval(() => {
							_self.second--;
							if (_self.second <= 0) {
								_self.show = true;
								_self.code = ""
								// if (this.value.lenth != 4) {
								// 	this.error = true;
								// }
								clearInterval(interval);
							}
						}, 1000);
					},
					fail: function(res) {

					}
				});
			},
			// change事件侦听
			change(value) {
				console.log('change', value);
			},
			// 输入完验证码最后一位执行
			finish(value) {
				console.log('finish', value);
				if (value == this.code) {
					this.loginBtnState = false;
					console.log("登录成功~~");
					this.error = false;
				} else {
					console.log("登录失败！！");
					this.error = true;
					this.value = "";
					console.log(value);
				}
			},
			// 判断用户是否是管理员
			whetherIsAdmin() {
				let openid = uni.getStorageSync("openid")
				console.log("执行查询");
				myRequest({
					url: "/houtai/isAdmin?openid=" + openid,
				}).then((res) => {
					uni.setStorage({
						key: "isAdmin",
						data: res.data.data,
						success() {
							uni.navigateBack({
								delta: 1, //返回层数，2则上上页
							})
						}
					})
				})
			},
			Login() {
				let _self = this;
				uni.showLoading({
					'title': '登录中...'
				})
				uni.getUserProfile({
					desc: '用于完善会员资料',
					success: (infoRes) => {
						uni.login({
							provider: 'weixin',
							success: function(loginRes) {
								console.log(loginRes);
								console.log('login code:', loginRes.code);
								uni.request({
									method: 'POST',
									url: "http://39.106.27.4:8080" + "/user/user/login",
									data: {
										code: loginRes.code,
									},
									success: (res) => {
										console.log('get_openid.do:', res);
										if (res.data.code == 1) {
											console.log("登录成功！！！！！！");
											// uni.setStorageSync("openid", res.data.data.openid)
											uni.setStorage({
												key: "openid",
												data: res.data.data.openid,
												success() {
													_self.whetherIsAdmin()
												}
											})
										}
										uni.hideLoading();
										uni.showToast({
											title: '登录成功',
											icon: "success",
											duration: 1500,
										});
									},
									fail(err) {
										console.log("出错了！", err);
									}
								});
							}
						});
					},
					fail(res) {
						console.log('授权失败', res)
						uni.hideLoading()
						uni.showToast({
							title: '授权失败',
							icon: 'error',
							duration: 2000
						})
					}
				})
			}
		}
	};
</script>

<style lang="scss" scoped>
	.wrap {
		padding: 80rpx;
	}

	.box {
		margin: 30rpx 0;
		font-size: 30rpx;
		color: 555;
	}

	.key-input {
		padding: 30rpx 0;

		text {
			display: none;
		}

		.error {
			display: block;
			color: red;
			font-size: 30rpx;
			margin: 20rpx 0;
		}
	}

	.title {
		font-size: 50rpx;
		color: #333;
	}

	.key-input .tips {
		font-size: 30rpx;
		color: #333;
		margin-top: 20rpx;
		margin-bottom: 60rpx;
	}

	.captcha {
		color: #ccc;
		font-size: 30rpx;
		margin-top: 40rpx;

		.noCaptcha {
			display: block;
		}

		.regain {
			display: block;
		}
	}

	.loginBtn {
		background: rgb(249, 174, 61);
		outline: none;
		border: none;
	}
</style>