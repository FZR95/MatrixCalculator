<template>
	<keep-alive>
		<view class="content">
			<view>
				<view class="CreateView">
					<view class="rcView">
						<view class="rc">行数：<input type="number" v-model="row" class="input" /></view>
						<view class="rc">列数：<input type="number" v-model="column" class="input" /></view>
					</view>
					<button @click="Create()" class="CreateButton">添加</button>
				</view>
				<view class="MatrixPanel">
					<view v-if="welcometext" class="welcometext">代码已开源，详见<关于>。</view>
					<view v-for="(item, index) in matrix" v-bind:key="index">
						<matrix-plate :matrix="matrix[index]" :order="index" v-if="matrix[index].show" @Matrixtrans="matrixtrans" @Deltem="Delete"></matrix-plate>
					</view>
				</view>
			</view>
			<!-- Popups -->
			<!-- Single -->
			<uni-popup type="center" :show="p1" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">需要运算的矩阵：</text>
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" class="pop_input" />
					<button @click="ok()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Double -->
			<uni-popup type="center" :show="p2" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">参与运算的矩阵：</text>
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" class="pop_input" />
					<text class="pop_juhzen">{{methodtext}}矩阵</text>
					<input type="number" v-model="b" class="pop_input" />
					<button @click="ok()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Scarlar -->
			<uni-popup type="center" :show="p3" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">矩阵数乘：</text>
					<input type="digit" v-model="c" class="pop_input" />
					<text class="pop_juhzen">{{methodtext}}矩阵</text>
					<input type="number" v-model="b" class="pop_input" />
					<button @click="ok()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Warning -->
			<uni-popup type="center" :show="pw" :custom="true">
				<view class="popup">
					<text class="pop_title">{{warning}}</text>
					<button @click="close()">知道了</button>
				</view>
			</uni-popup>
			<!-- Result -->
			<uni-popup type="center" :show="result" :custom="true" :maskClick=false>
				<view class="popup">
					<text class="pop_title">结果矩阵如下：</text>
					<text v-if="Matrix" class="pop_showresult">{{ResultMatrix}}</text>
					<text class="pop_showresult" v-else>{{Result}}</text>
					<button @click="Copy()" class="pop_button pop_row">复制</button>
					<button @click="ResultOK()" class="pop_button pop_row">确定</button>

				</view>
			</uni-popup>
			<!-- About -->
			<uni-popup type="center" :show="about" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">关 于</text>
					<scroll-view scroll-y="true" show-scrollbar="true" style="max-height: 500upx;">
						<rich-text class="pop_juhzen texts">{{text_about}}</rich-text>
					</scroll-view>
					<button @click="close()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Help -->
			<uni-popup type="center" :show="help" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">帮 助</text>
					<scroll-view scroll-y="true" show-scrollbar="true" style="max-height: 500upx;">
						<rich-text class="pop_juhzen texts">{{text_help}}</rich-text>
						<rich-text class="pop_juhzen texts red">{{text_help_red}}</rich-text>
					</scroll-view>
					<button @click="close()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<view class="blank"> </view>
			<Tabbar @Method="FMethod"></Tabbar>
		</view>
	</keep-alive>

</template>

