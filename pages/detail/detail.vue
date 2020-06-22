<template>
	<view class="main">
		<view class="font_title" style="display: flex;justify-content: space-between;align-items: center;">
			<view>{{Method[method]}}</view>
			<image src="../../static/question.png" @click="Question()" style="width: 32rpx;height: 32rpx;margin-left: 64rpx;"></image>
		</view>
		<view class="caption">矩 阵</view>
		<view class="matrix_calc">
			<view>
				<view v-for="(item,index) in DisplayMatrix[0]" :key="index">{{item}}</view>
			</view>
			<view>{{symbol[method]}}</view>
			<view v-if="method<3">
				<view v-for="(item,index) in DisplayMatrix[1]" :key="index">{{item}}</view>
			</view>
			<view v-if="method==3||method==6">{{c}}</view>
		</view>
		<view class="caption">结 果</view>
		<view class="matrix_progress" style="border: none;margin-bottom: 0;">
			<view v-for="(item,index) in DisplayMatrix[2]" :key="index">{{item}}</view>
		</view>
		<view class="caption">定 义</view>
		<view v-if="method==0">矩阵相加即两矩阵对应位置的每一项相加。要求两矩阵行、列数均相等。聪明的你应该不需要再看详细步骤了吧！</view>
		<view v-if="method==1">矩阵相减即两矩阵对应位置的每一项相减。要求两矩阵行、列数均相等。聪明的你应该不需要再看详细步骤了吧！（解释链接与矩阵加法相同。）</view>
		<view v-if="method==2">两矩阵相乘，其运算规则如下：矩阵A的第m行跟矩阵B的第n列对应运算，其中A的行中第x个元素与B的列中第x个元素对应相乘，将所有乘积相加即得到结果矩阵的第m行第n列元素。<br />设矩阵A为m×p，矩阵B为p×n，所得结果矩阵为m×n，因此矩阵A的列数需等于矩阵B的行数。<br />
			<!-- <image src="http://latex.codecogs.com/svg.latex?A_{ij}=\sum_{k=1}^{p}a_{ik}b_{kj}=a_{i1}b_{1j}+a_{i2}b_{2j}+\cdots+a_{ip}b_{pj}" style="border: 0;"></image> -->
		</view>
		<view v-if="method==3">矩阵的数乘即矩阵的每一项与数乘数相乘。聪明的你应该不需要再看详细步骤了吧！</view>
		<view v-if="method==4">矩阵转置，其运算规则如下：矩阵的第m行第n列元素为结果矩阵的第n行第m列元素，所得结果矩阵行列与原矩阵颠倒。聪明的你应该不需要再看详细步骤了吧！</view>
		<view v-if="method==5">矩阵求行列式，主要有两种求解方法：<br />1.将矩阵化简为三角矩阵，将对角线元素相乘；<br />2.将每一个元素的代数余子式相加。<br />本程序用的是前者。详细步骤还未完工……。</view>
		<view v-if="method==8">
			矩阵求逆，主要有两种求解方法：<br />1.矩阵的伴随矩阵/矩阵的行列式。其中伴随矩阵的每一个元素为原矩阵对应位置的代数余子式；<br />2.恒等变形法：A*A逆=E。<br />本程序用的是前者。详细步骤还未完工……。
		</view>
		<view v-if="method==6">
			矩阵求幂，即矩阵的连续自乘，详细步骤还未完工……。
		</view>
		<view class="caption">点个广告支持一下吧！</view>
		<!--
		<view class="caption" v-if="method>4">详细步骤</view>
		<view v-if="method==5" class="DisplayPogress">
			<view v-for="(item,index) in DisplayMatrix" :key="index" v-if="index>2">
				<view>Step{{index-2}}:</view>
				<view class="matrix_progress">
					<view v-for="(it,index) in item" :key="index">{{it}}</view>
				</view>
			</view>
		</view>
		<view v-if="method==6">
			<view v-for="(item,index) in Progress" :key="index" v-if="index>2">
				<view>Step{{index-2}}:</view>
				<view class="matrix_progress">
					<view v-for="(it,index) in item" :key="index">{{it}}</view>
				</view>
			</view>
		</view>
		-->
	</view>
