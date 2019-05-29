<template>
	<div class="good">
		<!--左边的菜单栏     ref属性是vue获取原生dom 元素的-->
		<div class="menu_container" ref="menuContainer">
			<ul>
				<li v-for="(good,index) in goods" class="muen_list" :class="{current:index==currentIndex}" @click="clickMenu(index)">
					<span class="text">
						<span class="icon" :class="classMap[good.type]" v-show="good.type>0"></span>
						{{good.name}}
					</span>
				</li>
			</ul>
		</div>
		<!--右边的具体商品-->
		<div class="foods_container"  ref="foodsContainer">
			<ul>
				<li v-for="good in goods" class="foods_list"  ref="foodList">
					<h1 class="name">{{good.name}}</h1>
					<ul>
						<li v-for="item in good.foods"  class="content_container" @click="clickFood(item)">
							<!--<div >-->
								<div class="avatar">
									<img width="57" height="57" :src="item.icon"  />
								</div>
								<div class="content">
									<div class="title">
										{{item.name}}
									</div>
									<div class="description">
										{{item.description}}
									</div>
									<div class="extra">
										<span class="sellCount">月售{{item.sellCount}}份</span>
										<span class="rating">好评率{{item.rating}}%</span>
									</div>
									<div class="price">
										<span class="now">￥{{item.price}}</span>
										<span class="old" v-show="item.oldPrice>0">￥{{item.oldPrice}}</span>
									</div>
								</div>
								<!--这里是我们的购物车控制器所在的位置-->
								<div class="cartcontrol_container">
									<cartcontrol :food="item"></cartcontrol>
								</div>
							<!--</div>-->
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<!--购物车组件-->
		<shopcart :deliveryPrice="seller.deliveryPrice" 
			:minPrice="seller.minPrice" 
			:selectFoods="selectFoods"></shopcart>
		<!--商品详情组件-->
		<food :food="selectFood" ref="mFood"></food>
	</div>
</template>

<script>
import BScrool from 'better-scroll';
import shopcart from '../shopcart/shopcart';
import cartcontrol from '../cartcontrol/cartcontrol';
import food from '../food/food';
	const ERRORNO=0;
	export default{
		name:'goods',
		props:{
//			这里的seller是在App.vue里面的路由传递过来的
			seller:{
				type:Object
			}
		},
		data(){
			return {
				goods:[],
				heightList:[],
				scrollY:0,
				selectFood:{}
			}
		},
		created(){
			this.classMap=["decrease","discount","special","invoice","guarantee"];
			this.$http.get('/api/goods').then((res)=>{
				if(res.body.errno==ERRORNO){
					this.goods=res.body.data;
					//渲染页面上的元素的
					this.$nextTick(()=>{
						this.initScroll();
						this.calculateHeight();
					})
					
				}
			},(error)=>{
				console.log("获取商品列表失败");
			})
		},
		methods:{
//			初始化滚动插件
			initScroll(){
				//需要new出来插件对象，插件对象的构造方法的第一参数是我们的dom元素
				//dom元素的获取是使用的ref属性
				this.menuContainer=new BScrool(this.$refs.menuContainer,{
					click:true
				});
				//第二个参数是我们的滚动选项属性,
				//这个属性必须添加，才能监听滚动事件 值必须是3 probeType:3
				this.foodsContainer=new BScrool(this.$refs.foodsContainer,{
					click:true,
					probeType:3
				});
				//监听滚动事件,第一个参数是监听的事件名
				this.foodsContainer.on('scroll',(position)=>{
//					console.log(position);
					//先四舍五入再去绝对值
					this.scrollY=Math.abs(Math.round(position.y));
//					console.log(this.scrollY);
				})
			},
			//计算每一个商品的高度，即li的高度
			calculateHeight(){
				let foodList=this.$refs.foodList;
				let height=0;
				this.heightList.push(height);
				for(var i=0;i<foodList.length;i++){
					let item=foodList[i];
					height+=item.clientHeight;
//					console.log(height);
					this.heightList.push(height);
					
				}
			},
			//点击菜单事件
			clickMenu(index){
//				console.log(index);
				let foodList=this.$refs.foodList;
				let itemLi=foodList[index];
				//第一个参数就是要滚动的元素，第二个参数就是就是多长事件滚动完成，单位是毫秒
				this.foodsContainer.scrollToElement(itemLi,300);
				
			},
			
			//点击查看当前商品的详情
			clickFood(food){
				this.selectFood=food;
				//拿到food组件，并调用它里面的show方法
				this.$refs.mFood.show();
			}
			
		},
		computed:{
			currentIndex(){
				for(var i=0;i<this.heightList.length;i++){
					let height1=this.heightList[i];
					let height2=this.heightList[i+1];
					if(!height2 || (this.scrollY>=height1 && this.scrollY<height2)){
						return i;
					}
				}
				return 0;
			},
			
//			计算选中的商品选中的商品
			selectFoods(){
				let myFoods=[];
				this.goods.forEach((good)=>{
					good.foods.forEach((food)=>{
						if(food.count){
							myFoods.push(food);
						}
						
					})
				});
				return myFoods;
			}
		},
		components:{
			shopcart,
			cartcontrol,
			food
		}
	}
