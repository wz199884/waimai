<template>
	<div>
		
	
	<!--有分析可知，购物车分左右两个部分，右边是固定的宽度，左边自适应
	所以使用flex布局-->
		<div class="shopcart"  @click="toggleList">
		<div class="shopcart_left">
			<!--购物车图标分三层-->
			<div class="logo_cantainer">
				<div class="logo" :class="{active:totalNum>0}">
					<span class="icon-shopping_cart" :class="{active:totalNum>0}"></span>
				</div>
				<!--购买商品的数量-->
				<div class="num" v-show="totalNum>0" >
					{{totalNum}}
				</div>
			</div>
			<!--购物总价-->
			<div class="totalPrice" :class="{active:totalPrice>0}">
				￥{{totalPrice}}
			</div>
			<!--配送费-->
			<div class="deliveryPrice">
				另需配送费￥{{deliveryPrice}}
			</div>
		</div>
		<div class="shopcart_right" :class="{active:totalPrice-minPrice>=0}" @click.stop="jiesuan">
			<!--￥{{minPrice}}元起送-->
			{{payText}}
		</div>
	
		<!--购物车列表-->	
		<transition name="fold">
		<div class="shopcart_list" v-show="listShow">
			<!--列表头部-->
			<div class="shopcart_title">
				<span class="title">购物车</span>
				<span class="empty" @click="empty">清空</span>
			</div>
			<!--商品列表-->
			<div class="foods_list" ref="foodList">
				<ul>
					<li v-for="food in selectFoods" class="food_item">
						<!--商品名称-->
						<h1 class="name">{{food.name}}</h1>
						<!--每一个商品的总价-->
						<div class="food_price">
							￥<span class="price">{{food.count*food.price}}</span>
						</div>
						<div class="cartcontrol_container">
							<cartcontrol :food="food"></cartcontrol>
						</div>
						
					</li>
				</ul>
			</div>
		</div>
		</transition>
	</div>
		<!--遮罩层-->
		<transition name="fade" >
			<div class="mask" v-show="listShow"  @click="hideList">
			</div>
		</transition>
	</div>
</template>

