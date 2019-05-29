<template>
	<div class="header">
		<!--内容容器-->
		<div class="content_container">
			<div class="avatar">
				<img width="64"  height="64" :src="seller.avatar" />
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">
					{{seller.description}}/{{seller.deliveryTime}}分钟到达
				</div>
				<div class="supports" v-if="seller.supports">
					<span class="icon" :class="classMap[seller.supports[0].type]"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div v-if="seller.supports" class="supports_count" @click="showDetail">
				<span class="count">{{seller.supports.length}}个</span><span class="icon-keyboard_arrow_right"></span>
				
			</div>
		</div>
		<!--公告-->
		<div  class="bulletin_container" @click="showDetail">
			<span class="icon"></span>
			<span class="bulletin">{{seller.bulletin}}</span>
			<i class="icon-keyboard_arrow_right  myarrow"></i>
			<!--<span class="icon-keyboard_arrow_right myarrow"></span>-->
		</div>
		<!--背景-->
		<div class="heaer_background">
			<img width="100%" height="100%" :src="seller.avatar"/>
		</div>
		<!--遮罩层-->
		<transition name="fade">
			<div class="mask" v-show="isSHow">
			<div class="detail_container clearfix">
				<div class="detail_main">
					<h1 class="name">{{seller.name}}</h1>
					<div class="star">
						<star :size='48' :score="seller.score"></star>
					</div>
					<div class="title">
						<div class="line"></div>
						<div class="text">优惠信息</div>
						<div class="line"></div>
					</div>
					
					<ul>
						<li v-if="seller.supports" v-for="item in seller.supports" class="supports_list">
							<div class="supports">
								<span class="icon" :class="classMap[item.type]"></span>
								<span class="text">{{item.description}}</span>
							</div>
						</li>
					</ul>
					<div class="title">
						<div class="line"></div>
						<div class="text">商家公告</div>
						<div class="line"></div>
					</div>
					<p class="bulletin">{{seller.bulletin}}</p>
				</div>
			</div>
			<div class="detail_close" @click="hideDetail">
				<span class="icon-close"></span>
			</div>
		</div>
		</transition>
	</div>
</template>

<script>
	import star from '../star/star';
	export default{
		name:'top',
		props:{
			seller:{
				type:Object
			}
		},
		data(){
			return {
				isSHow:false
			}
		},
		created(){
			this.classMap=["decrease","discount","special","invoice","guarantee"];
		},
		components:{
			star
		},
		methods:{
			showDetail(){
				this.isSHow=true;
			},
			hideDetail(){
				this.isSHow=false;
			}
		}
	}
</script>

<style lang="less">
@import '../../common/less/mixin.less';
	.header{
		position:relative;
		color: white;
		background-color: rgba(7,17,27,0.5);
		.content_container{
			position:relative;
			font-size:0;
			padding: 24px 12px 18px 24px;
			.avatar{
				display: inline-block;
				vertical-align: top;
				margin-right: 16px;
			}
			.content{
				display: inline-block;
				.title{
					font-size: 0;
					padding: 2px 0 8px 0;
					font-weight: bold;
					.brand{
						display: inline-block;
						width: 30px;
						height: 18px;
						background-repeat: no-repeat;
						background-size: 100% 100%;
						.bg-img('brand');
						vertical-align: top;
						margin-right: 6px;
					}
					.name{
						line-height: 18px;
						font-size: 16px;
					}
				}
				.description{
					font-size: 12px;
					line-height: 12px;
					font-weight: 200;
					margin-bottom: 10px;
				}
				.supports{
					margin-bottom: 2px;
					font-weight: 200;
					font-size: 0;
					.icon{
						display: inline-block;
						width: 12px;
						height: 12px;
						background-repeat: no-repeat;
						background-size: 100% 100%;
						vertical-align: top;
						margin-right: 4px;
						&.decrease{
							.bg-img('decrease_1');
						}
						&.discount{
							.bg-img('discount_1');
						}
						&.guarantee{
							.bg-img('guarantee_1');
						}
						&.invoice{
							.bg-img('invoice_1');
						}
						&.special{
							.bg-img('special_1');
						}
					}
					.text{
						line-height: 12px;
						font-size: 10px;
					}
				}
				
			}
			.supports_count{
				height: 10px;
				width: 42px;
				border-radius: 8px;
				background-color: rgba(0,0,0,0.2);
				position: absolute;
				right: 12px;
				bottom: 18px;
				padding: 7px 0 7px 8px;
				.count,.icon-keyboard_arrow_right{
					display: inline-block;
					width: 20px;
					height: 10px;
					font-size: 10px;
					line-height: 12px;
					font-weight: 200;
				}
				.icon-keyboard_arrow_right{
					
				}
			}
		}
		.bulletin_container{
			position: relative;
			height: 28px;
			padding: 0 12px;
			background-color: rgba(7,17,27,0.2);
			font-size: 10px;
			line-height: 28px;
			font-weight: 200;
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
			.icon{
				display: inline-block;
				width: 22px;
				height: 12px;
				background-repeat: no-repeat;
				background-size: 100% 100%;
				.bg-img('bulletin');
				vertical-align: top;
				margin-top: 9px;
			}
			.myarrow{
				display: inline-block;
				width: 10px;
				height: 10px;
				position: absolute;
				top: 9px;
				right: 5px;
			}
		}
		.heaer_background{
			width: 100%;
			height: 100%;
			position: absolute;
			top: 0;
			left: 0;
			z-index: -1;
			filter: blur(10px);
		}
		.fade-enter,.fade-leave-to{
			opacity: 0;
		}
		.fade-enter-active,.fade-leave-active{
			transition: opacity 1s; 
		}
		.fade-enter-to,.fade-leave{
			opacity: 1;
		}
		.mask{
			position: fixed;
			top: 0;
			bottom: 0;
			left: 0;
			right: 0;
			width: 100%;
			height: 100%;
			background-color: rgba(7,17,27,0.8);
			z-index: 200;
			overflow: auto;
			.detail_container{
				min-height: 100%;
				.detail_main{
					padding-bottom: 32px;
					margin-top: 64px;
					.name{
						font-size: 16px;
						line-height: 16px;
						font-weight: 700;
						text-align: center;
					}
					.star{
						text-align: center;
						margin-top: 16px;
					}
					.title{
						display: flex;
						width: 80%;
						margin: 28px auto 24px auto;
						.line{
							flex: 1;
							border-bottom: 1px solid rgba(255,255,255,0.2);
							height: 1px;
							margin-top: 6px;
						}
						.text{
							flex: 1;
							font-size: 14px;
							line-height: 14px;
							font-weight: 700;
							text-align: center;
						}
					}
					.supports_list{
						width: 80%;
						padding: 0 12px;
						margin: 0 auto;
						.supports{
							margin-bottom: 12px;
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
									.bg-img('decrease_2');
								}
								&.discount{
									.bg-img('discount_2');
								}
								&.guarantee{
									.bg-img('guarantee_2');
								}
								&.invoice{
									.bg-img('invoice_2');
								}
								&.special{
									.bg-img('special_2');
								}
							}
							.text{
								line-height: 12px;
								font-size: 12px;
								font-weight: 200;
							}
						}
					}
					.bulletin{
						width: 80%;
						margin: 0 auto;
						padding: 0 12px;
						font-size: 12px;
						line-height: 24px;
						font-weight: 200;
					}
				}
			}
			.detail_close{
				position: relative;
				clear: both;
				margin: -32px auto 0 auto;
				font-size: 32px;
				color: rgba(255,255,255,0.5);
				width: 32px;
				height: 32px;
			}
		}
	}
</style>