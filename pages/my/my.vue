<template>
	<view class="wrap">
		<view v-if="!isLogin">
			<view class="top"></view>
			<view class="content">
				<view class="title">欢迎登录校园帮</view>
				<input class="u-border-bottom" type="number" v-model="tel" placeholder="请输入手机号" />
				<view class="tips">未注册的手机号验证后自动创建校园帮账号</view>
				<button @tap="submit" :style="[inputStyle]" class="getCaptcha">获取短信验证码</button>
				<view class="alternative">
					<!-- <view class="password">密码登录</view> -->
					<view class="issue">遇到问题</view>
				</view>
			</view>
			<view class="buttom">
				<view class="loginType">
					<view class="wechat item" @click="Login">
						<view class="icon"><u-icon size="70" name="weixin-fill" color="rgb(83,194,64)"></u-icon></view>
						微信
					</view>
					<!-- <view class="QQ item">
						<view class="icon"><u-icon size="70" name="qq-fill" color="rgb(17,183,233)"></u-icon></view>
						QQ
					</view> -->
				</view>
				<view class="hint">
					登录代表同意
					<text class="link">校园帮用户协议、隐私政策，</text>
					并授权使用您的校园帮账号信息（如昵称、头像、收获地址）以便您统一管理
				</view>
			</view>
		</view>
		<!-- 我的页面主体代码 -->
		<view class="main" v-else>
			<view class="content_image">
				<image :src="imgBG" mode="aspectFill" @click="changImg"></image>
			</view>
			<!-- 内容 -->
			<view class="content_botton">
				<view class="content_info">

					<button class="user_profile" open-type="chooseAvatar" @chooseavatar="setUserAvatar" plain="true">
						<image :src="userinfo.avatar" mode="scaleToFill"></image>
					</button>

					<u-icon :name="userSex.icon" :color="userSex.color" size="25"
						@click="userSexShowState=true"></u-icon>

					<view class="username">
						<input type="nickname" maxlength="10" v-model="userinfo.name"
							@click="nameInputBorderBottomStyle=true" @blur="setNickName"
							:style="{borderBottom:(nameInputBorderBottomStyle?'1px solid black':none)}">
					</view>
					<view class="select">
						<view>我的订单数 ( <u-count-to :startVal="100" :endVal="userinfo.total" fontSize="15"></u-count-to> )
						</view>
						<view @click="myCollect">我帮助的订单 ({{userinfo.myhelp}})</view>
					</view>
				</view>
				<view class="my_order">
					<view class="my_order_title">
						订单管理
					</view>
					<view class="my_order_item" v-for="item in list" @click="gotoDingDanPage">
						<view class="my_order_content">
							<image :src="item.url" mode="widthFix"></image>
							<view class="">{{item.name}}</view>
						</view>
					</view>
				</view>
				<!--  -->
				<view class="other">
					<!-- 我的地址 -->
					<view class="box" @click="gotoMyAddress">
						<view class="title">
							<u-icon name="map" size="20" color="#7594d0"></u-icon>
							<text>我的地址</text>
						</view>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</view>
					<u-line color="#969696"></u-line>
					<!-- 官方投诉 -->
					<view class="box" @click="gotoTouSu">
						<view class="title">
							<u-icon name="kefu-ermai" size="20" color="#6c3273"></u-icon>
							<text>官方投诉</text>
						</view>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</view>
					<u-line color="#969696"></u-line>
					<!-- 关于我们 -->
					<view class="box" @click="gotoGuanYu">
						<view class="title">
							<u-icon name="question-circle" size="20" color="#3bc4a4"></u-icon>
							<text>关于我们</text>
						</view>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</view>
					<u-line color="#969696"></u-line>
					<!-- 管理端 -->
					<view class="box" v-if="isAdmin" @click="gotoAdminPage">
						<view class="title">
							<u-icon name="grid-fill" size="20" color="#d0301b"></u-icon>
							<text>管理页面</text>
						</view>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</view>
					<u-line color="#969696" v-if="isAdmin"></u-line>
					<!-- 退出登陆 -->
					<view class="box" @click="LogoutTools=true">
						<view class="title">
							<u-icon name="lock" size="20" color="#d0301b"></u-icon>
							<text>退出登陆</text>
						</view>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</view>
				</view>
			</view>
		</view>
		<!-- tools -->
		<u-modal showCancelButton :show="LogoutTools" title='确定要退出登录？' @confirm="Logout"
			@cancel="LogoutTools=false"></u-modal>
		<u-picker :show="userSexShowState" :columns="userSexArr" closeOnClickOverlay @confirm="setUserSex"
			@cancel="userSexShowState=false" @close="userSexShowState=false"></u-picker>
	</view>
</template>

