<template>
	<view class="container">
		<view class="main publicStyle">
			<view class="box">
				<view class="title">
					<u-icon name="gift" size="28"></u-icon>
					<text>快递大小</text>
				</view>
				<view class="size">
					<view class="item" :class="[selectSizeIndex==0?'activeItem':'']" @click="selectSizeIndex=0">
						小件 ￥2
					</view>
					<view class="item" :class="[selectSizeIndex==1?'activeItem':'']" @click="selectSizeIndex=1">
						中件 ￥3
					</view>
					<view class="item" :class="[selectSizeIndex==2?'activeItem':'']" @click="selectSizeIndex=2">
						大件 ￥5
					</view>
				</view>
			</view>
			<u-line height="2"></u-line>
			<view class="qujian box">
				<view class="title">
					<u-icon name="file-text" size="28"></u-icon>
					<text>取件信息</text>
				</view>
				<view class="information">
					<textarea placeholder="请输入取件码或上传截图(仅接单的的接单员可见)" v-model="qujianInformation"></textarea>
					<u-upload :fileList="fileList5" @afterRead="afterRead" @delete="deletePic" name="5" multiple
						:maxCount="2" deletable previewImage></u-upload>
				</view>
			</view>
			<view class="shouhuo box">
				<view class="title">
					<u-icon name="map" size="28"></u-icon>
					<text>收货地址</text>
				</view>
				<view class="select" @click="gotoAddressPage">
					{{address||"请选择收货地址"}}
				</view>
			</view>
			<u-line></u-line>
			<view class="shangjia box">
				<view class="title">
					<u-icon name="account" size="28"></u-icon>
					</image>
					<text>快递商家</text>
				</view>
				<view class="select" @click="gotoKuaidishangjia">
					{{shangjia||"选择快递点"}}
				</view>
			</view>
			<u-line></u-line>
			<view class="beizhu box">
				<view class="title">
					<u-icon name="file-text" size="28"></u-icon>
					</image>
					<text>备注信息</text>
				</view>
				<view class="select">
					<u-input inputAlign="right" type="text" placeholder="所有人可见" border="none"
						v-model="beizhu"></u-input>
				</view>
			</view>
			<u-line></u-line>
			<view class="beizhu box">
				<view class="title">
					<u-icon name="more-dot-fill" size="28"></u-icon>
					</image>
					<text style="color:b;">更多要求</text>
				</view>
				<view class="select">
					<u-switch v-model="switchState" size="25"></u-switch>
				</view>
			</view>
			<u-line></u-line>
			<view class="hiddOption" v-if="switchState">
				<view class="qiwang box">
					<view class="title">
						<text>期望送达</text>
					</view>
					<view class="select" @click="qiwangDateListState=true">
						{{qiwangDate}} >
					</view>
				</view>
				<u-line></u-line>
				<view class="sex box">
					<view class="title">
						<text>性别限制</text>
					</view>
					<view class="select" @click="sexListState=true">
						{{sex}} >
					</view>
				</view>
				<u-line></u-line>
				<view class="kdNum box">
					<view class="title">
						<text>快递数量</text>
					</view>
					<view class="select" @click="kuaidiListState=true">
						{{kuaidiNum}} 个 >
					</view>
				</view>
				<u-line></u-line>
				<view class="zhuijia box">
					<view class="title">
						<text>追加金额</text>
					</view>
					<view class="select">
						<u-switch v-model="switchZhuiJiaState" size="25"></u-switch>
					</view>
				</view>
				<u-line></u-line>
				<view class="jineBox" v-if="switchZhuiJiaState">
					<view class="jine box">
						<view class="title">
							<text>金额</text>
						</view>
						<view class="select" style="width: 90px;text-align: center;">
							<u-input type="number" placeholder="输入赏金" border="bottom" v-model="shangjinNum"
								@change="zhuijiaHandler"></u-input>
						</view>
					</view>
					<u-line></u-line>
				</view>
			</view>
			<u-picker :show="sexListState" :columns="sexColumns" @confirm="sexConfirm"
				@cancel="sexListState=false"></u-picker>
			<u-picker :show="kuaidiListState" :columns="kuaidiColumns" @confirm="kuaidiConfirm"
				@cancel="kuaidiListState=false"></u-picker>
			<u-picker :show="qiwangDateListState" :columns="qiwangDateColumns" @confirm="qiwangDateConfirm"
				@cancel="qiwangDateListState=false"></u-picker>
		</view>
		<view class="prompt publicStyle">
			<view class="desc">
				<view class="box">
					<view class="title">
						<u-icon name="question-circle" size="25" color="#3796c8"></u-icon>
						<text>除了顺丰快递，其它快递都在菜鸟驿站!</text>
					</view>
				</view>
				<u-line></u-line>
				<view class="box">
					<view class="title">
						<u-icon name="question-circle" size="25" color="#3796c8"></u-icon>
						<text>信息仅接单的同学可见，安全放心!</text>
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
				qujianInformation:"",
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
				sexIndex:0,
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
				qiwangIndex:0,
				// 期望送达时间列表
				qiwangDateColumns: [
					["无限制", '上午', '下午']
				],
				// 期望送达时间列表显示与隐藏
				qiwangDateListState: false,
				// 备注信息
				beizhu: "",
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
				password: ''
			}
		},
		onShow() {
			// 从缓存中获取手动选择的收获地址 如果选择了就用用户选择的 如果没有选择 将用默认地址
			console.log("获取到地址了", uni.getStorageSync("CacheItem"));
			if(!uni.getStorageSync("CacheItem")){
				let _self=this;
				let openid=uni.getStorageSync("openid")
				let data={
					openid
				}
				myRequest({
					url:"/address/all",
					method:"post",
					data
				}).then((res)=>{
					if(res.data.code==1){
						for (let item of res.data.data) {
							if(item.def){
								uni.setStorage({
									key:"CacheItem",
									data:item
								}).then(()=>{
									_self.address=item.address
								})
								break;
							}
						}
						
					}
				})
			}else{
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
				const arr = [2, 3, 5];
				let baseMoney=arr[this.selectSizeIndex];
				let shangjin=0
				if(this.switchState){
					baseMoney=arr[this.selectSizeIndex] * this.kuaidiNum
				}
				if(this.switchState&&this.switchZhuiJiaState){
					shangjin=(this.shangjinNum == "" ? 0 : Math.abs(this
					.shangjinNum))
				}
				return baseMoney + shangjin;
			}
		},
		methods: {
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
				let _self=this;
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
				let openid=uni.getStorageSync("openid");
				let _data = {
					openid:openid,
					kuaidiType:this.selectSizeIndex,
					kuaidiNum:this.kuaidiNum,
					notes:this.beizhu,
					desireTime:this.qiwangIndex,
					image:this.imageUrl,
					money:this.moneyNum,
					sex:this.sexIndex,
					startingPoint:this.shangjia,
					end:this.address,
					qujianInformation:this.qujianInformation,
					shouHuoAddress:uni.getStorageSync("CacheItem"),
					kuaidiShangjia:uni.getStorageSync("CacheShangJia"),
				}
				myRequest({
					url: "/kuaidi",
					method: "post",
					data: _data
				}).then((res)=>{
					console.log("成功");
					setTimeout(()=>{
						uni.navigateBack({
							delta: 1, //返回层数，2则上上页
						})
					},1500)
					
				}).catch((err)=>{
					console.log("失败");
				})
			},
			gotoKuaidishangjia() {
				uni.navigateTo({
					url: "/pages/publicPage/kuaidishangjia/kuaidishangjia"
				})
			},
			gotoAddressPage() {
				uni.navigateTo({
					url: "/pages/publicPage/address/address"
				})
			},
			// 期望送达日期确认按钮事件
			qiwangDateConfirm(e) {
				console.log(e.indexs[0]);
				this.qiwangIndex=e.indexs[0];
				this.qiwangDate = this.qiwangDateColumns[0][e.indexs[0]];
				this.qiwangDateListState = false;
			},
			// 选择性别确认按钮事件
			sexConfirm(e) {
				console.log(e.indexs[0]);
				this.sexIndex=e.indexs[0];
				this.sex = this.sexColumns[0][e.indexs[0]];
				this.sexListState = false;
			},
			// 选择快递数量确认按钮事件
			kuaidiConfirm(e) {
				this.kuaidiNum = this.kuaidiColumns[0][e.indexs[0]];
				this.kuaidiListState = false;
			},
			// 删除图片
			deletePic(event) {
				this[`fileList${event.name}`].splice(event.index, 1)
			},
			// 新增图片
			async afterRead(event) {
				// 当设置 multiple 为 true 时, file 为数组格式，否则为对象格式
				let lists = [].concat(event.file)
				let fileListLen = this[`fileList${event.name}`].length
				lists.map((item) => {
					this[`fileList${event.name}`].push({
						...item,
						status: 'uploading',
						message: '上传中'
					})
				})
				for (let i = 0; i < lists.length; i++) {
					const result = await this.uploadFilePromise(lists[i].url)
					let item = this[`fileList${event.name}`][fileListLen]
					this[`fileList${event.name}`].splice(fileListLen, 1, Object.assign(item, {
						status: 'success',
						message: '',
						url: result
					}))
					fileListLen++
				}
			},
			uploadFilePromise(url) {
				return new Promise((resolve, reject) => {
					let a = uni.uploadFile({
						url: 'http://39.106.27.4:8080/common/upload', // 仅为示例，非真实的接口地址
						filePath: url,
						name: 'file',
						formData: {
							user: 'test'
						},
						success: (res) => {
							// 给imageUrl赋值
							console.log(JSON.parse(res.data));
							this.imageUrl=JSON.parse(res.data).data.url;
							setTimeout(() => {
								resolve(res.data.data)
							}, 1000)
						}
					});
				})
			},

			// 追加金额输入框事件
			zhuijiaHandler(e) {
				if (e.length == 0) {
					this.shangjinNum = 0
				}
				console.log(e);
			}
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

					.item {
						height: 30rpx;
						line-height: 30rpx;
						text-align: center;
						border: 1px solid #cccccc;
						border-radius: 20rpx;
						padding: 15rpx 20rpx;
					}

					.activeItem {
						color: #f9ae3d;
						border-color: #f9ae3d;
					}
				}

			}

			.qujian {
				.title {
					padding: 20rpx 0px;
				}

				.information {
					width: 100%;
					// height: 200rpx;
					border: 1px solid #cccccc;
					border-radius: 25rpx;
					padding: 20rpx;

					textarea {
						width: 100%;
						height: 150rpx;
						overflow-y: auto;
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