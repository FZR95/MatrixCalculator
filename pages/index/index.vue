<template>
	<keep-alive>
		<view class="content">
			<view class="welcome" @click="Cheerup">
				{{welcometext}}
			</view>
			<view>
				<view class="panel">
					<image src="../../static/history.png" @click="Viewhist"></image>
					<view class="rcview">
						<view>行数：<input v-model="row" type="number" /></view>
						<view>列数：<input v-model="column" type="number" /></view>
					</view>
					<button @click="Create()">添加</button>
				</view>

				sss <view class="MatrixPanel">
					<view v-if="welcometext" class="welcometext"></view>
					<matrix-plate v-for="(item, index) in matrix" v-bind:key="index" :matrix="matrix[index]" :order="index" v-if="matrix[index].show"
					 @Matrixtrans="matrixtrans" @Deltem="Delete"></matrix-plate>
				</view>
				<view class="sidebar" :class="{sidebarshow:sidebar1}" @click="sidebar1=!sidebar1">
					<image src="../../static/left.png" class="reverse"></image>
					<button @click="toEquation">求 解 方 程</button>
				</view>
				<view class="sidebar" style="bottom: 350rpx;" :class="{sidebarshow:sidebar2}" @click="sidebar2=!sidebar2">
					<image src="../../static/help.png"></image>
					<button @click="About">帮助与其他</button>
				</view>
				<view class="tabbar">
					<view class="row">
						<button @click="FMethod(1)">+</button>
						<button @click="FMethod(3)">×</button>
						<button @click="FMethod(5)">转置</button>
						<button @click="FMethod(9)">求逆</button>
					</view>
					<view class="row">
						<button @click="FMethod(2)">-</button>
						<button @click="FMethod(4)">数乘</button>
						<button @click="FMethod(6)">行列式</button>
						<button @click="FMethod(7)">求幂</button>
					</view>
				</view>
			</view>
		</view>
		<!-- Popups -->
		<!-- Single -->
		<uni-popup type="center" ref="singlepopup" :mask-click="false">
			<view class="POPUP">
				<view class="header">需要运算的矩阵：</view>
				<view class="content">
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" />
				</view>
				<button @click="ok()" type="primary">确 定</button>
			</view>
		</uni-popup>
		<!-- Double -->
		<uni-popup type="center" ref="doublepopup" :mask-click="false">
			<view class="POPUP">
				<view class="header">参与运算的矩阵：</view>
				<view class="content">
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" />
					<text class="pop_juhzen"> {{methodtext}} 矩阵</text>
					<input type="number" v-model="b" />
				</view>
				<button @click="ok()" type="primary">确 定</button>
			</view>
		</uni-popup>
		<!-- Scarlar -->
		<uni-popup type="center" ref="scarlarpopup" :mask-click="false">
			<view class="POPUP">
				<view class="header">矩阵数乘：</view>
				<view class="content">
					<input v-model="c" />
					<text class="pop_juhzen"> {{methodtext}} 矩阵</text>
					<input type="number" v-model="b" />
				</view>
				<button @click="ok()" type="primary">确 定</button>
			</view>
		</uni-popup>
		<!-- Exponentiation -->
		<uni-popup type="center" ref="powpopup" :mask-click="false">
			<view class="POPUP">
				<view class="header">矩阵求幂：</view>
				<view class="content">
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" />
					<text class="pop_juhzen">{{methodtext}}</text>
					<input v-model="c" type="number" />
				</view>
				<button @click="ok()" type="primary">确 定</button>
			</view>
		</uni-popup>
		<!-- Warning -->
		<uni-popup type="center" ref="warnpopup">
			<view class="POPUP">
				<view class="header">{{warning}}</view>
				<button @click="close()" type="primary">知 道 了</button>
			</view>
		</uni-popup>
		<!-- Result -->
		<uni-popup type="center" ref="resultpopup" :maskClick="false">
			<view class="POPUP">
				<view class="header">结果矩阵如下：</view>
				<view class="content">
					<text v-if="Matrix">{{ResultMatrix}}</text>
					<text v-else>{{Result}}</text>
				</view>
				<view style="display: flex;justify-content: space-between;">
					<button @click="Detail()" style="width: 30%;">详情</button>
					<button @click="Copy()" style="width: 30%;">复制</button>
					<button @click="ResultOK()" style="width: 30%;">确定</button>

				</view>
			</view>
		</uni-popup>
		<!-- History -->
		<uni-popup type="center" ref="historypopup">
			<view class="POPUP">
				<view class="header">历史记录</view>
				<view v-for="(item,index) in matrix" :key="index" class="histview" @click="Drawback(index)" v-if="item.show==false">
					<view>矩阵{{item.id+1}} {{item.row}}×{{item.column}}</view>
					<image src="../../static/drawback.png"></image>
				</view>
			</view>
		</uni-popup>
		</view>
	</keep-alive>