<script>
	import Tabbar from "../../components/Tabbar/Tabbar.vue"
	import MatrixPlate from "../../components/MatrixPlate/MatrixPlate.vue"
	import uniPopup from "@/components/uni-popup/uni-popup.vue"
	//sync,async
	export default {
		components: {
			Tabbar,
			MatrixPlate,
			uniPopup,
		},
		data() {
			return {
				welcometext: true,
				//矩阵数据
				matrix: [],

				//输入记录
				num: 0, //记录矩阵个数
				row: "2", //input中的行数，传入matrixplate中
				column: "2",
				text_about: '1.1版本更新日志：\r\n[新增]矩阵求逆；\r\n[移除]小数模式，默认精确至小数点后三位；\r\n[修改]不再限制所创建的矩阵数量；\r\n[修改]不再限制矩阵元素个数；\r\n[优化]列数增多后的显示效果。\r\n代码经过了大幅整改，如有bug敬请谅解，您的反馈将是对我的最大支持！如有问题，敬请联系Email:\r\nmatrix826@163.com  \r\n特别感谢酷安网友@美得有声有色 为本小程序陆续提供的各种建议和存在的问题。',
				text_help: '欢迎使用本矩阵计算工具，接下来您将了解到本小程序的使用方法和小提示。\r\n1.使用方法:\r\nStep1:将想要添加矩阵的行、列填入应用最上方的输入框中，然后点击添加按钮即可。\r\nStep2:接着，点击响应矩阵的编辑按钮以进行编辑。请按照a11,a12...,a21,a22的顺序填写，中间用空格或者回车隔开，建议您依照矩阵的样式在行尾换行，更加直观便于编辑。示例填写：\r\n1 2\r\n3 4\r\nStep3:单击应用底部的按钮进行相应运算，在弹出框中填入您想要运算的矩阵或者数乘数。\r\nStep4:若您想要复制运算出的结果矩阵，或者让其参与下一步运算，点击复制按钮，创建一个相应的新矩阵，在编辑界面中粘贴即可。',
				text_help_red: '如果您发现输入过后呈现出的矩阵有一项为0，请注意矩阵的输入仅支持英文状态下的空格，且两项之间仅由单个空格或回车隔开，空格和回车在一起也是不可以的。',
				//运算输入
				Method: 0,
				a: 1, //运算矩阵A
				b: 2, //运算矩阵B
				c: 1, //数乘数

				//popup控制
				p1: false, //单矩阵
				p2: false, //双矩阵
				p3: false, //数乘矩阵
				pw: false, //错误
				result: false, //结果矩阵
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
			}
		},
		methods: {
			//矩阵管理
			Create: function() {
				this.welcometext = false;
				//创建数组对象
				var obj = {};
				obj.id = this.num;
				obj.show = true;
				obj.m = [];
				obj.calc = [];
				obj.row = this.row;
				obj.column = this.column;
				this.matrix.push(obj);
				this.num++;
			},
			Delete: function(number) {
				this.matrix[number].show = false;
				this.matrix[number].calc = [0];
				this.matrix[number].m = [];
			},
			//子组件传来的$emit Matrix
			matrixtrans: function(msg) {
				var order = msg[0]
				msg.shift();
				this.matrix[order].calc = msg;
			},
			//Popup中的确定键
			ok: function() {
				this.p1 = false;
				this.p2 = false;
				this.p3 = false;
				this.pw = false;
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
							this.Transpose()();
							break;
						};
					case 6:
						{
							this.Determinant();
							break;
						};
					case 9:
						{
							this.Inversion();
						}
				}
			},
			close: function() {
				this.p1 = false;
				this.p2 = false;
				this.p3 = false;
				this.pw = false;
				this.help = false;
				this.about = false;
			},
			//分配运算：
			FMethod: function(msg) {
				this.Method = msg;
				switch (this.Method) {
					case 1:
						{
							this.methodtext = "+";
							this.p2 = true;
							break;
						};
					case 2:
						{
							this.methodtext = "-";
							this.p2 = true;
							break;
						};
					case 3:
						{
							this.methodtext = "×";
							this.p2 = true;
							break;
						};
					case 4:
						{
							this.methodtext = "×";
							this.p3 = true;
							break;
						};
					case 5:
						{
							this.p1 = true;
							break;
						};
					case 6:
						{
							this.p1 = true;
							break;
						};
					case 7:
						{
							this.About();
							break;
						};
					case 8:
						{
							this.Help();
							break;
						};
					case 9:
						{
							this.p1 = true;
							break;
						};
				}
			},

			ResultOK: function() {
				this.result = false;
				this.ResultMatrix = " ";
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
					this.pw = true;
					this.warning = "两矩阵行列数需相等";
					console.log("warn");
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
					console.log("true");
					return true;
				} else {
					this.pw = true;
					this.warning = "两矩阵相乘需满足A列数等于B行数";
					console.log("warn");
					console.log("False");
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
					this.pw = true;
					this.warning = "矩阵行列数需相等";
					console.log("warn");
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
					this.result = true; //结果矩阵Popup
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
				}
			},
			About: function() {
				this.about = true;
			},
			Help: function() {
				this.help = true;
			},
		}
	}
</script>

<style>
	@import "../../common/Main.css";
	@import "../../common/Matrix.css";
	@import "../../common/Popup.css";

	.content {}

	.title {
		font-size: 36upx;
		color: #8f8f94;
	}

	.blank {
		display: block;
		height: 200upx;
		color: #FFFFFF;
	}

	.texts {
		height: 400upx;
		text-align: left;
		white-space: pre-wrap;
		overflow: scroll;
	}

	.red {
		color: #e64340;
	}

	.warn {
		width: 100%;
		align-items: center;
		text-align: center;
		font-size: 28upx;
	}
