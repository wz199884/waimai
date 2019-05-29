<template>
	<div class="rating_select">
		<div class="select_type">
			<span @click="select(2)" class="block positive" :class="{active:selectType==2}">{{desc.all}}<span class="count" :class="{active:selectType==2}">{{ratings.length}}</span></span>
			<span @click="select(0)" class="block positive" :class="{active:selectType==0}">{{desc.positive}}<span class="count" :class="{active:selectType==0}">{{positives.length}}</span></span>
			<span @click="select(1)" class="block negative" :class="{active:selectType==1}">{{desc.negative}}<span class="count" :class="{active:selectType==1}">{{negatives.length}}</span></span>
		</div>
		<div class="toggle" @click="myswitch">
			<span class="icon-check_circle" :class="{active:onlyContent}"></span>
			<span class="text" :class="{active:onlyContent}">只看有内容的评价</span>
		</div>
	</div>
</template>

<script>
	const POSITIVE=0;
	const NEGATIVE=1;
	const ALL=2;
	export default{
		name:'ratingselect',
		props:{
			selectType:{
				type:Number,
				default:0
			},
			onlyContent:{
				type:Boolean,
				default:false
				
			},
			ratings:{
				type:Array,
				default(){
					return []
				}
			},
			desc:{
				type:Object,
				default(){
					return {
						all:'全部',
						positive:'推荐',
						negative:'吐槽'
					}
				}
			}
		},
		methods:{
			select(type){
//				this.selectType=type;
//				子组件和父组件之间如何通信，使用的是emit
				this.$emit('select',type)
			},
			myswitch(){
//				this.onlyContent=!this.onlyContent;
				this.$emit("toggle")
			}
		},
		computed:{
			positives(){
//				数组的filter方法返回值也是一个数组
				return this.ratings.filter((rating)=>{
					return rating.rateType==POSITIVE;
				});
			},
			negatives(){
				return this.ratings.filter((rating)=>{
					return rating.rateType==NEGATIVE;
				});
			}
		}
		
	}
</script>

<style lang="less" scoped="scoped">
@import '../../common/less/mixin.less';
.rating_select{
	.select_type{
		padding: 12px 18px 28px 0px;
		.border-1px(rgba(7,17,27,0.1));
		font-size:0;
		margin:0 18px;
		.block{
			padding: 8px 12px;
			line-height: 16px;
			text-align: center;
			font-size: 12px;
			color: rgb(77,85,93);
			margin-right:8px;
			border-radius:2px;
			&.positive{
				background-color: rgba(0,160,220,0.2);
				.count{
					line-height: 16px;
					font-size: 8px;
					color: rgb(77,85,93);
					padding-left:6px;
					&.active{
						color: white;
					}
				}
				&.active{
					background-color:  rgb(0,160,220);
					color: white;
				}
			}
			&.negative{
				background-color: rgba(77,85,93,0.2);
				.count{
					line-height: 16px;
					font-size: 8px;
					color: rgb(77,85,93);
					padding-left:6px;
					&.active{
						color: white;
					}
				}
				&.active{
					background-color: rgb(77,85,93);
					color: white;
				}
			}
		}
	}
	.toggle{
		padding: 12px 18px;
		.border-1px(rgba(7,17,27,0.1));
		.icon-check_circle{
			vertical-align: top;
			line-height: 24px;
			font-size: 24px;
			color: rgb(147,153,159);
			&.active{
				color: #00c850;
			}
		}
		.text{
			vertical-align: top;
			line-height: 24px;
			font-size: 12px;
			color: rgb(147,153,159);
			&.active{
				color: #93999f;
			}
		}
	}
}
</style>