</template>

<script>
	import MatrixPlate from "../../components/MatrixPlate/MatrixPlate.vue"
	import uniPopup from "../../components/uni-popup/uni-popup.vue"
	export default {
		components: {
			MatrixPlate,
			uniPopup,
		},
		onShareAppMessage: function(res) {
			return {
				title: '欢迎使用矩阵计算工具',
				path: '/pages/index/index',
				imageUrl: '/static/gh_98d64701f7a7_430.jpg'
			}
		},
		onLoad() {
			//this.$refs.resultpopup.open();
			var time = new Date();
			let h = time.getHours();
			if (h >= 4 && h <= 7) {
				this.welcometext = '🌄伴着清晨的第一缕阳光，你早已坐在桌前，为你加油！'
			} else if (h >= 4 || h <= 4) {
				this.welcometext = '🌙你深夜还在努力的样子，真美'
			}
		},
		data() {
			return {
				welcometext: '',
				//矩阵数据
				matrix: [],
				//输入记录
				num: 0, //记录矩阵个数
				row: "2", //input中的行数，传入matrixplate中
				column: "2",
				//运算输入
				Method: 0,
				a: 1, //运算矩阵A
				b: 2, //运算矩阵B
				c: 1, //数乘数

				//popup控制
				warning: '',
				methodtext: '',

				//关于和帮助
				help: false,
				about: false,

				//结果Popup相关
				Matrix: false,
				ResultMatrix: [
					[]
				],
				Result: 0,
				sidebar1: false,
				sidebar2: false
			}
		},
		methods: {
			Cheerup(){
				uni.navigateTo({
					url:'../cheerup/cheerup'
				})
			},
			toEquation() {
				uni.navigateTo({
					url: '../equation/equation'
				})
			},
			Viewhist() {
				this.$refs.historypopup.open();
			},
			Drawback(index) {
				this.matrix[index].show = true;
			},
			//矩阵管理
			Create: function() {
				//创建数组对象
				var obj = {};
				obj.id = this.num++;
				obj.show = true;
				obj.m = [];
				obj.calc = [];
				obj.row = this.row;
				obj.column = this.column;
				this.matrix.push(obj);
			},
			Delete: function(number) {
				this.matrix[number].show = false;
			},
			Detail() {
				/* let obj = {
					matrixa: this.matrix[this.a - 1],
					matrixb: this.matrix[this.b - 1],
					c: this.c,
					method: this.Method
				} */
				//uni.$emit('DetailPage', obj);
				getApp().globalData.matrix[0] = this.matrix[this.a - 1];
				getApp().globalData.matrix[1] = this.matrix[this.b - 1];
				getApp().globalData.method = this.Method - 1;
				getApp().globalData.c = this.c;
				uni.navigateTo({
					url: '/pages/detail/detail'
				})
			},
			//子组件传来的$emit Matrix
			matrixtrans: function(msg) {
				var order = msg[0]
				msg.shift();
				this.matrix[order].calc = msg;
			},
			//Popup中的确定键
			ok: function() {
				this.$refs.singlepopup.close();
				this.$refs.doublepopup.close();
				this.$refs.scarlarpopup.close();
				this.$refs.powpopup.close();
				switch (this.Method) {
					case 1:
						{
							this.Add();
							break;
						};
					case 2:
						{
							this.Sub();
							break;
						};
					case 3:
						{
							this.Time();
							break;
						};
					case 4:
						{
							this.Scalar();
							break;
						};
					case 5:
						{
							this.Transpose();
							break;
						};
					case 6:
						{
							this.Determinant();
							break;
						};
					case 7:
						{
							this.Pow();
							break;
						};
					case 9:
						{
							this.Inversion();
						}
				}
			},
			close: function() {
				this.$refs.singlepopup.close();
				this.$refs.doublepopup.close();
				this.$refs.scarlarpopup.close();
				this.$refs.warnpopup.close();
				this.$refs.resultpopup.close();
			},
			//分配运算：
			FMethod: function(msg) {
				this.Method = msg;
				switch (this.Method) {
					case 1:
						{
							this.methodtext = "+";
							this.$refs.doublepopup.open();
							break;
						};
					case 2:
						{
							this.methodtext = "-";
							this.$refs.doublepopup.open();
							break;
						};
					case 3:
						{
							this.methodtext = "×";
							this.$refs.doublepopup.open();
							break;
						};
					case 4:
						{
							this.methodtext = "×";
							this.$refs.scarlarpopup.open();
							break;
						};
					case 5:
						{
							this.$refs.singlepopup.open();
							break;
						};
					case 6:
						{
							this.$refs.singlepopup.open();
							break;
						};
					case 7:
						{
							this.methodtext = "^";
							this.$refs.powpopup.open();
							break;
						};
					case 9:
						{
							this.$refs.singlepopup.open();
							break;
						};
				}
			},

			ResultOK: function() {
				this.close();
				this.ResultMatrix = " ";
				this.c = 1;
			},

			Copy: function() {
				uni.setClipboardData({
					data: this.ResultMatrix,
					success: function() {
						uni.showToast({
							title: "已复制至剪贴板"
						})
					}
				});
			},
			//运算部分：
			// 1 2 3 4 5 6
			Warning1: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				if (rowa == rowb && columna == columnb) {
					return true;
				} else {
					this.$refs.warnpopup.open();
					this.warning = "两矩阵行列数需相等";
					return false;
				}
			},
			Warning2: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				if (columna == rowb) {
					return true;
				} else {
					this.$refs.warnpopup.open();
					this.warning = "两矩阵相乘需满足A列数等于B行数";
					return false;
				}
			},
			Warning3: function() {
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				if (rowa == columna) {
					return true;
				} else {
					this.$refs.warnpopup.open();
					this.warning = "矩阵行列数需相等";
					return false;
				}
			},

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

			ParseResult: function(matrix, row, column) {
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						matrix[i][j] = matrix[i][j].toFixed(3);
					}
				}
				var string = '';
				for (var i = 0; i < row; i++) {
					for (var j = 0; j < column; j++) {
						if (j < column - 1)
							string = string + matrix[i][j] + ' ';
						else
							string = string + matrix[i][j];
					}
					string = string + "\n";
				}
				return string;
			}, //将结果矩阵转换为字符串

			Add: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				if (this.Warning1()) {
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
					this.ResultMatrix = this.ParseResult(matrixC, rowa, columna); //调用函数返回结果矩阵字符串
					this.Matrix = true; //结果为矩阵，popup的v-if
					this.$refs.resultpopup.open(); //结果矩阵Popup
				}
			},
			Sub: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				if (this.Warning1()) {
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
					this.ResultMatrix = this.ParseResult(matrixC, rowa, columna);
					this.Matrix = true;
					this.$refs.resultpopup.open();
				}
			},
			Time: function() {
				var A = this.a - 1;
				var B = this.b - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var rowb = this.matrix[B].row;
				var columnb = this.matrix[B].column;
				if (this.Warning2()) {
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
					this.ResultMatrix = this.ParseResult(matrixC, rowa, columnb);
					this.Matrix = true;
					this.$refs.resultpopup.open();
				}
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
					this.ResultMatrix = this.ParseResult(matrixC, rowa, columna);
					this.Matrix = true;
					this.$refs.resultpopup.open();
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
					this.ResultMatrix = this.ParseResult(matrixC, columna, rowa);
					this.Matrix = true;
					this.$refs.resultpopup.open();
				}
			},
			Determinant: function() {
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var dim = rowa;
				var result = 0;
				if (this.Warning3()) {
					//声明二维数组
					var matrixA = new Array();
					this.Transfer(matrixA, A);
					//实际算法
					result = this.det(matrixA, dim);
					this.Result = result.toFixed(3);
					this.Matrix = false;
					this.$refs.resultpopup.open();
				}
			},
			exchange: function(matrix, i1, i2) {
				var temp = new Array();
				temp = matrix[i1];
				matrix[i1] = matrix[i2];
				matrix[i2] = temp;
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
				}
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
				//1 2 3 4 5 6 7 8 0
				var A = this.a - 1;
				var rowa = this.matrix[A].row;
				var columna = this.matrix[A].column;
				var dim = rowa;
				var result = 0;
				if (this.Warning3()) {
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
					this.Matrix = true;
					this.$refs.resultpopup.open();
				}
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
				if (this.Warning3()) {
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
					this.Matrix = true;
					this.$refs.resultpopup.open();
					this.c = c1;
				}
			},
			About: function() {
				uni.navigateTo({
					url: '../about/about'
				})
			}
		}
	}
</script>

<style>
	@import "../../common/Main.css";
	@import "../../common/Matrix.css";
	@import "../../common/Popup.css";

	.texts {
		height: 400upx;
		text-align: left;
		white-space: pre-wrap;
		overflow: scroll;
	}

	.red {
		color: #e64340;
	}
</style>
