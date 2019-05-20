<template>
	<view>
		<!-- 搜索条 -->
		<view class="searchTopBox">
			<view class="searchBoxRadius">
				<view class="grace-search-icon searchBoxIcon"></view>
				<input class="searchBoxIpt" type="search" v-model="ipt" @confirm="searchNow($event)" placeholder="输入单词"></input>
			</view>
		</view>
		<!-- 搜索历史 -->
		<view class="searchBotBox">
			<view class="ov">
				<view class="fl">查询历史</view>
				<view @tap="clearKey" class="fr grace-more-r grace-search-remove"></view>
			</view>
			<view class="searchHistoryBox">
				<view class="searchHistoryBoxItem" v-for="(item,index) in searchKey" :key='index'>
					{{item}}
				</view>
			</view>
		</view>
		
		
		<view class="uni-list">
		    <block >
		        <view class="uni-list-cell" hover-class="uni-list-cell-hover">
		            <view class="uni-triplex-row">
		                <view class="uni-triplex-left">
							 <text class="uni-title uni-ellipsis">{{searchrs.content}}</text>
							  <text class="uni-title uni-ellipsis" v-if="searchrs.pron">音标:{{searchrs.pron}}</text>
		                    <text class="uni-title uni-ellipsis">{{searchrs.definition}}</text>
		                    
							<audio v-if="searchrs.pron"  style="text-align: left" :src="searchrs.audio_addresses.us[0]"  author="美式发音"   :name="searchrs.content"
							    controls></audio>
		                   
		                </view>
						 <view class="uni-triplex-right">
						    <view class="uni-common-mt" style="text-align: center;">
						    		<button v-if="searchrs.pron" @click="addtobook()" class="mini-btn" type="primary" size="mini"><uni-icon type="plus" size="20"></uni-icon></button>
						    </view>
						</view>
		               <view height='20upx'>
						    <!-- <video  v-bind:src="searchrs.audio_addresses.us[0]"></video> -->
					   </view>
		            </view>
		        </view>
		    </block>
		</view>
	</view>