</style>
=======
<template>
	<keep-alive>
		<view class="content">
			<view>
				<view class="CreateView">
					<view class="rcView">
						<view class="rc">行数：<input type="number" v-model="row" class="input" /></view>
						<view class="rc">列数：<input type="number" v-model="column" class="input" /></view>
					</view>
					<button @click="Create()" class="CreateButton">添加</button>
				</view>
				<view class="MatrixPanel">
					<view v-if="welcometext" class="welcometext">紧急修复矩阵相乘时出现的问题，为此带来的不便敬请谅解。</view>
					<view v-for="(item, index) in matrix" v-bind:key="index">
						<matrix-plate :matrix="matrix[index]" :order="index" v-if="matrix[index].show" @Matrixtrans="matrixtrans" @Deltem="Delete"></matrix-plate>
					</view>
				</view>
			</view>
			<!-- Popups -->
			<!-- Single -->
			<uni-popup type="center" :show="p1" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">需要运算的矩阵：</text>
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" class="pop_input" />
					<button @click="ok()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Double -->
			<uni-popup type="center" :show="p2" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">参与运算的矩阵：</text>
					<text class="pop_juhzen">矩阵</text>
					<input type="number" v-model="a" class="pop_input" />
					<text class="pop_juhzen">{{methodtext}}矩阵</text>
					<input type="number" v-model="b" class="pop_input" />
					<button @click="ok()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Scarlar -->
			<uni-popup type="center" :show="p3" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">矩阵数乘：</text>
					<input type="digit" v-model="c" class="pop_input" />
					<text class="pop_juhzen">{{methodtext}}矩阵</text>
					<input type="number" v-model="b" class="pop_input" />
					<button @click="ok()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Warning -->
			<uni-popup type="center" :show="pw" :custom="true">
				<view class="popup">
					<text class="pop_title">{{warning}}</text>
					<button @click="close()">知道了</button>
				</view>
			</uni-popup>
			<!-- Result -->
			<uni-popup type="center" :show="result" :custom="true" :maskClick=false>
				<view class="popup">
					<text class="pop_title">结果矩阵如下：</text>
					<text v-if="Matrix" class="pop_showresult">{{ResultMatrix}}</text>
					<text class="pop_showresult" v-else>{{Result}}</text>
					<button @click="Copy()" class="pop_button pop_row">复制</button>
					<button @click="ResultOK()" class="pop_button pop_row">确定</button>

				</view>
			</uni-popup>
			<!-- About -->
			<uni-popup type="center" :show="about" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">关 于</text>
					<scroll-view scroll-y="true" show-scrollbar="true" style="max-height: 500upx;">
					<rich-text class="pop_juhzen texts">{{text_about}}</rich-text>
					</scroll-view>
					<button @click="close()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<!-- Help -->
			<uni-popup type="center" :show="help" :custom="true" :mask-click="false">
				<view class="popup">
					<text class="pop_title">帮 助</text>
					<scroll-view scroll-y="true" show-scrollbar="true" style="max-height: 500upx;">
						<rich-text class="pop_juhzen texts">{{text_help}}</rich-text>
						<rich-text class="pop_juhzen texts red">{{text_help_red}}</rich-text>
					</scroll-view>
					<button @click="close()" class="pop_button">确定</button>
				</view>
			</uni-popup>
			<view class="blank"> </view>
			<Tabbar @Method="FMethod"></Tabbar>
		</view>
	</keep-alive>

</template>

