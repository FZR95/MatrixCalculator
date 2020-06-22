<template>
	<view class="main">
		<view class="caption">方程未知数的数量：<input placeholder="在此输入" type="number" v-model="dim" @blur="Dispose" /></view>
		<textarea placeholder="在此输入方程系数矩阵" @blur="Dispose" v-model="inputstr" :style="'font-size:'+font_size+'rpx'"></textarea>
		<view class="caption">方程：<button @click="Dispose" type="primary">确定</button></view>
		<view v-if="!matrixnum" style="display: flex;align-self: center;">系数矩阵应为{{dim}}行{{Number(dim)+1}}列。</view>
		<view class="equaview" v-for="(item,index) in latex" :key="index">
			{{item}}
		</view>
		<view class="caption">方程的解：</view>
		<view class="equaview" v-for="(item,index) in solved" :key="index">
			{{item}}
		</view>

		<view class="caption">定义：<image src="../../static/question.png" style="width: 42rpx;height: 42rpx;" @click="GoDef"></image>
		</view>
		<view>使用克拉默法则解一次方程。每一个未知数的解为其相应的系数矩阵行列式/系数矩阵行列式。</view>
		<view class="caption">详细步骤：</view>
		<view v-for="(item,index) in progress" :key="index">
			<view class="progressviewcaption">
				<view v-if="index==0">
					原系数矩阵：
				</view>
				<view v-else>
					a{{index}}系数矩阵:
				</view>
				<view>（行列式={{Result[index]}}）</view>
			</view>

			<br />
			<view class="progressview">
				<view v-for="(it,index) in item" :key="index">
					{{it}}
				</view>
			</view>

		</view>
	</view>
</template>


<script>
	export default {
		data() {
			return {
				font_size: 36,
				dim: '',
				inputstr: '',
				matrix: [],
				matrix_2D: [],
				Result: [], //contains all the determinant's result
				//SVCs
				latex: [],
				solved: [],

				matrixnum: false,
				progress: [],
				adshow: false,
			}
		},
		mounted() {},
		onLoad() {
			let that = this;
			uni.getStorage({
				key: 'FontSize',
				success: (res) => {
					that.font_size = res.data;
				}
			})
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
			GoDef() {
				uni.navigateTo({
					url: '../question/question?method=7'
				})
			},
			Dispose() {

				this.matrixnum = false;
				this.matrix.length = 0;
				this.matrix_2D.length = 0;
				this.latex.length = 0;
				this.solved.length = 0;
				this.Result.length = 0;
				this.progress.length = 0;

				let str = '';
				if (typeof this.dim !== 'undefined') {
					if (typeof this.inputstr !== 'undefined') {
						var numbers = this.inputstr.split(/[\s\n]/);
						this.matrix = numbers;

					}
				}
				if (this.matrix.length == this.dim * (Number(this.dim) + 1)) {
					this.matrixnum = true;
					//put latex into latex
					let dim = Number(this.dim);
					for (let i = 0; i < dim; i++) {
						let src = '';
						for (let j = 0; j < dim; j++) {
							src += this.matrix[i * (dim + 1) + j] + 'a_' + (j + 1);
							if (j != dim - 1)
								src += '+'
						}
						src += '=' + this.matrix[(i + 1) * (dim + 1) - 1] + 'b_' + (i + 1);
						this.latex.push(src);
					}
					if (this.dim > 1) {
						//Calc
						this.Solve();
						//put result into solve
						for (let i = 0; i < this.dim; i++) {
							let src = 'a_' + (i + 1);
							src += '=' + this.Result[i + 1] + '/' + this.Result[0] +
								'=' +
								(this.Result[i + 1] / this.Result[0]).toFixed(3);
							this.solved.push(src);
							this.adshow = true;
						}
					}

				}
			},
			Solve() {
				//create original matrix
				let matrix = new Array()
				for (let i = 0; i < this.dim; i++) {
					matrix[i] = new Array();
					for (let j = 0; j < this.dim; j++) {
						matrix[i][j] = this.matrix[i * (Number(this.dim) + 1) + j]
					}
				}
				this.matrix_2D.push(matrix);


				for (let num = 0; num < this.dim; num++) {
					//copy original matrix
					this.matrix_2D[num + 1] = new Array();
					for (let i = 0; i < this.dim; i++) {
						this.matrix_2D[num + 1][i] = new Array();
						for (let j = 0; j < this.dim; j++) {
							this.matrix_2D[num + 1][i][j] = matrix[i][j];
						}
					}
					//create corresponding matrix
					for (let i = 0; i < this.dim; i++) {
						this.matrix_2D[num + 1][i][num] = this.matrix[(i + 1) * (Number(this.dim) + 1) - 1];
					}
				}
				//Calc Det

				this.toprogress(matrix);
				this.Determinant(matrix);
				for (let i = 0; i < this.dim; i++) {
					this.toprogress(this.matrix_2D[i + 1]);
					this.Determinant(this.matrix_2D[i + 1]);
				}
			},
			toprogress(matrix) {
				let str = [];
				var reg = /,/g;
				for (let i = 0; i < this.dim; i++) {
					str[i] = matrix[i].join().replace(reg, '  ');
				}
				this.progress.push(str);
			},
			Determinant: function(matrix) {
				var dim = this.dim;
				var result = 0;
				//实际算法
				result = this.det(matrix, dim);
				this.Result.push(result.toFixed(3));
			},
			exchange: function(matrix, i1, i2) {
				//两行对换
				var temp = new Array();
				temp = matrix[i1];
				matrix[i1] = matrix[i2];
				matrix[i2] = temp;
			},
			det: function(matrix, dim) {
				if (dim == 1) {
					return matrix[0][0];
				} //若一维矩阵，结果为a00
				var index;
				for (index = dim - 1; index > -1; index--) {
					if (matrix[index][dim - 1] != 0)
						break;
				} //检索最后一列不为0的行
				if (index == -1)
					return 0; //奇异矩阵，对角线含0
				var sign = 1;
				if (index != dim - 1) {
					this.exchange(matrix, index, dim - 1);
					sign = -1;
				}
				var last_element = matrix[dim - 1][dim - 1];
				for (var i = 0; i < dim - 1; i++) {
					var factor = matrix[i][dim - 1] / last_element;
					matrix[i][dim - 1] = 0;
					for (var j = 0; j < dim - 1; j++) {
						matrix[i][j] -= factor * matrix[dim - 1][j];
					}
				}
				return sign * last_element * this.det(matrix, dim - 1);
			},

		}
	}
</script>

<style>
	@import "../../common/Equation.css";
</style>