<script>
	import {
		myRequest
	} from '../../libs/httpRequest';
	export default {
		data() {
			return {
				tel: '',
				isLogin: false,
				list: [{
					id: 1,
					url: '../../static/my/order.png',
					name: "全部订单"
				}, ],
				// 用户背景
				imgBG: uni.getStorageSync('imgBG') ||
					'https://schoolhelpv2.oss-cn-beijing.aliyuncs.com/b2042e61-7e66-41aa-9b78-6b1110594c1c.jpg',
				// 用户信息
				userinfo: {},
				userSex: {
					icon: "man",
					color: "#44aee2"
				},
				// 退出登陆弹窗
				LogoutTools: false,
				// 姓名输入框下边框是否显示
				nameInputBorderBottomStyle: false,
				// 是否显示选择性别
				userSexShowState: false,
				// 性别数组
				userSexArr: [
					["男", "女"]
				],
				// 是否是管理员
				isAdmin: uni.getStorageSync("isAdmin") || false,
			}
		},
		computed: {
			inputStyle() {
				let style = {};
				if (this.tel.length == 11) {
					style.color = "#fff";
					style.backgroundColor = this.$u.color['warning'];
				}
				return style;
			}
		},
		onShow() {
			this.isAdmin=uni.getStorageSync("isAdmin") || false;
			console.log("isAdmin",this.isAdmin);
			let _self = this;
			let openid = uni.getStorageSync("openid");
			if (openid) {
				// 隐藏登录模块 显示我的页面主体部分
				this.isLogin = true;
				// 获取用户信息
				this.getUserInfo();
			}
			console.log("show");


		},
		methods: {
			// 跳转到管理页面
			gotoAdminPage() {
				uni.navigateTo({
					url: "/pages/my/AdminPage/AdminPage",
				})
			},
			// 修改用户性别
			setUserSex(e) {
				this.userSexShowState = false;
				console.log(e.indexs[0] + 1);
				this.updateUserInfo(null, null, e.indexs[0] + 1);
			},
			// 修改用户头像
			setUserAvatar(e) {
				this.updateUserInfo(e.detail.avatarUrl, null, null);
			},
			// 修改用户信息
			updateUserInfo(img, name, sex) {
				let _self = this;
				let openid = uni.getStorageSync("openid");
				let data = {
					openid,
					avatar: img,
					name: name,
					sex: sex,
				};
				myRequest({
					url: "/common/avatar",
					method: "post",
					data
				}).then((res) => {
					if (res.data.code == 1) {
						_self.getUserInfo()
					}
				})
			},
			// 修改名称
			setNickName(e) {
				console.log(e.detail.value);
				this.userinfo.name = e.detail.value;
				setTimeout(() => {
					console.log(e.detail.value);
				}, 1000)
				this.nameInputBorderBottomStyle = false;
				this.updateUserInfo(null, e.detail.value, null);
			},
			// 跳转到订单页面
			gotoDingDanPage() {
				uni.switchTab({
					url: "/pages/dingdan/dingdan?currentTabIndex=0",
				})
			},
			// 跳转到我的地址
			gotoMyAddress() {
				uni.navigateTo({
					url: "/pages/publicPage/address/address",
				})
			},
			// 跳转到官方投诉
			gotoTouSu() {
				uni.navigateTo({
					url: "/pages/my/tousu/tousu",
				})
			},
			// 跳转到关于我们
			gotoGuanYu() {
				uni.navigateTo({
					url: "/pages/my/guanyu/guanyu",
				})
			},
			// 获取用户信息
			getUserInfo() {
				let _self = this;
				let openid = uni.getStorageSync("openid");
				if (openid) {
					// 获取用户信息
					myRequest({
						url: "/common/user",
						method: "post",
						data: {
							"openid": openid
						}
					}).then((res) => {
						console.log("userInfo", res.data.data);
						_self.userinfo = res.data.data;
						uni.setStorageSync("userInfo", res.data.data);
						_self.nickName = res.data.data.name;
						if (res.data.data.sex == 1) {
							_self.userSex = {
								icon: "man",
								color: "#44aee2"
							}
						} else {
							_self.userSex = {
								icon: "woman",
								color: "#be1134"
							}
						}
					})
				}
			},
			// 判断用户是否是管理员
			whetherIsAdmin() {
				let openid = uni.getStorageSync("openid")
				let _self = this;
				myRequest({
					url: "/houtai/isAdmin?openid=" + openid,
				}).then((res) => {
					_self.isAdmin = res.data.data
					uni.setStorageSync("isAdmin",res.data.data);
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
								myRequest({
									url: "/user/user/login",
									method: 'POST',
									data: {
										code: loginRes.code,
									}
								}).then((res) => {
									if (res.data.code == 1) {
										console.log("登录成功！！！！！！");
										uni.setStorageSync("openid", res.data.data
											.openid);
										uni.showToast({
											title: '登录成功',
											icon: "success",
											duration: 1500,
										});
										_self.isLogin = true;
										uni.hideLoading();
										_self.whetherIsAdmin()
										return myRequest({
											url: "/common/user",
											method: "post",
											data: {
												"openid": res.data.data.openid
											}
										})
									}
									uni.hideLoading();
								}).then((res) => {
									console.log("userInfo", res.data.data);
									_self.userinfo = res.data.data;
									uni.setStorageSync("userInfo", res.data.data);
								})
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
			},
			// 退出登录
			Logout() {
				uni.clearStorageSync();
				this.isLogin = false;
				this.LogoutTools = false;
				this.tel="";
			},
			submit() {
				if (this.$u.test.mobile(this.tel)) {
					this.$u.route({
						url: 'pages/my/code?phone=' + this.tel,
					})
				}
			},

			// 我的页面主体代码
			changImg() {
				let _self = this;
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album'], //从相册选择
					success: function(res) {
						console.log(JSON.stringify(res.tempFilePaths))
						uni.setStorageSync('imgBG', res.tempFilePaths[0])
						_self.imgBG = res.tempFilePaths[0]
					}
				});
			},
		}
	}
