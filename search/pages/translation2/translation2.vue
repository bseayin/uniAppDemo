
<template>
	<view>
		<search></search>
		 
		
	</view>
	
</template>

<script>
	
	import search from "@/components/jm-search/jm-search.vue"


	export default {
		 components: {search},
		data() {
			return {
				 
			}
		},
		methods: {
			
		}
	}
</script>

<style>
/* 三行列表 */
.uni-triplex-row {
	display: flex;
	flex: 1;
	width: 100%;
	box-sizing: border-box;
	flex-direction: row;
	padding: 22upx 30upx;
}
.uni-triplex-right,
.uni-triplex-left {
	display: flex;
	flex-direction: column;
}
.uni-triplex-left {
	width: 84%;
}
.uni-triplex-left .uni-title{
	padding:8upx 0;
}
.uni-triplex-left .uni-text, .uni-triplex-left .uni-text-small{color:#999999;}
.uni-triplex-right {
	width: 16%;
	text-align: right;
}
</style>