</template>
<script>
	export default {
		onLoad: function() {
			this.resdispose();
			// uni.$on('DetailPage',function(res){
			// 	this.resdispose(res)
			// })
		},
		// onUnload:function(){
		// 	uni.$off('DetailPage');
		// },
		data() {
			return {
				Method: ['相加', '相减', '相乘', '数乘', '转置', '求行列式', '求幂', '', '求逆'],
				symbol: ['+', '-', '×', '数乘', '转置', '求行列式', '^', '', '求逆'],
				method: 0,
				matrix: [],
				a: 1,
				b: 2,
				c: 1,
				DisplayMatrix: [],
				DisplayPogress: [],
				InverProgress: [],
				fixednum: 3,
				dim: 1
			}
		},
		methods: {
			Question() {
				uni.navigateTo({
					url: '../question/question?method=' + this.method
				})
			},
			resdispose() {
				this.method = getApp().globalData.method;
				this.matrix = getApp().globalData.matrix;
				this.c = getApp().globalData.c;
				this.MatrixDispose(this.matrix[0].calc, this.matrix[0].row, this.matrix[0].column);
				if (this.method < 4)
					this.MatrixDispose(this.matrix[1].calc, this.matrix[1].row, this.matrix[1].column);
				this.DisplayMatrix[2] = '';
				switch (this.method) {
					case 0:
						{
							this.Add();
							break;
						};
					case 1:
						{
							this.Sub();
							break;
						};
					case 2:
						{
							this.Time();
							break;
						};
					case 3:
						{
							this.Scalar();
							break;
						};
					case 4:
						{
							this.Transpose();
							break;
						};
					case 5:
						{
							this.Determinant();
							break;
						};
					case 6:
						{
							this.Pow();
							break;
						};
					//case 7:Cramer's 
					case 8:
						{
							this.Inversion();
							break;
						};

				}
			},
			//展现数组,每行为字符串数组的一个
			MatrixDispose(matrix, row, column) {
				let str = new Array();
				for (let i = 0; i < row; i++) {
					str[i] = '';
					for (let j = 0; j < column; j++) {
						if (j < column - 1)
							str[i] += matrix[i * column + j] + '  ';
						else
							str[i] += matrix[i * column + j];
					}
				}
				this.DisplayMatrix.push(str);
			},
			MatrixDispose_2D(matrix, row, column) {
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						matrix[i][j] = matrix[i][j];
					}
				}
				let str = new Array();
				var reg = /,/g;
				for (let i = 0; i < row; i++) {
					str[i] = '';
					for (let j = 0; j < row; j++) {
						str[i] += matrix[i][j].toFixed(3) + ' '
					}
				}
				this.DisplayMatrix.push(str);
			},
			//CalcFunc
			Claim2D: function(matrix, row, column) {
				for (var i = 0; i < row; i++) {
					matrix[i] = new Array();
					for (var j = 0; j < column; j++) {
						matrix[i][j] = 0;
					}
				}
			}, //声明二维数组
			Transfer: function(matrix, order) {
				var row = this.matrix[order].row;
				var column = this.matrix[order].column;
				this.Claim2D(matrix, row, column);
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						matrix[i][j] = Number(this.matrix[order].calc[i * column + j]);
					}
				}
			}, //转为二维数组
			ParseResult(matrix, row, column) {
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						matrix[i][j] = matrix[i][j].toFixed(3);
					}
				}
				let str = new Array();
				var reg = /,/g;
				for (let i = 0; i < row; i++) {
					str[i] = matrix[i].join().replace(reg, '  ');
				}
				this.DisplayMatrix[2] = str;
			},
			Add: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				//转为二维数组
				var matrixA = new Array();
				this.Transfer(matrixA, A);
				var matrixB = new Array();
				this.Transfer(matrixB, B);
				var matrixC = new Array();
				this.Claim2D(matrixC, rowa, columnb);
				//实际算法部分
				for (var i = 0; i < rowa; i++) {
					for (var j = 0; j < columnb; j++) {
						matrixC[i][j] = matrixA[i][j] + matrixB[i][j];
					}
				}
				this.ParseResult(matrixC, rowa, columna);
			},
			Sub: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				//转为二维数组
				var matrixA = new Array();
				this.Transfer(matrixA, A);
				var matrixB = new Array();
				this.Transfer(matrixB, B);
				var matrixC = new Array();
				this.Claim2D(matrixC, rowa, columnb);
				//实际算法部分
				for (var i = 0; i < rowa; i++) {
					for (var j = 0; j < columna; j++) {
						matrixC[i][j] = matrixA[i][j] - matrixB[i][j];
					}
				}
				this.ParseResult(matrixC, rowa, columna);
			},
			Time: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				//转为二维数组
				var matrixA = new Array();
				this.Transfer(matrixA, A);
				var matrixB = new Array();
				this.Transfer(matrixB, B);
				var matrixC = new Array();
				this.Claim2D(matrixC, rowa, columnb);
				//实际算法部分
				for (var i = 0; i < rowa; i++) {
					for (var j = 0; j < columnb; j++) {
						for (var k = 0; k < columna; k++) {
							matrixC[i][j] = matrixC[i][j] + matrixA[i][k] * matrixB[k][j];
						}
					}
				}
				this.ParseResult(matrixC, rowa, columnb);
			},
			Scalar: function() {
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				if (true) {
					//转为二维数组
					var matrixA = new Array();
					this.Transfer(matrixA, A);
					var matrixC = new Array();
					this.Claim2D(matrixC, rowa, columna);
					//实际算法部分
					for (var i = 0; i < rowa; i++) {
						for (var j = 0; j < columna; j++) {
							matrixC[i][j] = this.c * matrixA[i][j];
						}
					}
					this.ParseResult(matrixC, rowa, columna);
				}
			},
			Transpose: function() {
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				if (true) {
					//转为二维数组
					var matrixA = new Array();
					this.Transfer(matrixA, A);
					var matrixC = new Array();
					this.Claim2D(matrixC, columna, rowa);
					//实际算法部分
					for (var i = 0; i < rowa; i++) {
						for (var j = 0; j < columna; j++) {
							matrixC[j][i] = matrixA[i][j];
						}
					}
					this.ParseResult(matrixC, columna, rowa);
				}
			},
			Determinant: function() {
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var dim = rowa;
				var result = 0;
				this.dim = dim;
				//声明二维数组
				var matrixA = new Array();
				this.Transfer(matrixA, A);
				//实际算法
				result = this.det(matrixA, dim);
				//输出结果匹配为二维矩阵
				let mat = new Array();
				mat[0] = new Array();
				mat[0][0] = result;
				this.ParseResult(mat, 1, 1);
			},
			exchange: function(matrix, i1, i2) {
				var temp = new Array();
				temp = matrix[i1];
				matrix[i1] = matrix[i2];
				matrix[i2] = temp;
				this.MatrixDispose_2D(matrix, this.dim, this.dim);
			}, //两行对换
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
					this.MatrixDispose_2D(matrix, dim, dim);
				}
				//this.MatrixDispose_2D(matrix,dim,dim);
				return sign * last_element * this.det(matrix, dim - 1);
			},
			lowerdim: function(matrixA, row, column, ai, aj) {
				//1 2 3 4 5 6 8 9 7
				//ai,aj:代数余子式的i，j
				//bi,bj:控制新的矩阵b行列
				//i,j:控制matrixA的循环
				var matrixB = new Array();
				this.Claim2D(matrixB, row - 1, column - 1);
				var bi = 0;
				var bj = 0;
				for (var i = 0; i < row; i++) {
					if (i != ai) {
						for (var j = 0; j < column; j++) {
							if (j != aj) {
								matrixB[bi][bj] = matrixA[i][j];
								bj++;
							}
						}
						bi++;
					}
					bj = 0;
				}
				let obj = {};
				obj.matrixa = matrixA;
				obj.matrixb = matrixB;
				obj.explain = "的余子式："
				this.InverProgress.push(obj);

				return matrixB;
			}, //matrixB为matrixA[i][j]的余子式
			//1 2 1 1 -2 -1 0 1 0
			inver: function(matrixA, row, column, A) {
				var dim = row - 1;
				var matrixDet = new Array();
				this.Transfer(matrixDet, A);
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						matrixDet[i][j] = this.det(this.lowerdim(matrixA, row, column, i, j), dim);

						if ((i + j) % 2 != 0) {
							matrixDet[i][j] *= -1;
						}

					}
				}
				return matrixDet;
			}, //求出各余子式的行列式
			Inversion: function() {
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var dim = rowa;
				var result = 0;
				//声明二维数组
				var matrixA = new Array();
				this.Transfer(matrixA, A);
				var matrixC = new Array();
				this.Claim2D(matrixC, rowa, columna);
				//实际算法
				//A^-1=(1/|A|)*adjA

				matrixC = this.inver(matrixA, rowa, columna, A);
				var matrixCt = new Array();
				this.Claim2D(matrixCt, rowa, columna);
				for (var i = 0; i < rowa; i++) {
					for (var j = 0; j < columna; j++) {
						matrixCt[j][i] = matrixC[i][j];
					}
				} //转置matrixC为代数余子式（adjA)
				var det = this.det(matrixA, dim);
				for (i = 0; i < rowa; i++) {
					for (j = 0; j < columna; j++) {
						matrixC[i][j] = (1 / det) * matrixCt[i][j];
					}
				}
				this.ResultMatrix = this.ParseResult(matrixC, rowa, columna);
			},
			Pow: function() {
				var A = this.a - 1;
				var c1 = this.c;
				var row = this.matrix[A].row;
				var column = this.matrix[A].column;
				//转为二维数组
				var matrixA = new Array();
				this.Transfer(matrixA, A);
				var matrixA2 = new Array();
				this.Transfer(matrixA2, A);
				var matrixC = new Array();
				this.Claim2D(matrixC, row, column);
				if (this.c == 1) {
					for (i = 0; i < row; i++) {
						for (j = 0; j < column; j++) {
							matrixC[i][j] = matrixA[i][j];
						}
					}
				} else {
					//1 2 3 4
					//实际算法部分
					while (--this.c) {
						for (var i = 0; i < row; i++) {
							for (var j = 0; j < column; j++) {
								for (var k = 0; k < column; k++) {
									matrixC[i][j] += matrixA2[i][k] * matrixA[k][j];
								}
							}
						}

						for (i = 0; i < row; i++) {
							for (j = 0; j < column; j++) {
								matrixA[i][j] = matrixC[i][j];
								matrixC[i][j] = 0;
							}
						}
					}
					for (i = 0; i < row; i++) {
						for (j = 0; j < column; j++) {
							matrixC[i][j] = matrixA[i][j];
						}
					}
				}
				this.ResultMatrix = this.ParseResult(matrixC, row, column);
				this.c = c1;
			},
		}
	}
</script>

<style>
	@import "../../common/Detail.css";
</style>
