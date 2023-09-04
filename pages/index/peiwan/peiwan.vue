<template>
	<view class="container">
		<view class="main publicStyle">
			<view class="shouhuo box">
				<view class="title">
					<u-icon name="integral" size="28"></u-icon>
					<text>游戏平台</text>
				</view>
				<view class="select" @click="showGameListState=true">
					{{game}}
				</view>
			</view>
			<u-line></u-line>
			<view class="shouhuo box">
				<view class="title">
					<u-icon name="calendar" size="28"></u-icon>
					<text>游戏时间/盘数</text>
				</view>
				<view class="select">
					<u-input inputAlign="right" type="text" placeholder="例如:13:00-16:00或几盘" border="none"
						v-model="gameNum"></u-input>
				</view>
			</view>
			<u-line></u-line>
			<view class="box" style="margin-top: 20rpx;">
				<view class="title">
					<u-icon name="file-text" size="28"></u-icon>
					<text>备注信息</text>
				</view>
				<view class="size" style="border: 1px solid #ccc;border-radius: 25rpx;flex-direction: column;">
					<textarea v-model="textareaTextData" placeholder="请输入详细信息，如端游的游戏大区，陪玩的要求等"></textarea>
					<view class="selectHelpContent">
						<view class="HelpContentItem" v-for="(item,index) in hellpListData" :key="index"
							@click="setTextateaText(item)">
							{{item}}
						</view>
					</view>
				</view>
			</view>
			<u-line height="2"></u-line>
			<view class="beizhu box">
				<view class="title">
					<u-icon name="pushpin" size="28"></u-icon>
					</image>
					<text>游戏ID</text>
				</view>
				<view class="select">
					<u-input inputAlign="right" type="text" placeholder="输入游戏ID" border="none"
						v-model="gameID"></u-input>
				</view>
			</view>
			<u-line></u-line>
			<view class="shangjia box">
				<view class="title">
					<u-icon name="rmb-circle" size="28"></u-icon>
					</image>
					<text>金额</text>
				</view>
				<view class="select">
					<u-input inputAlign="right" type="text" placeholder="输入金额" border="none" v-model="money"></u-input>
				</view>
			</view>

			<!-- <u-picker :show="sexListState" :columns="sexColumns" @confirm="sexConfirm"
				@cancel="sexListState=false"></u-picker>
			<u-picker :show="kuaidiListState" :columns="kuaidiColumns" @confirm="kuaidiConfirm"
				@cancel="kuaidiListState=false"></u-picker>
			<u-picker :show="qiwangDateListState" :columns="qiwangDateColumns" @confirm="qiwangDateConfirm"
				@cancel="qiwangDateListState=false"></u-picker> -->
		</view>
		<view class="prompt publicStyle">
			<view class="desc">
				<view class="box">
					<view class="title">
						<u-icon name="question-circle" size="25" color="#3796c8"></u-icon>
						<text
							style="width: 80%;">游戏陪玩如长时间没有人接单建议提高金额</text>
					</view>
				</view>
			</view>
		</view>
		<view class="pay publicStyle">
			<view class="box">
				<view class="title">
					需支付
				</view>
				<view class="money">
					{{moneyNum}}元
				</view>
				<view class="btn" @click="payState=true">
					立即发布
				</view>
			</view>
		</view>
		<view class="statement">
			<text style="margin-right: 10rpx;" @click="mianzeState=true">免责声明</text>
			&nbsp;&nbsp;
			<text @click="tiaokuanAndcelvState=true">用户条款&隐私策略</text>
		</view>

		<u-modal :show="mianzeState" title="免责声明" :content='mianzeData' @confirm="mianzeState=false"></u-modal>
		<u-modal :show="tiaokuanAndcelvState" title="用户条款&隐私策略" :content='tiaokuanAndcelvData'
			@confirm="tiaokuanAndcelvState=false"></u-modal>
		<u-keyboard default="" ref="uKeyboard" mode="number" :dotDisabled="true" :mask="true" :mask-close-able="false"
			:dot-enabled="false" :show="payState" :safe-area-inset-bottom="true" :tooltip="false" @change="onChange"
			@backspace="onBackspace">
			<view class="abc">
				<view class="u-text-center u-padding-20 money">
					<text class="amountOfMoney">{{moneyNum}}</text>
					<text class="u-font-10 u-padding-left-10">元</text>
					<view class="u-padding-10 close" data-flag="false" @tap="showPop(false,'x')">
						<u-icon name="close" color="#333333" size="28"></u-icon>
					</view>
				</view>
				<view class="u-flex u-row-center">
					<u-code-input mode="box" :maxlength="6" :dot="true" v-model="password" :disabled-keyboard="true"
						@finish="finish">
					</u-code-input>
				</view>
				<view class="u-text-center u-padding-top-10 u-padding-bottom-20 tips">支付键盘</view>
			</view>
		</u-keyboard>

		<u-picker :show="showGameListState" ref="uPicker" :columns="columns" @confirm="confirm"
			@change="changeHandler"></u-picker>
	</view>
