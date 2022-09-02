<template>
	<view class="content">
		
		<!-- 状态栏 -->
		<view v-if="list.length!==0" class="todo-header">
			<!-- 状态栏的左侧 -->
			<view class="todo-header_left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			
			<!-- 状态栏的右侧 -->
			<view class="todo-header_right">
				<!-- 选项卡 -->
				<view class="todo-header_right_item " :class="{'active-tab':activeIndex === 0}" @click="tab(0)">全部</view>
				<view class="todo-header_right_item" :class="{'active-tab':activeIndex === 1}" @click="tab(1)">待办</view>
				<view class="todo-header_right_item" :class="{'active-tab':activeIndex === 2}" @click="tab(2)">已完成</view>
			</view>
		</view>
		
		<!-- 缺省页面 没有数据时显示的内容-->
		<view v-if="list.length === 0" class="default">
			<view class="image-default">
				<image src="../../static/study.png" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="default-info_text">您还没有今日的学习计划</view>
				<view class="default-info_text">点击下方+号就可以创建哦</view>
			</view>
		</view>
		
		
		<!-- 内容区域 -->
		<view v-else class="todo-content">
			<view class="todo-list" :class="{'todo--finish':item.checked}" v-for="(item,index) in listData"  :key="index" @click="finish(item.id)">
				<view class="todo-list_checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list_content">{{item.content}}</view>
			</view>
		</view>
		

		
		<!-- 创建按钮 创建新的备忘录 -->
		<view class="create-todo" @click="create">
			<text class="iconfont icon-jia" :class="{'create-todo-active':textShow}"></text>
		</view>
		
		<!-- 备忘录的内容输入 -->
		<view v-if="active" class="create-content" :class="{'create-show':textShow}">
			<view class="create-content-box" >
				<view class="create-input">
					<input type="text" v-model="content_value"  placeholder="请输入您的学习计划"/>
				</view>
				<!-- 发布按钮 -->
				<view class="create-button" @click="add">
					创建
				</view>
			</view>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[],
				active:false,
				content_value:'',
				activeIndex:0,
				text:'全部',
				textShow:false
			}
		},
		onLoad() {

		},
		computed:{
			
			listData(){
				let list=JSON.parse(JSON.stringify(this.list))
				let newList=[]
				//点击全部
				if(this.activeIndex===0){
					this.text='全部'
					return list
					}
				//点击待办
				if(this.activeIndex===1){
					//checked==false
					list.forEach((item)=>{
						if(!item.checked){
							newList.push(item)
						}
					})
					this.text='待办'
					return newList
					}
				//点击已完成
				if(this.activeIndex===2){
					//checked==true
					list.forEach((item)=>{
						if(item.checked){
							newList.push(item)
						}
					})
					this.text='已完成'
					return newList
					}
				
				return []
			},
		},
		methods: {
			// 创建新的备忘录按钮事件,打开输入框
			create(){
				if (this.active) {
					this.close()
				} else {
					this.open()
				}
			},
			// 打开 动画
			open() {
				this.active = true
				this.$nextTick(() => {
					setTimeout(() => {
						this.textShow = true
					}, 50)
				})
			},
			// 关闭动画
			close() {
				this.textShow = false
				this.$nextTick(()=>{
					setTimeout(() => {
						this.active = false
					}, 350)
				})
			},
			
			//发布
			add(){
				if(this.content_value ===''){
					uni.showToast({
						title:"请输入内容！",
						icon:"none"
					})
					return
				}
				this.list.unshift({
					id:'id'+new Date().getTime(),
					content:this.content_value,
					checked:false
					})
					this.content_value=''
					this.close()
			},
			//点击列表表示已完成
			finish(id){
				let index=this.list.findIndex((item)=>item.id === id)
				this.list[index].checked=!this.list[index].checked
			},
			tab(index){
				this.activeIndex=index
			}
			
		}
	}
</script>

