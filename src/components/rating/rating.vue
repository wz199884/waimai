<template>
	<div class="ratings" ref="ratings">
		<div class="ratings_container">
			<!--评价概述部分-->
			<div class="overview">
				<!--概述左边-->
				<div class="overview_left">
					<span class="score">{{seller.score}}</span>
					<span class="text">综合评分</span>
					<span class="rankRate">高于周边商家{{seller.rankRate}}%</span>
				</div>
				<!--概述右边-->
				<div class="overview_right">
					<div class="scores">
						<span class="title">服务态度</span>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="serviceScore">{{seller.serviceScore}}</span>
					</div>
					<div class="scores">
						<span class="title">商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="serviceScore">{{seller.foodScore}}</span>
					</div>
					<div class="deliveryTime">
						<span class="text">送达时间</span>
						<span class="time">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<!--分割线-->
			<split></split>
			<!--评论选择器-->
			<ratingselect 
				:desc="desc"
				:select-type="selectType"
				:only-content="onlyContent"
				:ratings="ratings"
				@select="select"
				@toggle="toggle"
				></ratingselect>
				
			<div class="rating_content">
				<ul>
					<li v-for="rating in ratings" class="rating_item" v-show="needShow(rating.rateType,rating.text)">
						<div class="avatar">
							<img width="28" height="28" :src="rating.avatar"/>
						</div>
						<div class="content">
							<h1 class="username">{{rating.username}}</h1>
							<div class="extra">
								<star :size="24" :score="rating.score"></star>
								<span v-show="rating.deliveryTime" class="deliveryTime">{{rating.deliveryTime}}分钟送达</span>
							</div>
							
							<p class="text">{{rating.text}}</p>
							<div class="recommend">
								<span :class="{'icon-thumb_down':rating.rateType==1,'icon-thumb_up':rating.rateType==0}"></span>
								<span v-for="item in rating.recommend" class="recommend_item">{{item}}</span>
							</div>
							<div class="time">
								{{rating.rateTime | formatTime}}
							</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script>
	import BScroll from 'better-scroll';
	import star from '../star/star';
	import split from '../split/split';
	import ratingselect from '../ratingselect/ratingselect';
	
	const ALL=2;
	const ERRNO=0;
	export default{
		name:'rating',
		data(){
			return {
				desc:{
					all:"全部",
					positive:"满意",
					negative:"不满意"
				},
				selectType:ALL,
				onlyContent:false,
				ratings:[]
			}
		},
		props:{
			seller:{
				type:Object
			}
		},
		components:{
			star,
			split,
			ratingselect
		},
		methods:{
			select(type){
				this.selectType=type;
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
			}
		},
		created(){
			this.$http.get('/api/ratings').then((res)=>{
				if(res.body.errno==ERRNO){
					this.ratings=res.body.data;
					this.$nextTick(()=>{
						if(!this.scroll){
							this.scroll=new BScroll(this.$refs.ratings,{
								click:true
							});
						}else{
							this.scroll.refresh();
						}
					})
				}
			},(error)=>{
				console.log("获取评论数据失败");
			})
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
		
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
.ratings{
	position: absolute;
	top: 174px;
	bottom: 0;
	left: 0;
	width: 100%;
	overflow: hidden;
	background-color: white;
	.overview{
		display: flex;
		padding: 18px 0;
		.overview_left{
			flex: 0 0 137px;
			width: 137px;
			text-align: center;
			border-right: 1px solid rgba(7,17,27,0.1);
			@media only screen and (max-width:320px){
				flex: 0 0 120px;
				width: 120px;
			}
			.score{
				display: block;
				line-height: 28px;
				font-size: 24px;
				color: rgb(255,153,0);
				margin-bottom: 6px;
			}
			.text{
				display: block;
				line-height: 12px;
				font-size: 12px;
				color: rgb(7,17,27);
				margin-bottom: 8px;
			}
			.rankRate{
				display: block;
				line-height: 10px;
				font-size: 10px;
				color: rgb(147,153,159);
				margin-bottom: 6px;
			}
		}
		.overview_right{
			flex: 1;
			padding: 0 24px;
			@media only screen and (max-width:320px){
				padding: 0 4px;
			}
			.scores{
				margin-bottom: 8px;
				font-size:0;
				.title{
					line-height: 18px;
					font-size: 12px;
					color: rgb(7,17,27);
					margin-right: 12px;
				}
				.star{
					display: inline-block;
					margin-right: 12px;
				}
				.serviceScore{
					line-height: 18px;
					font-size: 12px;
					color: rgb(255,153,0);
				}
			}
			.deliveryTime{
				.text{
					line-height: 18px;
					font-size: 12px;
					color: rgb(7,17,27);
					margin-right: 12px;
				}
				.time{
					line-height: 18px;
					font-size: 12px;
					color: rgb(147,153,159);
				}
			}
		}
	}
	.rating_select{
		margin-top: 18px;
	}
	.rating_content{
		padding: 0 18px;
		.rating_item{
			display: flex;
			padding: 18px 0;
			position: relative;
			.border-1px(rgba(7,17,27,0.1));
			.avatar{
				flex: 0 0 28px;
				margin-right: 12px;
				width: 28px;
				height: 28px;
				img{
					border-radius: 50%;
				}
			}
			.content{
				flex: 1;
				.username{
					line-height: 12px;
					font-size: 10;
					color: rgb(7,17,27);
				}
				.extra{
					margin: 4px 0 6px 0;
					font-size: 0;
					.star{
						display: inline-block;
						margin-right: 6px;
					}
					.deliveryTime{
						line-height: 12px;
						font-size: 10px;
						font-weight: 200;
						color: rgb(147,153,159);
					}
				}
				.text{
					line-height: 18px;
					font-size: 12px;
					color: rgb(7,17,27);
				}
				.recommend{
					margin-top: 8px;
					font-size: 0;
					.icon-thumb_down{
						line-height: 16px;
						font-size: 12px;
						color: rgb(183,187,191);
						margin-right: 8px;
					}
					.icon-thumb_up{
						line-height: 16px;
						font-size: 12px;
						color: rgb(0,160,220);
						margin-right: 8px;
					}
					.recommend_item{
						line-height: 16px;
						padding: 0 6px;
						border: 1px solid rgba(7,17,27,0.1);
						border-radius: 2px;
						font-size: 9px;
						color: rgb(147,153,159);
						margin-right: 8px;
						margin-bottom: 8px;
						display: inline-block;
					}

				}
				.time{
					position: absolute;
					top:18px;
					right: 18px;
					line-height: 12px;
					font-size: 10px;
					font-weight: 200;
					color: rgb(147,153,159);
				}
			}
		}
	}
}
</style>