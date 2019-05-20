
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
				 globalData: {
				  userInfo: null,
				  version: "1.7",
				  appid: 'wx2c559ff4654897fb',
				  secret: '1589cb81e8fc44ee0c7cc97c519aa8db'
				}
			}
		},
		methods: {
			
		},
		onLoad() {
			 let that = this
    // 登录
    wx.login({
      success: res => {
        if (res.code) {
          // 发送 res.code 到后台换取 openId, sessionKey
          var d = that.globalData;
          var urlVal = 'https://api.weixin.qq.com/sns/jscode2session?appid=' + d.appid + '&secret=' + d.secret + '&js_code=' + res.code + '&grant_type=authorization_code';
          wx.request({
            url: urlVal,
            data: {},
            method: 'GET',
            success: function(res) {
              var obj = {};
              obj.openid = res.data.openid;  //获取到的openid
			  console.log("*****",obj.openid);
              wx.setStorageSync('user', obj); //存储openid 
            }
          })
        } else {
          console.log('登录失败！' + res.errMsg)
        }
      }
    })
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
