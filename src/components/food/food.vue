<template>
	<transition name="move">
	<div class="food" v-show="showFlag"  ref="food">
		<div class="foods_container">
			<!--头部图片-->
			<div class="header_img">
				<img :src="food.image"  />
				<div class="back" @click="back">
					<span class="icon-arrow_lift"></span>
				</div>
			</div>
			<!--内容部分-->
			<div class="content_container">
				<h1 class="title">{{food.name}}</h1>
				<div class="extra">
					<span class="sellCount">月售{{food.sellCount}}份</span>
					<span class="rating">好评率{{food.rating}}%</span>
				</div>
				<div class="price">
					<span class="now">￥{{food.price}}</span>
					<span class="old" v-show="food.oldPrice>0">￥{{food.oldPrice}}</span>
				</div>
				<transition name="fade">
				<div class="add_first_cart" v-show="food.count<=0 || !food.count" @click="buy">
					加入购物车
				</div>
				</transition>
				<div class="cartcontrol_container" v-show="food.count>0">
					<cartcontrol :food="food"></cartcontrol>
				</div>
			</div>
			<!--分割线-->
			<split></split>
			<!--商品介绍-->
			<div class="info" v-show="food.info">
				<h1 class="title">商品介绍</h1>
				<p class="text">{{food.info}}</p>
			</div>
			<!--分割线-->
			<split v-show="food.info"></split>
			<div class="ratings">
				<h1 class="title">商品评价</h1>
				<!--选择评论类型-->
				<ratingselect 
					@select="select"  
					@toggle="toggle"
					:select-type="selectType"
					:only-content="onlyContent"
					:ratings="food.ratings"
					></ratingselect>
				<!--评论内容-->
				<div class="rating_content">
					<ul>
						<li v-for="rating in food.ratings" class="rating_item" v-show="needShow(rating.rateType,rating.text)">
							<!--用户信息-->
							<div class="user">
								<span class="username">{{rating.username}}</span>
								<img class="avatar" width="12" height="12" :src="rating.avatar"/>
							</div>
							<!--评论时间-->
							<div class="time">
								{{rating.rateTime | formatTime}}
							</div>
							<!--评论内容-->
							<div class="content">
								<span :class="{'icon-thumb_down':rating.rateType==1,'icon-thumb_up':rating.rateType==0}"></span>
								<span class="text">{{rating.text}}</span>
							</div>
						</li>
					</ul>
				</div>
			</div>
		</div>
	</div>
	</transition>
</template>

