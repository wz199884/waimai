<template>
	<div class="cartcontrol">
		<!--减号-->
		<transition name='move'>
			<div class="decrease" @click.stop="decrease" v-show="food.count>0">
				<span class="icon-remove_circle_outline"></span>
			</div>
		</transition>
		<!--该商品的数量-->
		<div class="num" v-show="food.count>0">
			{{getNum}}
		</div>
		<!--加号-->
		<div class="add" @click.stop="add">
			<span class="icon-add_circle"></span>
		</div>
		
	</div>
</template>

<script>
//	导入vue的作用是给food对象,动态添加属性count
	import Vue from 'vue';
	export default {
		name:'cartcontrol',
		props:{
			food:{
				type:Object
			}
		},
		methods:{
			add(){
//				判断当food对象没有count属性的时候添加该该属性,
//				如果有,就直接+
				if(!this.food.count){
					Vue.set(this.food,'count',1);
				}else{
					this.food.count++;
				}
				
			},
			decrease(){
				this.food.count--;
			},
			
		},
		computed:{
			getNum(){
				return this.food.count;
			}
		}
	}
</script>

<style lang="less" scoped="scoped">
.cartcontrol{
	font-size: 0;
	.decrease{
		display: inline-block;
		line-height: 24px;
		font-size: 24px;
		color: rgb(0,160,220);
		vertical-align: top;
		padding: 6px;
		opacity: 1;
		transform: translate3d(0,0,0);
		&.move-enter-active,&.move-leave-active{
			transition: all 0.2s linear;
		}
		&.move-enter,&.move-leave-to{
			opacity: 0;
			transform: translate3d(24px,0,0);
		}
		
	}
	.num{
		display: inline-block;
		line-height: 24px;
		font-size: 10px;
		color: rgb(147,153,159);
		width: 24px;
		text-align: center;
		margin-top: 6px;
	}
	.add{
		display: inline-block;
		line-height: 24px;
		font-size: 24px;
		color: rgb(0,160,220);
		vertical-align: top;
		padding: 6px;
	}
}
</style>