<style>
	@import '../../common/icon.css';
	.todo-header{
		position: fixed;
	 	top: 35px;
		left: 0;
		width: 100%;
		z-index: 10;
		box-sizing: border-box;
		display: flex;     /* 元素横向排列 */
		height: 45px;
		box-shadow: -1px 1px 5px 0 rgba(0,0,0,0.1);
		background: #FFFFFF;
		align-items: center;    /* 垂直居中 */
		font-size: 12px;
		color: #333333;
		padding: 0 15px;
		
		}
		.todo-header_left{
			width: 100%;
		}
		.todo-header_right{
			flex-shrink: 0; /* 当左边元素宽度占100%时,使用此属性可以防止被挤压 */
			display: flex;    /* 元素横向排列 */
		}
		.todo-header_right_item{
			padding: 0 5px;
		}
		.active-tab{
			color: #279abf;
		}
		.active-text{
			font-size: 14px;
			color: #279abf;
			padding-right: 10px;
		}
		.todo-content{
			position: relative;
			padding-top:70px;
			padding-bottom: 100px;
		}
		.todo-list{
			position: relative;
			padding: 15px;
			box-shadow: -1px 1px 5px 0 rgba(0,0,0,0.1),-1px 2px 1px 0 rgba(255,255,255) inset;
			margin: 15px;
			border-radius: 10px;
			background: #cfebfd;
			color: #0c3854;
			font-size: 14px;
			overflow: hidden; /* 避免圆角被覆盖 */
			display: flex;
			align-items: center;
		}
		.todo-list:after{
			position: absolute;
			content: '';
			top: 0;
			left: 0;
			width: 5px;
			background: #9ed1e8;
			bottom: 0;
		}
		.checkbox{
			width: 20px;
			height: 20px;
			border-radius: 50%;
			background: #FFFFFF;
			box-shadow: 0 0 5px 1px rgba(0,0,0,0.1);
		}
		.todo-list_checkbox{
			padding-right: 15px;
		}
		.todo--finish .checkbox{
			position: relative;
			background:#eee ;
		}
		.todo--finish .checkbox:after {
				content: '';
				position: absolute;
				width: 10px;
				height: 10px;
				top: 0;
				left: 0;
				right: 0;
				bottom: 0;
				margin: auto;
				border-radius: 50%;
				box-shadow: 0 0 2px 0px rgba(0, 0, 0, 0.2) inset;
				background: #CFEBFD;
			}
		.todo--finish .todo-list_content{
			color: #999999;
		}	
		.todo--finish.todo-list:before{ /* 同级关系不需要加空格 */
			content: '';
			position: absolute;
			height: 2px;
			top: 0;
			bottom: 0;
			left: 40px;
			right: 10px;
			margin: auto 0;
			background: #bdcdd8;
		}
		.todo--finish.todo-list:after{ /* 同级关系不需要加空格 */
			background: #cccccc;
		}
		.create-todo{
			display: flex;
			justify-content: center;
			align-items: center;
			position: fixed;
			bottom: 20px;
			left: 0;
			right: 0;
			width: 50px;
			height: 50px;
			border-radius: 50%;
			background: #deeff5;
			box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1),-1px 1px 1px 0 rgba(255,255,255) inset;
			margin: 0 auto;  /* 水平居中 */
		}
		.icon-jia{
			font-size: 30px;
			color: #add8e6;
		}
		.create-content{
			position: fixed;
			bottom: 95px;
			left: 20px;
			right: 20px;
			transition: all 0.3s;
			opacity: 0;
			transform: scale(0) translateY(200%);
			
			
		}
		.create-input{
			width: 100%;
			padding-right: 15px;
			color: #ffaa00;
		}
		.create-button{
			display: flex;
			align-items: center;
			justify-content: center;
			flex-shrink: 0; /* 防止被左边的元素挤压 */
			height: 50px;
			width: 80px;
			border-radius: 50px;
			font-size: 16px;
			color: #88d4ec;
			box-shadow: -2px 0 2px 1px rgba(0,0,0,0.1);
		}
		.create-content:after {
			position: absolute;
			content: '';
			right: 0;
			left: 0;
			width: 20px;
			height: 20px;
			background: #deeff5;
			bottom: -8px;
			transform: rotate(45deg);
			margin: 0 auto;	/* 左右居中 */
			box-shadow: 1px 1px 5px 2px rgba(0,0,0,0.1);
			z-index: -1;
			
		}
		.create-content-box{
			display: flex;
			align-items: center;
			padding: 0 15px;
			padding-right: 0;
			border-radius: 50px;
			background: #DEEFF5;
			box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255) inset;
			z-index: 2;
			
		}
		.create-content-box:after{
			content: '';
			position: absolute;
			right: 0;
			left: 0;
			bottom: -8px;
			margin: 0 auto;
			width: 20px;
			height: 20px;
			background: #DEEFF5;
			transform: rotate(45deg);
		}
		.default{
			padding-top: 100px;
		}
		.image-default{
			display: flex;
			justify-content: center;
		}
		.image-default image{
			width: 100%;
		}
		.default-info{
			text-align: center;
			font-size: 14px;
			color: #cccccc;
		}
		.icon-jia{
			transition: transform 0.3s;
		}
		.create-todo-active{
			transform: rotate(135deg);
		}
		.create-show{
			opacity: 1;
			transform: scale(1) translateY(0);
			
		}
</style>