<script>
	import Vue from 'vue';
	import BScroll from 'better-scroll';
	import cartcontrol from '../cartcontrol/cartcontrol';
	import split from '../split/split';
	import ratingselect from '../ratingselect/ratingselect';
	const ALL=2;
	export default{
		name:'food',
		data(){
			return {
				showFlag:false,
				selectType:ALL,
				onlyContent:false
			}
		},
		props:{
			food:{
				type:Object
			}
		},
		methods:{
//			显示商品详情
			show(){
				this.showFlag=true;
//				在商品详情页显示的时候进行初始化滚动，不要忘了使用异步的nextTick方式
				this.$nextTick(()=>{
					
					if(!this.scroll){
						
						this.scroll=new BScroll(this.$refs.food,{
							click:true
						});
					}else{
						this.scroll.refresh();
					}
				});
			},
			back(){
				this.showFlag=false;
			},
			select(type){
				this.selectType=type;
				//一但我们的数据改变，dom元素的高度也变化了，better-scroll需要重新刷新
				this.$nextTick(()=>{
					this.scroll.refresh();
				});
			},
			toggle(){
				this.onlyContent=!this.onlyContent;
				this.$nextTick(()=>{
					this.scroll.refresh();
				});
			},
			needShow(type,text){
				if(this.onlyContent && !text){
					return false;
				}
				if(this.selectType==ALL){
					return true;
				}else{
					return this.selectType==type;
				}
				return ALL;
			},
			buy(){
				if(this.food.count<=0 || !this.food.count){
					Vue.set(this.food,'count',1);
				}
			}
			
			
		},
		components:{
			cartcontrol,
			split,
			ratingselect
		},
		filters:{
			formatTime(time){
				var date=new Date(time);
				var year=date.getFullYear();
				var month=date.getMonth()+1;
				var day=date.getDate();
				var hour=date.getHours();
				var minute=date.getMinutes();
				
				month<10?month="0"+month:month;
				day<10?day="0"+day:day;
				hour<10?hour="0"+hour:hour;
				minute<10?minute="0"+minute:minute;
				
				return year+"-"+month+"-"+day+" "+hour+":"+minute;
				
			}
		},
		computed:{
//			是否显示评论的那个li
//			needShow(type,text){
//				if(this.onlyContent && !text){
//					return false;
//				}
//				if(this.selectType==ALL){
//					return true;
//				}else{
//					return this.selectType==type;
//				}
//				return ALL;
//			}
		}
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
.food{
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	width: 100%;
	height: 100%;
	overflow: hidden;
	background-color: white;
	transform:translate3d(0,0,0);
	&.move-enter-active,&.move-leave-active{
		transition: all 0.5s;
	}
	&.move-enter,&.move-leave-to{
		transform:translate3d(100%,0,0);
	}
	/*宽度为100%,高度为0,padding-top为100%,就可以让盒子的高度和宽度一样*/
	.header_img{
		width: 100%;
		height: 0;
		padding-top: 100%;
		position:relative;
		img{
			position:absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
		}
		.back{
			position: absolute;
			top: 0;
			left: 0;
			padding: 20px;
			color: white;
		}
	}
	.content_container{
		padding: 18px;
		position: relative;
		.title{
			line-height: 14px;
			font-size: 14px;
			font-weight: 700;
			color: rgb(7,17,27);
		}
		.extra{
			margin: 8px 0 18px 0;
			font-size: 0;
			.sellCount{
				line-height: 10px;
				font-size: 10px;
				color: rgb(147,153,159);
				margin-right: 12px;
			}
			.rating{
				line-height: 10px;
				font-size: 10px;
				color: rgb(147,153,159);
			}
		}
		.price{
			font-size: 0;
			.now{
				line-height: 24px;
				font-size: 14px;
				font-weight: 700;
				color: rgb(240,20,20);
				margin-right: 12px;
			}
			.old{
				line-height: 24px;
				font-size: 10px;
				font-weight: 700;
				color: rgb(147,153,159);
				text-decoration: line-through;
			}
		}
		.add_first_cart{
			position: absolute;
			right: 18px;
			bottom: 18px;
			width: 74px;
			height: 24px;
			text-align: center;
			line-height: 24px;
			background-color: rgb(0,160,220);
			color: white;
			border-radius: 12px;
			font-size: 10px;
			opacity: 1;
			&.fade-enter-active,&.fade-leave-active{
				transition: all 0.5s linear;
			}
			&.fade-enter,&.fade-leave-to{
				opacity: 0;
			}
			
		}
		.cartcontrol_container{
			position: absolute;
			right: 18px;
			bottom: 12px;
		}
	}
	.info{
		padding: 18px;
		.title{
			line-height: 14px;
			font-size: 14px;
			font-weight: 700;
			color: rgb(7,17,27);
		}
		.text{
			margin-top: 6px;
			padding: 0 8px;
			line-height: 24px;
			font-size: 12px;
			font-weight: 200;
			color: rgb(77,85,93);
		}
	}
	.ratings{
		.title{
			margin: 18px 18px 6px 18px;
			line-height: 14px;
			font-size: 14px;
			font-weight: 700;
			color: rgb(7,17,27);
		}
		.rating_content{
			padding: 0 18px;
			.rating_item{
				padding: 16px 0;
				.border-1px(rgba(7,17,27,0.1));
				.user{
					position: absolute;
					right: 0;
					top: 16px;
					font-size: 0;
					.username{
						line-height: 12px;
						font-size: 10px;
						color: rgb(147,153,159);
						margin-right: 6px;
					}
					.avatar{
						border-radius: 50%;
					}
				}
				.time{
					line-height: 12px;
					font-size: 10px;
					color: rgb(147,153,159);
				}
				.content{
					margin-top: 6px;
					font-size: 0;
					.icon-thumb_down{
						line-height: 24px;
						font-size: 12px;
						color: rgb(147,153,159);
						margin-right: 4px;
					}
					.icon-thumb_up{
						line-height: 24px;
						font-size: 12px;
						color: rgb(0,160,220);
						margin-right: 4px;
					}
					.text{
						line-height: 16px;
						font-size: 12px;
						color: rgb(7,17,27);
					}
				}
			}
		}
	}
}
</style>