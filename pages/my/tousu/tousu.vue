<template>
	<view class="contnner">
		<view class="content">
			<view class="desc">
				<u--textarea  height="200px" v-model="textareaData" placeholder="请输入投诉内容" count></u--textarea>
			</view>
			<view class="btn" @click="show=true">
				提交投诉
			</view>
		</view>
		<u-modal :show="show" :title="title" :content='content' @confirm="show=false"></u-modal>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				show: false,
				title: '校园帮',
				content: '感谢您的投诉,我们会针对您提出的问题进行研究讨论',
				textareaData: "", 
			};
		},
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

			.btn {
				margin-top: 30rpx;
				width: 100%;
				padding: 25rpx;
				box-sizing: border-box;
				background: rgb(96, 148, 225);
				border-radius: 45rpx;
				font-size: 40rpx;
				color: white;
				text-align: center;
			}
		}
	}
</style>