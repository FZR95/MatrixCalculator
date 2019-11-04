<template>
	<keep-alive>
		<view class="content">
			<view class="header">
				<view class="inline left">矩阵{{matrix.id+1}}</view>
				<image src="../../static/bianji.png" class="icon1 inline" @click="edit()"></image>
				<image src="../../static/shanchu.png" class="icon2 inline" @click="deletem()"></image>
				<view class="inline right">{{matrix.row}}×{{matrix.column}}</view>
			</view>
			<view class="panel">
				<view class="matrix">
					<text class="Matrixshow">{{Matrix_show}}</text>
				</view>
			</view>
			<uniPopup type="center" ref="popup">
				<text>请按照a11,a12,a13,...,a21,...的次序填写数组，并用空格或回车隔开</text>
				<textarea placeholder="请在此输入..." auto-height="true" v-model="str" maxlength="-1" class="textarea"></textarea>
				<button @click="close()">确定</button>
			</uniPopup>
		</view>
	</keep-alive>
</template>

<script>
	import uniPopup from "@/components/uni-popup/uni-popup.vue"
	export default {
		name: 'MatrixPlate',
		components: {
			uniPopup
		},
		props: {
			'matrix': Object
		},
		data() {
			return {
				str: '',
				Matrix_show: '',
			};
		},
		computed: {
			width: function() {
				return {
					width: 100 / this.row - 3 + "%"
				}
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
	.content {
		margin: 20upx;
		background-color: #f1f2f6;
		border-radius: 30upx;
		height: auto;
		display: block;
		overflow: visible;
	}

	.header {
		display: block;
		border: black solid 0 0 0 3upx;
		border-radius: 30upx 30upx 0 0;
		height: 70upx;
		background-color: #09BB07;
		padding: 16upx;
	}

	.inline {
		display: inline-block;
		margin: 12upx;
	}

	.icon1 {
		position: absolute;
		left: 150upx;
		display: inline-block;
		width: 50upx;
		height: 50upx;

	}

	.icon2 {
		position: absolute;
		left: 250upx;
		display: inline-block;
		width: 50upx;
		height: 50upx;
	}

	.left {
		position: absolute;
		left: 28upx;
	}

	.right {
		position: absolute;
		right: 28upx;
	}

	.panel {
		display: block;
	}

	.matrix {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-flow: row wrap;
		padding-bottom: 10upx;
		margin-bottom: 16upx;
	}

	.Matrixshow {
		white-space: pre;
		overflow: scroll;
	}
	text{
		font-size: 30upx;
	}
	.unit {
		height: 60upx;
		display: flex;
		align-items: center;
		justify-content: center;
		text-align: center;
		margin: 5upx;
		margin-left: 0upx;
		background-color: #ffffff;
	}
</style>