</script>

<style lang="scss" scoped>
	.wrap {
		font-size: 28rpx;

		// background-color: #eee;
		.content {
			width: 600rpx;
			margin: 80rpx auto 0;

			.title {
				text-align: left;
				font-size: 60rpx;
				font-weight: 500;
				margin-bottom: 100rpx;
			}

			input {
				text-align: left;
				margin-bottom: 10rpx;
				padding-bottom: 6rpx;
			}

			.tips {
				color: #ccc;
				margin-bottom: 60rpx;
				margin-top: 8rpx;
			}

			.getCaptcha {
				background-color: rgb(253, 243, 208);
				color: #ccc;
				border: none;
				font-size: 30rpx;
				padding: 12rpx 0;

				&::after {
					border: none;
				}
			}

			.alternative {
				color: #ccc;
				display: flex;
				justify-content: space-between;
				margin-top: 30rpx;
			}
		}

		.buttom {
			.loginType {
				display: flex;
				padding: 350rpx 150rpx 150rpx 150rpx;
				justify-content: center;

				.item {
					display: flex;
					flex-direction: column;
					align-items: center;
					color: #ccc;
					font-size: 28rpx;
				}
			}

			.hint {
				padding: 20rpx 40rpx;
				font-size: 20rpx;
				color: #ccc;

				.link {
					color: #ccc;
				}
			}
		}

		.main {
			width: 100%;
			box-sizing: border-box;
			background-color: #eee;
			// height: 100vh;
			min-height: 100vh;
			padding-bottom: 50px;

			.content_image {
				image {
					// 高斯模糊
					// filter: blur(3rpx);
					width: 100%;
				}

				height: 250rpx;
				position: relative;
			}

			.content_image::after {
				content: "";
				position: absolute;
				bottom: -105px;
				left: 0;
				width: 100%;
				height: 40px;
				/* 调整这个值来控制模糊的区域高度 */
				background: inherit;
				/* 继承原图的背景 */
				backdrop-filter: blur(2px);
				/* 调整这个值来控制模糊的强度 */
			}

			.content_botton {
				.content_info {
					position: relative;
					z-index: 999;
					display: flex;
					flex-direction: column;
					align-items: center;
					justify-content: space-around;
					margin-top: 15%;
					margin-left: 5%;
					border-radius: 20rpx;
					width: 90%;
					height: 260rpx;
					background-color: white;
					padding-bottom: 20rpx;
					margin-bottom: 20rpx;

					button[plain] {
						border-color: #35353500;
					}

					.user_profile {
						width: 130rpx;
						height: 130rpx;
						border-radius: 50%;
						overflow: hidden;
						padding: 0;
						margin: 0;
						margin-top: -40rpx;
					}

					image {
						width: 130rpx;
						height: 130rpx;
						border-radius: 50%;
					}

					.username {
						width: 250rpx;

						input {
							width: 100%;
							text-align: center;
							overflow: hidden;
							white-space: nowrap;
							text-overflow: ellipsis;
						}
					}

					.select {
						display: flex;
						justify-content: space-between;

						view {
							padding: 0 40rpx;
						}
					}
				}

				.my_order {
					width: 90%;
					margin-top: 10rpx;
					margin-left: 5%;
					height: 250rpx;
					border-radius: 20rpx;
					background-color: white;
					margin-bottom: 20rpx;

					.my_order_title {
						border-radius: 20rpx;
						padding-left: 20rpx;
						line-height: 80rpx;
						height: 80rpx;
					}

					.my_order_item {
						padding: 4.5%;
						float: left;
						margin-bottom: 20rpx;

						.my_order_content {
							display: flex;
							flex-direction: column;
							align-items: center;
							justify-content: space-between;

							image {
								padding-bottom: 10rpx;
								width: 80rpx;
								height: 50rpx;
							}
						}
					}
				}
			}

			.other {
				margin: 10rpx 40rpx;
				width: 90%;
				padding: 0rpx 20rpx;
				border-radius: 20rpx;
				background-color: white;
				box-sizing: border-box;

				.box {
					width: 100%;
					display: flex;
					align-items: center;
					justify-content: space-between;
					padding: 25rpx 10rpx;
					box-sizing: border-box;

					.title {
						display: flex;
						align-items: center;

						text {
							margin-left: 10rpx;
							font-size: 28rpx;
						}
					}
				}
			}
		}
	}
</style>