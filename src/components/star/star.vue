<template>
	<div class="star" :class="starType">
		<span v-for="start_class in starClasses" class="star_item" :class="start_class"></span>
		<!--<span class="star_item"></span>
		<span class="star_item"></span>
		<span class="star_item"></span>
		<span class="star_item"></span>-->
	</div>
</template>


<script>
	const LENGTH=5;
	const START_ON='on';
	const START_HALF='half';
	const START_OFF='off';
	export default{
		name:'star',
		props:{
			size:{
				type:Number
			},
			score:{
				type:Number
			}
		},
		computed:{
			starType(){
				return 'star_'+this.size;
			},
			starClasses(){
				let result=[];
				// 4.2  4.6
				let score=Math.floor(this.score*2)/2;
				let onStart=Math.floor(score);
				let isHalfStart=score%1!=0;
				for(let i=0;i<onStart;i++){
					result.push(START_ON);
				}
				if(isHalfStart){
					result.push(START_HALF)
				}
				if(result.length<LENGTH){
					for(let i=0;i<=LENGTH-result.length;i++){
						result.push(START_OFF)
					}
				}
				return result;
			}
		}
	}
</script>

<style lang="less">
@import '../../common/less/mixin.less';
	.star{
		font-size:0;
		.star_item{
			display:inline-block;
			background-size:100% 100%;
			background-repeat:no-repeat;
		}
		&.star_48{
			.star_item{
				width:20px;
				height:20px;
				margin-right:20px;
				font-size:20px;
				&:last-child{
					margin-right:0;
				}
				&.on{
					.bg-img('star48_on');
				}
				&.half{
					.bg-img('star48_half');
				}
				&.off{
					.bg-img('star48_off');
				}
			}
		}
			&.star_36{
			.star_item{
				width:15px;
				height:15px;
				margin-right:6px;
				font-size: 15px;
				&:last-child{
					margin-right:0;
				}
				&.on{
					.bg-img('star36_on');
				}
				&.half{
					.bg-img('star36_half');
				}
				&.off{
					.bg-img('star36_off');
				}
			}
		}
		&.star_24{
			.star_item{
				width:10px;
				height:10px;
				margin-right:3px;
				font-size:10px;
				&:last-child{
					margin-right:0;
				}
				&.on{
					.bg-img('star24_on');
				}
				&.half{
					.bg-img('star24_half');
				}
				&.off{
					.bg-img('star24_off');
				}
			}
		}
		
	}
</style>