<script>
	import BScroll from 'better-scroll';
	import cartcontrol from '../cartcontrol/cartcontrol';
	export default{
		name:'shopcart',
		data(){
			return{
				fold:true
			}
		},
		props:{
			deliveryPrice:{
				type:Number,
				default:0
			},
			minPrice:{
				type:Number,
				default:0
			},
			selectFoods:{
				type:Array,
//				数组的默认值必须是函数,并且有返回值
				default(){
					return [
						{
							price:10,
//							count是该商品的个数,该属性是动态添加的
							count:2
						}
					]
				}
			}
		},
		computed:{
//			计算商品的总个数
			totalNum(){
				let total=0;
				this.selectFoods.forEach((food)=>{
					total+=food.count;
				})
				return total;
			},
//			计算总价格
			totalPrice(){
				let total=0;
				this.selectFoods.forEach((food)=>{
					total+=food.price*food.count;
				})
				return total;
			},
//			购物车右边显示的文本
			payText(){
				if(this.totalPrice==0){
//					￥{{minPrice}}元起送
//					"￥"+this.minPrice+"元起送"; //第一种字符串拼接
					return `￥${this.minPrice}元起送`;
				}else if(this.totalPrice>0 && this.totalPrice<this.minPrice){
					return `还差￥${this.minPrice-this.totalPrice}起送`;
				}else{
					return "去结算";
				}
			},
//			控制购物车列表的显示
			listShow(){
				if(this.totalNum<=0){
					this.fold=true;
					return false;
				}
				let show=!this.fold;
				if(show){
					this.$nextTick(()=>{
						if(!this.scroll){
							this.scroll=new BScroll(this.$refs.foodList,{
							click:true
							});
						}else{
							this.scroll.refresh();
						}
					});
				}
				return show;
			}
		},
		components:{
			cartcontrol
		},
		methods:{
			toggleList(){
				this.fold=!this.fold;
			},
//			清空购物车列表
			empty(){
				this.selectFoods.forEach((food)=>{
					food.count=0;
				})
			},
			jiesuan(){
				if(this.totalPrice-this.minPrice>=0){
					window.alert("需支付"+this.totalPrice);
				}
			},
			hideList(){
				this.fold=true;
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
.shopcart{
	position: fixed;
	left: 0;
	bottom: 0;
	width: 100%;
	height: 48px;
	display: flex;
	/*这个值需要记住,后面的页面会用到*/
	z-index: 50;
	.shopcart_left{
		flex: 1;
		background-color: #141d27;
		.logo_cantainer{
			display: inline-block;
			margin: 0 8px;
			margin-bottom: 2px;
			position: relative;
			top: -10px;
			width: 56px;
			height: 56px;
			border: 6px solid #141d27;
			border-radius: 50%;
			box-sizing: border-box;
			.logo{
				width:100%;
				height:100%;
				border-radius: 50%;
				background-color: #2b343c;
				text-align: center;
				margin-top:-2px;
				line-height: 52px;
				&.active{
					background-color: rgb(0,160,200);
				}
				.icon-shopping_cart{
					line-height: 24px;
					font-size: 24px;
					color: rgba(255,255,255,0.4);
					&.active{
						color: white;
					}
				}
			}
			.num{
				position: absolute;
				top: -6px;
				left: 24px;
				width: 24px;
				height: 15px;
				background-color: rgb(240,20,20);
				color: white;
				text-align: center;
				line-height: 16px;
				border-radius: 6px;
				box-shadow: 0px 4px 8px 0px rgba(0,0,0,0.4);
				font-size: 9px;
				font-weight: 700;
				padding: 2px;
			}
		}
		.totalPrice{
			vertical-align: top;
			display: inline-block;
			margin: 12px 0;
			line-height: 24px;
			font-size: 16px;
			font-weight: 700;
			color: rgba(255,255,255,0.4);
			padding-right: 12px;
			border-right: 1px solid rgba(255,255,255,0.1);
			&.active{
				color: white;
			}
		}
		.deliveryPrice{
			vertical-align: top;
			display: inline-block;
			padding: 0 12px;
			line-height: 24px;
			margin: 12px 0;
			font-size: 12px;
			font-weight: 400;
			color: #72777d;
		}
	}
	.shopcart_right{
		flex: 0 0 105px;
		width: 105px;
		background-color: #2b333b;
		line-height: 48px;
		font-size: 12px;
		font-weight: 700;
		color: rgba(255,255,255,0.4);
		text-align: center;
		padding: 0 12px;
		&.active{
			background-color: #00b43c;
			color: white;
		}
	}
	/*购物车列表样式*/
	.shopcart_list{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		background-color: white;
		z-index: -1;
		transform: translate3d(0,-100%,0);
		&.fold-enter-active,&.fold-leave-active{
			transition: all 0.5s;
		}
		&.fold-enter,&.fold-leave-to{
			transform: translate3d(0,0,0);
		}
		.shopcart_title{
			height:40px;
			padding:0 18px;
			background-color:#F3F5F7;
			.border-1px(rgba(7,17,27,0.1));
			.title{
				line-height: 40px;
				font-size: 14px;
				font-weight: 200;
				color: rgb(0,17,27);
				float: left;
			}
			.empty{
				line-height: 40px;
				font-size: 12px;
				color: rgb(0,160,220);
				float: right;
			}
		}
		.foods_list{
			max-height: 217px;
			overflow: hidden;
			padding: 0 18px;
			.food_item{
				position: relative;
				height:48px;
				.border-1px(rgba(7,17,27,0.1));
				.name{
					line-height: 48px;
					font-size: 14px;
					color: rgb(7,17,27);
				}
				.food_price{
					position: absolute;
					top: 0;
					right: 96px;
					line-height: 48px;
					font-size: 10px;
					color: rgb(240,20,20);
					.price{
						font-size: 14px;
						font-weight: 700;
					}
					
				}
				.cartcontrol_container{
					position: absolute;
					top: 6px;
					right: 0;
				}
			}
		}
	}
}
.mask{
	position: fixed;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	width: 100%;
	height: 100%;
	z-index: 40;
	background-color: rgba(7,17,27,0.6);
	opacity: 1;
	&.fade-enter-active,&.fade-leave-active{
		transition: all 0.5s;
	}
	&.fade-enter,&.fade-leave-to{
		opacity: 0;
	}
	
}
</style>