</template>

<script>
	import {
		myRequest
	} from '@/libs/httpRequest.js'
	export default {
		data() {
			return {
				// 快递大小选中索引
				selectSizeIndex: 0,
				// 上传图片
				fileList5: [],
				// 取件信息
				qujianInformation: "",
				// 更多需求按钮状态
				switchState: false,
				// 追加金额按钮状态
				switchZhuiJiaState: false,
				// 赏金
				shangjinNum: "",
				// 性别列表
				sexColumns: [
					['不限', '男生', '女生']
				],
				// 性别列表显示与隐藏
				sexListState: false,
				// 性别
				sex: "无限制",
				// 性别索引
				sexIndex: 0,
				// 快递数量
				kuaidiNum: 1,
				// 快递数量列表
				kuaidiColumns: [
					['1', '2', '3', '4', '5', '6', '7', '8', '9']
				],
				// 快递列表显示与隐藏
				kuaidiListState: false,
				// 期望送达时间
				qiwangDate: "无限制",
				// 期望送达时间索引
				qiwangIndex: 0,
				// 期望送达时间列表
				qiwangDateColumns: [
					["无限制", '上午', '下午']
				],
				// 期望送达时间列表显示与隐藏
				qiwangDateListState: false,
				// 备注信息
				quhuoAddress: "",
				// 收货地址
				address: "",
				// 快递商家
				shangjia: "",
				// 免责声明弹窗状态
				mianzeState: false,
				// 免责声明弹内容
				mianzeData: "1.使用快递业务时，请认真遵守私人财产规范.\r\n2.发布订单后，请勿向外宣传自己的取件信息，否则后果自负，并且不处理退款.\r\n3. 收取快递时， 请检查包裹的完整性， 如出现接单员人为故意损坏包裹的情况， 请及时与平台客服联系举报处理\r\n4. 当您发布订单后， 即代表您同意包裹内的物品好坏与平台无关另类别当处理(包裹未拆封的情况 F)",
				// 用户条款&隐私策略弹窗状态
				tiaokuanAndcelvState: false,
				// 用户条款&隐私策略内容
				tiaokuanAndcelvData: "1.开发者收集的信息，开发者收集你的用户信息(微信昵称、头像、性别、地区)，用于跑腿派送给接单员的联系方式。开发者收集你的位置信息，用于跑腿派送给接单员的联系方式。\r\n2.开发者对信息的存储\r\n2.1 储存地点:境内\r\n 2.2 储存期限:小程序停止运营后及时删除。\r\n3.信息的使用规则\r\n3.1开发者将会在本指引所涵盖的用途内使用收集的信息。\r\n3.2如开发者使用你的信息超出本指引目的或合理范围，会及时更新本指引，同时，开发者在使用你的信息前，再次告知并征得你的明示同意。",
				// imageUrl
				imageUrl: "",
				// 支付弹窗state
				payState: false,
				// 支付密码
				password: '',

				// 选择帮助列表数据
				hellpListData: [
					"游戏内容请自定义",
					"请在完成陪玩内容下再点击完成订单",
					"以上为参考价格",
					"排位/3.5",
					"娱乐/2.5",
				],
				// 帮助内容
				textareaTextData: "",
				// 钱数
				money: 1,
				// 游戏平台
				game: "选择游戏",
				columns: [
					['手游', '端游'],
					['王者荣耀', '和平精英', '阴阳师', '决战平安京', '第五人格', '其他手游', ],
					["手Q安卓", "手Q苹果", "微信安卓", "微信苹果", "手游其他"]
				],
				// 游戏数据
				columnData: [
					['王者荣耀', '和平精英', '阴阳师', '决战平安京', '第五人格', '其他手游', ],
					["绝地求生", "英雄联盟", "穿越火线", "其他端游"],
				],
				daquData: [
					["手Q安卓", "手Q苹果", "微信安卓", "微信苹果", "手游其他"],
					["端游大区"]
				],
				// 游戏选择弹窗状态
				showGameListState: false,
				// 游戏id
				gameID:""
			}
		},
		onShow() {
			// 从缓存中获取手动选择的收获地址 如果选择了就用用户选择的 如果没有选择 将用默认地址
			console.log("获取到地址了", uni.getStorageSync("CacheItem"));
			if (!uni.getStorageSync("CacheItem")) {
				let _self = this;
				let openid = uni.getStorageSync("openid")
				let data = {
					openid
				}
				myRequest({
					url: "/address/all",
					method: "post",
					data
				}).then((res) => {
					if (res.data.code == 1) {
						for (let item of res.data.data) {
							if (item.def) {
								uni.setStorage({
									key: "CacheItem",
									data: item
								}).then(() => {
									_self.address = item.address
								})
								break;
							}
						}

					}
				})
			} else {
				this.address = uni.getStorageSync("CacheItem").address;
			}
			this.shangjia = uni.getStorageSync("CacheShangJia").title + uni.getStorageSync("CacheShangJia").desc;
		},
		// 页面卸载时
		onUnload() {
			console.log("卸载了");
			// 清楚选中的地址 让下一次进入页面时可以自动获取
			uni.removeStorageSync("CacheItem");
		},
		computed: {
			// 总钱数
			moneyNum() {
				return Math.abs(this.money);
			}
		},
		methods: {
			changeHandler(e) {
				const {
					columnIndex,
					value,
					values, // values为当前变化列的数组内容
					index,
					// 微信小程序无法将picker实例传出来，只能通过ref操作
					picker = this.$refs.uPicker
				} = e
				// 当第一列值发生变化时，变化第二列(后一列)对应的选项
				if (columnIndex === 0) {
					// picker为选择器this实例，变化第二列对应的选项
					picker.setColumnValues(1, this.columnData[index])
					picker.setColumnValues(2, this.daquData[index])
				}
			},
			// 回调参数为包含columnIndex、value、values
			confirm(e) {
				console.log('confirm', e)
				this.game = e.value[0] + "-" + e.value[1] + "-" + e.value[2]
				this.showGameListState = false
			},



			// 选中帮助选项
			setTextateaText(item) {
				this.textareaTextData += " " + item + " "
			},

			//支付模块开始-------------------------------------------------
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
				let _self = this;
				uni.showLoading({
					title: '支付中'
				})

				setTimeout(() => {
					uni.hideLoading();
					this.payState = false;
					uni.showToast({
						icon: 'success',
						title: '支付成功'
					})
					_self.releaseOrder()
				}, 2000);
			},
			showPop(flag = true, source) {
				this.password = '';
				this.payState = flag;
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
			// 支付模块结束------------------------------------------------
			// 发布订单
			releaseOrder() {
				let openid = uni.getStorageSync("openid");
				let _data = {
					openid: openid,
					money: this.moneyNum,
					notes: this.textareaTextData,
					end:this.gameID,
					startingPoint:this.game,
					kuaidiNum:this.gameNum,
				}
				myRequest({
					url: "/peiwan",
					method: "post",
					data: _data
				}).then((res) => {
					console.log("成功");
					setTimeout(() => {
						uni.navigateBack({
							delta: 1, //返回层数，2则上上页
						})
					}, 1500)

				}).catch((err) => {
					console.log("失败");
				})
			},
			gotoAddressPage() {
				uni.navigateTo({
					url: "/pages/publicPage/address/address"
				})
			},
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		width: 100%;
		height: 100vh;
		box-sizing: border-box;
		padding: 10rpx;
		background: rgb(247, 248, 247);

		.publicStyle {
			width: 100%;
			background: white;
			border-radius: 25rpx;
			margin-top: 20rpx;
			box-shadow:
				10px 20px 50px rgba(0, 0, 0, 0.15);

		}

		.main {
			width: 100%;
			box-sizing: border-box;
			padding: 20rpx;

			.box {
				width: 100%;
				padding: 0rpx 50rpx;
				box-sizing: border-box;

				.title {
					display: flex;
					align-items: center;

					text {
						margin-left: 10rpx;
					}
				}

				.size {
					width: 100%;
					padding: 0rpx 10rpx;
					margin-top: 30rpx;
					margin-bottom: 30rpx;
					box-sizing: border-box;
					display: flex;
					align-items: center;
					justify-content: space-between;

					textarea {
						// border: 1px solid #e5e5e5;
						width: 100%;
						height: 130rpx;
						padding: 15rpx;
						overflow-y: auto;
					}

					.selectHelpContent {
						width: 100%;
						margin: 10rpx 0rpx;
						display: flex;
						flex-wrap: wrap;

						.HelpContentItem {
							width: max-content;
							padding: 15rpx 25rpx;
							color: #3fafe7;
							background: rgb(242, 249, 255);
							border-radius: 10rpx;
							margin: 10rpx;
						}
					}
				}

			}



			.shouhuo,
			.shangjia,
			.beizhu,
			.gengduo,
			.qiwang,
			.jine,
			.zhuijia,
			.kdNum,
			.sex {
				width: 100%;
				display: flex;
				align-items: center;
				justify-content: space-between;
				margin-top: 40rpx;
				margin-bottom: 20rpx;
			}
		}

		.prompt {
			width: 100%;
			padding: 10rpx;
			box-sizing: border-box;

			.desc {
				width: 100%;

				.box {
					width: 100%;

					.title {
						width: 100%;
						display: flex;
						align-items: center;
						padding: 20rpx 40rpx;
					}
				}

				text {
					margin-left: 20rpx;
					color: rgb(55, 150, 200);
				}
			}
		}

		.pay {
			width: 100%;
			padding: 20rpx 50rpx;
			box-sizing: border-box;

			.title {
				width: 100%;
				text-align: center;
				font-size: 50rpx;
				font-weight: 800;
				padding: 20rpx 0rpx;
			}

			.money {
				width: 100%;
				color: red;
				text-align: center;
				font-weight: 500;
				font-size: 35rpx;
			}

			.btn {
				width: 100%;
				color: white;
				height: 80rpx;
				line-height: 80rpx;
				text-align: center;
				background: rgb(96, 148, 225);
				border-radius: 50rpx;
				font-size: 30rpx;
				margin-top: 30rpx;
				margin-bottom: 30rpx;
			}
		}

		.statement {
			color: rgb(96, 148, 225);
			width: 100%;
			padding: 50rpx 0rpx;
			text-align: center;
		}

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
	}
</style>