<template>
	<view style="display: flex;flex-direction: column;justify-content: center; align-items:  center;text-align: center;position: fixed;left: 0;bottom: 0;right: 0;top: 0;">
		<view style="font-weight: 700;line-height: 46rpx;">微信小程序不支持程序内浏览网页，点击下方的按钮将链接复制到剪贴板，然后使用浏览器浏览有关定义。链接如下：</view>
		<view style="word-break: break-all;">{{link}}</view>
		<button type="primary" @click="copy">复制链接</button>
	</view>
</template>

<script>
	export default {
		onLoad: function(res) {
			this.link = this.links[Number(res.method)];
		},
		data() {
			return {
				links: ['https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E5%8A%A0%E6%B3%95/12641418?fr=aladdin',
					'https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E5%8A%A0%E6%B3%95/12641418?fr=aladdin',
					'https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E4%B9%98%E6%B3%95',
					'https://zhidao.baidu.com/question/373033325.html',
					'https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E8%BD%AC%E7%BD%AE/4150715?fr=aladdin',
					'https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E8%A1%8C%E5%88%97%E5%BC%8F/18882017?fr=aladdin', 'https://zhidao.baidu.com/question/561292066099943604.html',
					'https://baike.baidu.com/item/%E5%85%8B%E8%8E%B1%E5%A7%86%E6%B3%95%E5%88%99/7211518?fromtitle=%E5%85%8B%E6%8B%89%E9%BB%98%E6%B3%95%E5%88%99&fromid=911408&fr=aladdin',
					'https://baike.baidu.com/item/%E7%9F%A9%E9%98%B5%E6%B1%82%E9%80%86/12712839?fr=aladdin'
				],
				link: ''
			}
		},
		methods: {
			copy() {
				uni.setClipboardData({
					data: this.link
				})
			}
		}
	}
</script>

<style>

</style>
