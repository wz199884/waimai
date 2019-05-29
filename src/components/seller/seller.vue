<template>
	<div class="seller" ref="seller">
		<div class="seler_container">
			<!--头部的商家概述-->
			<div class="overview">
				<div class="overview_top">
					<h1 class="title">{{seller.name}}</h1>
					<div class="extra">
						<star :size="36" :score="seller.score"></star>
						<span class="ratingCount">({{seller.ratingCount}})</span>
						<span class="sellCount">月售{{seller.sellCount}}单</span>
					</div>
					<div class="favorite"  @click="clickFavorite">
						<span class="icon-favorite" :class="{active:favorite}"></span>
						<span class="text_favorite" :class="{active:favorite}">{{favoriteText}}</span>
						
					</div>
				</div>
				<div class="overview_bottom">
					<div class="remark">
						<h1 class="text">起送价</h1>
						<div class="price">
							<span class="money">{{seller.minPrice}}</span>
							<span class="money_text">元</span>
						</div>
					</div>
					<div class="remark">
						<h1 class="text">商家配送</h1>
						<div class="price">
							<span class="money">{{seller.deliveryPrice}}</span>
							<span class="money_text">元</span>
						</div>
					</div>
					<div class="remark">
						<h1 class="text">平均配送时间</h1>
						<div class="price">
							<span class="money">{{seller.deliveryTime}}</span>
							<span class="money_text">分钟</span>
						</div>
					</div>
				</div>
			</div>
			<!--分割线-->
			<split></split>
			<!--公告与活动-->
			<div class="bulletin">
				<h1 class="title">公告与活动</h1>
				<p class="text">{{seller.bulletin}}</p>
				<ul >
					<li v-if="seller.supports" v-for="item in seller.supports" class="supports_list">
						<div class="supports">
							<span class="icon" :class="classMap[item.type]"></span>
							<span class="texts">{{item.description}}</span>
						</div>
					</li>
				</ul>
			</div>
			<!--分割线-->
			<split></split>
			<!--商家实景-->
			<div class="pics">
				<h1 class="title">商家实景</h1>
				<div class="pics_container" ref="picsContainer">
					<ul class="pic_list" ref="ulList">
						<li v-for="pic in seller.pics" class="pic_item">
							<img width="120" height="90" :src="pic"/>
						</li>
					</ul>
				</div>
				
			</div>
			<!--分割线-->
			<split></split>
			<!--商家信息-->
			<div class="info">
				<h1 class="title">商家信息</h1>
				<ul>
					<li v-for="item in seller.infos" class="info_item">
						{{item}}
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
	export default{
		name:'seller',
		data(){
			return {
				favorite:false
			}
		},
		props:{
			seller:{
				type:Object
			}
		},
		components:{
			star,
			split
		},
		created(){
			this.classMap=["decrease","discount","special","invoice","guarantee"];
		},
		methods:{
			initScroll(){
				this.$nextTick(()=>{
					if(!this.scroll){
					this.scroll=new BScroll(this.$refs.seller,{
						click:true
					});
					}else{
						this.scroll.refresh();
					}
				});
			},
			initPics(){
//				需要计算ul宽度
				if(this.seller.pics){
					let width=120;
					let margin=6;
					let ulWidth=(width+margin)*this.seller.pics.length-margin;
					this.$refs.ulList.style.width=ulWidth+"px";
					this.$nextTick(()=>{
						if(!this.scrollPics){
							this.scrollPics=new BScroll(this.$refs.picsContainer,{
								scrollX:true,
								eventPassthrough:'vertical'
							});
						}else{
							this.scrollPics.refresh();
						}
					});
				}
			},
//			点击收藏
			clickFavorite(){
				this.favorite=!this.favorite;
			}
		},
		watch:{
		'seller'(){
				this.$nextTick(()=>{
					this.initScroll();
					this.initPics();
				});
			}
		},
//		如果监听不起作用,就使用钩子函数,mounted
		mounted(){
			this.$nextTick(()=>{
				this.initScroll();
				this.initPics();
			});	
		},
		computed:{
			favoriteText(){
//				if(this.favorite){
//					return '已收藏'
//				}else{
//					return '收藏'
//				}
				return this.favorite?'已收藏':'收藏';
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
.seller{
	position: absolute;
	top: 174px;
	bottom: 0;
	left: 0;
	width: 100%;
	overflow: hidden;
	background-color: white;
	.overview{
		padding: 0 18px;
		.overview_top{
			position:relative;
			padding: 18px 0;
			.border-1px(rgba(7,17,27,0.1));
			.title{
				line-height: 14px;
				margin-bottom: 8px;
				font-size: 14px;
				color: rgb(7,17,27);
			}
			.extra{
				font-size: 0;
				.star{
					vertical-align: top;
					display: inline-block;
					margin-right: 8px;
					margin-top: 1px;
				}
				.ratingCount,.sellCount{
					vertical-align: top;
					line-height: 18px;
					margin-right: 12px;
					font-size: 10px;
					color: rgb(77,85,93);
				}
				.sellCount{
					margin-right: 0;
				}
			}
			.favorite{
				position: absolute;
				top: 18px;
				right: 18px;
				text-align: center;
				width: 50px;
				.icon-favorite{
					display: block;
					line-height: 24px;
					font-size: 24px;
					color: #d4d6d9;
					&.active{
						color: rgb(240,20,20);
					}
				}
				.text_favorite{
					display: block;
					line-height: 10px;
					font-size: 10px;
					color: #a1a7ac;
					&.active{
						color: rgb(77,85,93);
					}
				}
			}
		}
		.overview_bottom{
			display: flex;
			padding: 18px 0;
			text-align: center;
			.remark{
				flex:1;
				border-right:1px solid rgba(7,17,27,0.1);
				&:last-child{
					border-right:none;
				}
				.text{
					line-height: 10px;
					margin-bottom: 4px;
					font-size: 10px;
					color: rgb(147,153,159);
				}
				.price{
					line-height: 24px;
					font-weight: 200;
					color: rgb(7,17,27);
					font-size: 0;
					.money{
						font-size: 24px;
					}
					.money_text{
						font-size: 10px;
					}
				}
			}
		}
	}
	.bulletin{
		padding: 0 18px;
		.title{
			line-height: 14px;
			margin-bottom: 8px;
			margin-top: 18px;
			font-size: 14px;
			color: rgb(7,17,27);
		}
		.text{
			padding: 0 12px 16px 12px;
			line-height: 24px;
			font-size: 12px;
			font-weight: 200;
			color: rgb(240,20,20);
			.border-1px(rgba(7,17,27,0.1));
		}
		.supports_list{
			padding: 16px 12px;
			width: 100%;
			.border-1px(rgba(7,17,27,0.1));
			&:last-child{
				.border-no();
			}
			.supports{
				width: 100%;
				font-weight: 200;
				font-size: 0;
				.icon{
					display: inline-block;
					width: 16px;
					height: 16px;
					background-repeat: no-repeat;
					background-size: 100% 100%;
					vertical-align: top;
					margin-right: 6px;
					&.decrease{
						.bg-img('decrease_4');
					}
					&.discount{
						.bg-img('discount_4');
					}
					&.guarantee{
						.bg-img('guarantee_4');
					}
					&.invoice{
						.bg-img('invoice_4');
					}
					&.special{
						.bg-img('special_4');
					}
				}
				.texts{
					line-height: 16px;
					font-size: 12px;
					font-weight: 200;
					color: rgb(7,17,27);
				}
			}
		}
	}
	.pics{
		padding: 18px 0 18px 18px;
		.title{
			line-height: 14px;
			margin-bottom: 12px;
			font-size: 14px;
			color: rgb(7,17,27);
		}
		.pic_list{
			white-space: nowrap;
			.pic_item{
				display: inline-block;
				width: 120px;
				height: 90px;
				margin-right: 6px;
				&:last-child{
					margin-right: 0;
				}
			}
		}
		
	}
	.info{
		padding: 0 18px;
		.title{
			line-height: 14px;
			padding-bottom: 12px;
			margin-top: 18px;
			font-size: 14px;
			color: rgb(7,17,27);
			.border-1px(rgba(7,17,27,0.1));
		}
		.info_item{
			padding: 16px 12px;
			.border-1px(rgba(7,17,27,0.1));
			line-height: 16px;
			font-size: 12px;
			font-weight: 200;
			color: rgb(7,17,27);
			&:last-child{
				.border-no();
			}
		}
	}
}
</style>