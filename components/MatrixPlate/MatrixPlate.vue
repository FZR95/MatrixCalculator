<template>
	<keep-alive>
		<view>
			<view class="main">
				<view class="header">
					<image src="../../static/edit.png" @click="edit()"></image>
					<view>矩阵{{matrix.id+1}} {{matrix.row}}×{{matrix.column}}</view>
					<image src="../../static/delete.png" @click="deletem()"></image>
				</view>
				<view class="matrix">
					<text class="Matrixshow">{{Matrix_show}}</text>
				</view>
			</view>
			<uni-popup type="center" ref="popup">
				<view class="POPUP">
					<view class="header">
						<view>编辑矩阵{{matrix.id+1}}</view>
						<view style="display: flex;font-size: 32rpx;align-items: center;">字体大小<input v-model="font_size" type="number" /></view>
					</view>
					<view class="content">
						<textarea v-model="str" maxlength="-1" class="textarea" :style="'font-size:'+font_size+'rpx'"></textarea>
					</view>
					<button @click="close()" type="primary">确 定</button>
				</view>
			</uni-popup>
		</view>
	</keep-alive>
</template>

<script>
	import uniPopup from "../../components/uni-popup/uni-popup.vue"
	export default {
		name: 'MatrixPlate',
		components: {
			uniPopup,

		},
		props: {
			'matrix': Object
		},
		data() {
			return {
				str: '',
				Matrix_show: '',
				font_size: 36
			};
		},
		created() {
			let that = this;
			uni.getStorage({
				key: 'FontSize',
				success: (res) => {
					that.font_size = res.data;
				}
			})
		},
		computed: {
			width: function() {
				return {
					width: 100 / this.row - 3 + "%"
				}
			}
		},
		watch: {
			font_size: function() {
				let that = this;
				uni.setStorage({
					key: 'FontSize',
					data: that.font_size
				})
			}
		},
		methods: {
			edit: function() {
				this.$refs.popup.open();
			},
			deletem: function() {
				this.$emit('Deltem', this.matrix.id);
			},
			close: function() {
				this.$refs.popup.close();
				var numbers = this.str.split(/[\s\n]/);
				this.matrix.m = numbers; //matrix：用于组件显示矩阵的矩阵
				this.Matrix_show = this.Stringify();
				var calcmatrix = this.matrix.m;
				calcmatrix.unshift(this.matrix.id); //calcmatrix：用于运算用的矩阵，返回给父组件，第一位为矩阵序号
				this.$emit('Matrixtrans', calcmatrix);
			},
			Stringify: function() {
				var str = '';
				var row = Number(this.matrix.row);
				var column = Number(this.matrix.column);
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						str = str + Number(this.matrix.m[i * column + j]).toFixed(3) + "  ";
					}
					str = str + "\n";
				}
				return str;
			},
		},
	}
</script>

<style>
	.main {
		width: 710rpx;
		display: flex;
		flex-direction: column;
		margin-top: 20rpx;
		border-radius: 10rpx;
		overflow: hidden;
		background: none;
		box-shadow: 0rpx 0rpx 10rpx 5rpx #C8C7CC;
	}

	.main .header {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		background-color: #1aad19;
		padding: 10rpx;
	}

	image {
		width: 50rpx;
		height: 50rpx;
	}

	.matrix {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-flow: row wrap;
		margin: 20rpx;
	}

	.Matrixshow {
		white-space: pre;
		overflow: scroll;
		max-height: 420rpx;

	}
</style>
