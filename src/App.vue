<template>
  <div id="app">
   
   <myheader :seller='seller'></myheader>
   
  	<div class="nav">
  		<div class="goods">
  			<router-link to='/goods'>商品</router-link>
  		</div>
  		<div class="ratings">
  			<router-link to='/rating'>评价</router-link>
  		</div>
  		<div class="seller">
  			<router-link to='/seller'>商家</router-link>
  		</div>
  	</div>
   	<!--通过路由来传递seller对象，因为后面的商家组件，和评价组件，都使用到了seller对象的信息-->
   	<keep-alive>
  		<router-view :seller="seller"></router-view>
  	</keep-alive>
  </div>
</template>

<script>
	import header from './components/header/header';
	const ERRNO=0;
export default {
  name: 'App',
  components:{
  	'myheader':header
  },
  data(){
  	return{
  		seller:{}
  	}
  },
  created(){
  	this.$http.get('/api/seller').then((res)=>{
//		console.log(res);
//		console.log(res.body);
  		res=res.body;
  		if(res.errno==ERRNO){
  			this.seller=res.data;
  		}
//		console.log(res.body.data);
  	},(err)=>{
  		console.log("获取selles数据的时候异常");
  	})
  }
}
</script>

<style lang="less">
@import 'common/less/mixin.less';
@import 'common/less/base.less';
#app {
	.nav{
		height: 40px;
		display: flex;
		/*border-bottom:1px solid rgba(7,17,27,0.1);*/
		.border-1px(rgba(7,17,27,0.1));
		.goods,.ratings,.seller{
			flex: 1;
			text-align: center;
			line-height: 40px;
			font-size: 14px;
			a{
				width: 100%;
				height: 100%;
				display: inline-block;
				line-height: 14px;
				color: rgb(77,85,93);
			}
			a.router-link-active{
				color: rgb(240,20,20);
			}
		}
	}
}
</style>