</script>

<style lang="less">
@import '../../common/less/mixin.less';
	.good{
		display: flex;
		position: absolute;
		top: 174px;
		bottom: 48px;
		width: 100%;
		overflow: hidden;
		.menu_container{
			flex: 0 0 80px;
			width: 80px;
			background-color: #f3f5f7;
			.muen_list{
				height: 54px;
				width: 56px;
				padding: 0 12px;
				display: table;
				font-size:0;
				&.current{
					background-color: white;
					font-weight: 700;
					color: #07111b;
					position:relative;
					top:-1px;
					.text{
						.border-no();
					}
				}
				.text{
					display: table-cell;
					vertical-align: middle;
					color: #07111b;
					font-size: 12px;
					font-weight: 200;
					line-height: 14px;
					.border-1px(rgba(7,17,27,0.1));
					.icon{
						display: inline-block;
						width: 12px;
						height: 12px;
						background-repeat: no-repeat;
						background-size: 100% 100%;
						vertical-align: top;
						&.decrease{
							.bg-img('decrease_3');
						}
						&.discount{
							.bg-img('discount_3');
						}
						&.guarantee{
							.bg-img('guarantee_3');
						}
						&.invoice{
							.bg-img('invoice_3');
						}
						&.special{
							.bg-img('special_3');
						}
					}
				}
			}
			
		}
		.foods_container{
			flex: 1;
			.foods_list{
				.name{
					height: 26px;
					background-color: #f3f5f7;
					padding-left: 14px;
					border-left: 2px solid #d9dde1;
					font-size: 12px;
					line-height: 26px;
					color: rgb(147,153,159);
				}
				.content_container{
					position: relative;
					display: flex;
					margin: 18px 18px;
					padding-bottom: 18px;
					.border-1px(rgba(7,17,27,0.1));
					&:last-child{
						.border-no();
					}
					.avatar{
						flex: 0 0 57px;
						height: 57px;
						height: 57px;
						margin-right: 10px;
					}
					.content{
						flex: 1;
						.title{
							margin-top: 2px;
							font-size: 14px;
							line-height: 14px;
							color: rgb(7,17,27);
							font-weight: 700;
						}
						.description{
							margin: 8px 0;
							font-size: 10px;
							line-height: 18px;
							color: rgb(147,153,159);
						}
						.extra{
							
							line-height: 10px;
							color: rgb(147,153,159);
							font-size: 0;
							.sellCount{
								font-size: 10px;
								margin-right: 12px;
							}
							.rating{
								font-size: 10px;
							}
						}
						.price{
							.now{
								font-size: 14px;
								line-height: 24px;
								color: rgb(240,20,20);
								font-weight: 700;
							}
							.old{
								font-size: 10px;
								line-height: 24px;
								color: rgb(147,153,159);
								font-weight: 700;
								text-decoration: line-through;
							}
						}
					}
					.cartcontrol_container{
						position: absolute;
						bottom: 12px;
						right: 12px;
					}
				}
			}
		}
	}

</style>