<script>
	import Tabbar from "../../components/Tabbar/Tabbar.vue"
	import MatrixPlate from "../../components/MatrixPlate/MatrixPlate.vue"
	import uniPopup from "@/components/uni-popup/uni-popup.vue"
	//sync,async
	export default {
		components: {
			Tabbar,
			MatrixPlate,
			uniPopup,
		},
		data() {
			return {
				welcometext:true,
				//矩阵数据
				matrix: [],

				//输入记录
				num: 0, //记录矩阵个数
				row: "2", //input中的行数，传入matrixplate中
				column: "2",
				text_about: '1.1版本更新日志：\r\n[新增]矩阵求逆；\r\n[移除]小数模式，默认精确至小数点后三位；\r\n[修改]不再限制所创建的矩阵数量；\r\n[修改]不再限制矩阵元素个数；\r\n[优化]列数增多后的显示效果。\r\n代码经过了大幅整改，如有bug敬请谅解，您的反馈将是对我的最大支持！如有问题，敬请联系Email:\r\nmatrix826@163.com  \r\n特别感谢酷安网友@美得有声有色 为本小程序陆续提供的各种建议和存在的问题。',
				text_help: '欢迎使用本矩阵计算工具，接下来您将了解到本小程序的使用方法和小提示。\r\n1.使用方法:\r\nStep1:将想要添加矩阵的行、列填入应用最上方的输入框中，然后点击添加按钮即可。\r\nStep2:接着，点击响应矩阵的编辑按钮以进行编辑。请按照a11,a12...,a21,a22的顺序填写，中间用空格或者回车隔开，建议您依照矩阵的样式在行尾换行，更加直观便于编辑。示例填写：\r\n1 2\r\n3 4\r\nStep3:单击应用底部的按钮进行相应运算，在弹出框中填入您想要运算的矩阵或者数乘数。\r\nStep4:若您想要复制运算出的结果矩阵，或者让其参与下一步运算，点击复制按钮，创建一个相应的新矩阵，在编辑界面中粘贴即可。',
				text_help_red: '如果您发现输入过后呈现出的矩阵有一项为0，请注意矩阵的输入仅支持英文状态下的空格，且两项之间仅由单个空格或回车隔开，空格和回车在一起也是不可以的。',
				//运算输入
				Method: 0,
				a: 1, //运算矩阵A
				b: 2, //运算矩阵B
				c: 1, //数乘数

				//popup控制
				p1: false, //单矩阵
				p2: false, //双矩阵
				p3: false, //数乘矩阵
				pw: false, //错误
				result: false, //结果矩阵
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
			}
		},
		methods: {
			//矩阵管理
			Create: function() {
				this.welcometext=false;
				//创建数组对象
				var obj = {};
				obj.id = this.num;
				obj.show = true;
				obj.m = [];
				obj.calc = [];
				obj.row = this.row;
				obj.column = this.column;
				this.matrix.push(obj);
				this.num++;
			},
			Delete: function(number) {
				this.matrix[number].show = false;
				this.matrix[number].calc = [0];
				this.matrix[number].m = [];
			},
			//子组件传来的$emit Matrix
			matrixtrans: function(msg) {
				var order = msg[0]
				msg.shift();
				this.matrix[order].calc = msg;
			},
			//Popup中的确定键
			ok: function() {
				this.p1 = false;
				this.p2 = false;
				this.p3 = false;
				this.pw = false;
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
							this.Transpose()();
							break;
						};
					case 6:
						{
							this.Determinant();
							break;
						};
					case 9:
						{
							this.Inversion();
						}
				}
			},
			close: function() {
				this.p1 = false;
				this.p2 = false;
				this.p3 = false;
				this.pw = false;
				this.help = false;
				this.about = false;
			},
			//分配运算：
			FMethod: function(msg) {
				this.Method = msg;
				switch (this.Method) {
					case 1:
						{
							this.methodtext = "+";
							this.p2 = true;
							break;
						};
					case 2:
						{
							this.methodtext = "-";
							this.p2 = true;
							break;
						};
					case 3:
						{
							this.methodtext = "×";
							this.p2 = true;
							break;
						};
					case 4:
						{
							this.methodtext = "×";
							this.p3 = true;
							break;
						};
					case 5:
						{
							this.p1 = true;
							break;
						};
					case 6:
						{
							this.p1 = true;
							break;
						};
					case 7:
						{
							this.About();
							break;
						};
					case 8:
						{
							this.Help();
							break;
						};
					case 9:
						{
							this.p1 = true;
							break;
						};
				}
			},

			ResultOK: function() {
				this.result = false;
				this.ResultMatrix = " ";
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
					this.pw = true;
					this.warning = "两矩阵行列数需相等";
					console.log("warn");
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
					console.log("true");
					return true;
				} else {
					this.pw = true;
					this.warning = "两矩阵相乘需满足A列数等于B行数";
					console.log("warn");
					console.log("False");
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
					this.pw = true;
					this.warning = "矩阵行列数需相等";
					console.log("warn");
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
					this.result = true; //结果矩阵Popup
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
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
					this.result = true;
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
					}//转置matrixC为代数余子式（adjA)
					for (i = 0; i < rowa; i++) {
						for (j = 0; j < columna; j++) {
							matrixC[i][j] = 1 / this.det(matrixA, dim) * matrixCt[i][j];
						}
					}
					this.ResultMatrix = this.ParseResult(matrixC, rowa, columna);
					this.Matrix = true;
					this.result = true;
				}
			},
			About: function() {
				this.about = true;
			},
			Help: function() {
				this.help = true;
			},
		}
	}
</script>

<style>
	@import "../../common/Main.css";
	@import "../../common/Matrix.css";
	@import "../../common/Popup.css";

	.content {}

	.title {
		font-size: 36upx;
		color: #8f8f94;
	}

	.blank {
		display: block;
		height: 200upx;
		color: #FFFFFF;
	}

	.texts {
		height: 400upx;
		text-align: left;
		white-space: pre-wrap;
		overflow: scroll;
	}

	.red {
		color: #e64340;
	}

	.warn {
		width: 100%;
		align-items: center;
		text-align: center;
		font-size: 28upx;
	}
</style>

