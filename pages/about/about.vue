<template>
	<view class="main">
		<view class="font_title">帮助</view>
		<view class="content">
			欢迎使用本矩阵计算工具，接下来您将了解到本小程序的使用方法和小提示。
			<br />Step1:将想要添加矩阵的行、列填入应用最上方的输入框中，然后点击添加按钮即可。<br />Step2:接着，点击相应矩阵的编辑按钮以进行编辑。请按照a11,a12...,a21,a22的顺序填写，中间用空格或者回车隔开，建议您依照矩阵的样式在行尾换行，更加直观便于编辑。示例填写：<br />1
			2<br />3 4<br />Step3:单击应用底部的按钮进行相应运算，在弹出框中填入您想要运算的矩阵或者数乘数。<br />Step4:若您想要复制运算出的结果矩阵，或者让其参与下一步运算，点击复制按钮，创建一个相应的新矩阵，在编辑界面中粘贴即可。
			<br />
			<view style="color:#e64340;">如果您发现输入过后呈现出的矩阵有一项为0，请注意矩阵的输入仅支持英文状态下的空格，且两项之间仅由单个空格或回车隔开，空格和回车在一起也是不可以的。</view>
		</view>
		<button open-type="contact" type="primary">意见与建议</button>
		<button open-type="feedback" type="default">问题反馈</button>
		<button open-type="share">分享该应用</button>
		<view class="font_title">关于</view>
		<view>
			计划中的更新：<br />
			[1]过客激励计划；<br />
			[2]详情页展现详细步骤；<br />
			[3]夜间模式。<br />
		</view>
		<view>
			1.2.x后续小幅更新日志：<br />
			[新增]矩阵求幂功能；<br />
			[移除]Latex图片，先暂时改为纯文本；<br />
			[新增]矩阵内容输入字体大小调节；<br />
			[新增]欢迎语。<br />
		</view>
		<view>
			1.2版本更新日志： <br />
			本来是寒假计划的工作，但是拖到了现在，作为一个学集成电路的大学狗，终于有勇气继续更新了，同时本应用的使用人数达到了一个里程碑，用这个更新庆祝一下吧！<br />
			[新增]解方程功能；<br />
			[新增]矩阵运算过程详情页；<br />
			[新增]矩阵历史记录页，不小心删掉了也没关系啦（仅限小程序当次运行时创建的矩阵）；<br />
			[新增]反馈、问题与界面、页面和小程序分享；<br />
			[优化]崭新的视觉效果；<br />
			本次更新在新增的功能页中加入了广告横幅，原界面仍未增加任何广告，广告是独立开发者继续创作下去的动力之一，感谢您的理解。
		</view>
		<view>1.1版本更新日志：<br />
			[新增]矩阵求逆；<br />
			[移除]小数模式，默认精确至小数点后三位；<br />
			[修改]不再限制所创建的矩阵数量；<br />
			[修改]不再限制矩阵元素个数；<br />
			[优化]列数增多后的显示效果。<br />
			您的反馈将是对我的最大支持！如有问题，敬请联系Email:<br />matrix826@163.com
			<!--<br />特别感谢酷安网友@美得有声有色 为本小程序陆续提供的各种建议和存在的问题。-->
		</view>
		<button type="primary" @click="Github()">GitHub</button>
	</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			}
		},
		onShareAppMessage: function(res) {
			return {
				title: '欢迎使用矩阵计算工具',
				path: '/pages/index/index',
				imageUrl: '/static/gh_98d64701f7a7_430.jpg'
			}
		},
		methods: {
			Github: function() {
				uni.setClipboardData({
					data: 'https://github.com/FZR95/MatrixCalculator.git',
					success: function() {
						uni.showToast({
							title: "已复制至剪贴板"
						})
					}
				});
			}
		}
	}
</script>

<style>
	button {
		width: 100%;
		margin-bottom: 20rpx;
	}

	.main {
		padding: 20rpx;
		display: flex;
		flex-direction: column;

	}

	.font_title {
		font-size: 64rpx;
		align-self: flex-start;
		margin-top: 291rpx;
		margin-bottom: 100rpx;
	}

	.content {
		display: flex;
		flex-direction: column;
		align-items: flex-start;
		justify-content: flex-start;
		width: 100%;
	}

	.content view {
		margin-bottom: 20rpx;
	}
</style>
