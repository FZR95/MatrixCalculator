<template>
	<view class="tabbar">
		<view class="column">
		<view class="r">
			<button class="button bottom shrink" @click="Method=1;Emit()">+</button>
			<button class="button bottom shrink" @click="Method=3;Emit()">×</button>
			<button class="button bottom shrink" @click="Method=5;Emit()">转置</button>
			<button class="button bottom shrink" @click="Method=9;Emit()">求逆</button>
			<button class="button bottom colored shrink" @click="Method=7;Emit()">关于</button>
		</view>
		<view class="r">
			<button class="button" @click="Method=2;Emit()">-</button>
			<button class="button" @click="Method=4;Emit()">数乘</button>
			<button class="button" @click="Method=6;Emit()">行列式</button>
			<button class="button colored shrink" @click="Method=8;Emit()">帮助</button>
		</view>
		</view>
		
	</view>
</template>

<script>
	export default{
		name:'Tabbar',
		data(){
			return{
				Method:1
			}
		},
		methods:{
			Emit:function(){
				this.$emit('Method', this.Method);
			}
		}
		
	}
</script>

<style>
	.tabbar{
		display: flex;	
		flex-direction: row;
		margin:0;
		padding: 0;
		background-color: #F7F7FA;
		position: fixed;
		bottom: 0upx;
		width: 100%;
		
	}
	.r{
		display: flex;
		flex-direction: row;
		width: 100%;
	}
	.column{
		display: flex;
		flex-direction: column;
		width: 100%;
	}
	.button{
		color: #000000;
		border-radius: 10upx;
		text-align: center;
		width: 25%;
		margin-right: 10upx;
	}
	.button:last-child{
		margin-right: 0upx;
	}
	.bottom{
		margin-bottom: 10upx;
	}
	.colored{
		color: #576b95;
	}
	.shrink{
		width: 20%;
	}
</style>