</template>
<script>
	import uniIcon from "@/components/uni-icon/uni-icon.vue"

	export default {
		 components: {uniIcon},
		data() {
			return {
				searchKey: [],
				ipt: '',
				searchrs:{},
				searchClose: true,

			}
		},
		onShow() {
			var vv = uni.getStorageSync('searchLocal');
			var arr = vv.split("-");
			this.searchKey = arr;
			
		},
		methods: {

			clearKey: function() {
				var that = this;
				uni.showModal({
					title: '提示',
					content: '点击确定将删除所有历史信息，确定删除吗？',
					success: function(res) {
						if (res.confirm) {
							that.searchKey = [];
							uni.setStorage({
								key: 'searchLocal',
								data: that.searchKey
							});
							
						} else if (res.cancel) {

						}å
					}
				});

			},
			searchNow: function(e) {
				if (this.ipt == '') {
					uni.showToast({
						title: '未输入搜索关键字',
						icon: 'none',
						duration: 1000
					});
					return false;
				}
				var that = this;
				that.searchrs={};
				var newArr = that.searchKey;
				newArr.push(this.ipt);
				this.searchKey = newArr;
				this.callsb(this.ipt);
				var newStr = newArr.join('-');
				uni.setStorage({
					key: 'searchLocal',
					data: newStr
				});
			},
			callsb:function(val){
				console.log("**********call扇贝*******"+val);
				
				const requestUrl = 'https://api.shanbay.com/bdc/search/?word='+val
				var that = this;
				uni.request({
					url: requestUrl,
					dataType: 'text',
					data: {
						noncestr: Date.now()
					},
					success: (res) => {
					console.log(res);
					let datajson=JSON.parse(res.data);
					if("SUCCESS"==datajson.msg){
						// string 转 json
						that.searchrs=datajson.data;
						console.log(that.searchrs);
						uni.setStorage({
							key: 'searchrsLocal',
							data: that.searchrs
						});
					}
						
						
					},
					fail: (err) => {
						console.log('request fail', err);
						uni.showModal({
							content: err.errMsg,
							showCancel: false
						});
					},
					complete: () => {
						this.loading = false;
					}
				});
			},
			callyoudao:function(val){
				console.log("**********callyoudao*******"+val);
				const duration = 2000;
				const requestUrl = 'http://fanyi.youdao.com/translate?&doctype=json&type=AUTO&i='+val
				
				uni.request({
					url: requestUrl,
					dataType: 'text',
					data: {
						noncestr: Date.now()
					},
					success: (res) => {
						console.log('request success', res)
						console.log("***************1*******")
						console.log(res)
						console.log("***************2*******")
						console.log(res.data)
							console.log("***************3*******")
						let translateResult=JSON.parse(res.data);
						console.log(translateResult)
						console.log("***************4*******")
						
						console.log(translateResult.translateResult[0][0].tgt)
						this.res = '请求结果 : ' + JSON.stringify(res);
						// let  srcval=this.res.data.translateResult[0].src;
						// let   tgtval=this.res.data.translateResult[0].tgt;
						console.log(this.res);
						console.log(this.res.data);
					},
					fail: (err) => {
						console.log('request fail', err);
						uni.showModal({
							content: err.errMsg,
							showCancel: false
						});
					},
					complete: () => {
						this.loading = false;
					}
				});
			},
			addtobook:function(){
				console.log("***********addtobook *******");
				var userinfo = uni.getStorageSync('user');
				let rsdata= uni.getStorageSync('searchrsLocal');
				 // let username=userinfo.nickName;
				 
				 console.log("***********addtobook ***userinfo****",userinfo);
				 console.log("***********addtobook ***rsdata****",rsdata);
				 
				 const requestUrl = 'http://localhost:9001/core/book/add'
				 var that = this;
				 let requestdata={};
				 requestdata.openid=userinfo.openid;
				 requestdata.content=rsdata.content;
				 requestdata.pron=rsdata.pron;
				 requestdata.definition=rsdata.definition;
				 requestdata.audio_addresses=rsdata.audio_addresses.us[0];
				
				 var datarequest = JSON.stringify(requestdata);
				  console.log("**datarequest****",datarequest);
				 uni.request({
				 	url: requestUrl,
				 	dataType:'json',
				 	data: datarequest,
					method:'POST',
				 	success: (res) => {
				 	console.log(res);
				 
				 		
				 		
				 	},
				 	fail: (err) => {
				 		console.log('request fail', err);
				 		uni.showModal({
				 			content: err.errMsg,
				 			showCancel: false
				 		});
				 	},
				 	complete: () => {
				 		this.loading = false;
				 	}
				 });
				 
			}
		}
	}
</script>
<style>
	page {
		background: #FFF;
	}

	.ov {
		overflow: hidden;
	}

	.fl {
		float: left;
	}

	.fr {
		float: right;
	}

	.searchTopBox {
		width: 100%;
		background-color: #0b877f;
		height: 100upx;
		box-sizing: border-box;
		padding-top: 15upx;
	}

	.searchBoxRadius {
		width: 90%;
		height: 70upx;
		background-color: #fff;
		margin-left: 5%;
		overflow: hidden;
		border-radius: 35upx;
	}

	.searchBoxIcon {
		font-size: 40upx;
		margin-top: 20upx;
		margin-left: 20upx;
		float: left;
	}

	.searchBoxIpt {
		height: 70upx;
		line-height: 70upx;
		margin-left: 20upx;
		float: left;
	}

	.searchBotBox {
		width: 100%;
		margin-top: 30upx;
		padding: 15upx 3%;
		box-sizing: border-box;
	}

	.searchHistoryBox {
		width: 100%;
		box-sizing: border-box;
		overflow: hidden;
		margin-top: 40upx;
	}

	.searchHistoryBoxItem {
		float: left;
		font-size: 26upx;
		color: #666;
		line-height: 46upx;
		height: 46upx;
		padding: 0 20upx;
		border-radius: 23upx;
		margin-left: 15upx;
		margin-bottom: 20upx;
		border: 1px solid #ccc;
	}
	
	
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
	.mini-btn {
		margin-right: 10upx;
	}
</style>
