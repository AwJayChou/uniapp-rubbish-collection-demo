<template>
	<view class="content">
		<view style="width: 100%;padding:10px 20px;">
			<button @click="btnPhoto">请选择一张照片</button>
			<image :src="imgfile" style="width: 100%;" mode="widthFix"></image>
		</view>
		<view style="text-align: center;font-size: 14px;color: #999;">识别结果</view>
		<view style="text-align: center;height: 30px;line-height: 30px;">
			{{selectedName}}
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imgfile: '',
				selectedName: ""
			}
		},
		onLoad() {

		},
		methods: {
			btnPhoto: function() {
				uni.chooseImage({
					count: 1,
					success: (res) => {
						console.log(" readFileData ", res)
						this.imgfile = res.tempFilePaths[0];
						console.log(" imgFile ", this.imgfile)
						this.readImage2Base64(this.imgfile);
					},
				})
			},
			readImage2Base64: function(path) {
				wx.getFileSystemManager().readFile({
				  filePath: path, 
				  encoding: 'base64', 
				  success:async (res)=> { 
				    // console.log(res);
					const result=await this.queryPicName(res.data);
					
					this.parseResults(result.result);
				  }
				})
			},
			async queryPicName(img64) {
				// 文档：https://ai.baidu.com/ai-doc/REFERENCE/Ck3dwjhhu
				// 先通过自己申请的AppId和Secret去鉴权认证获取AccessToken
				const client_id = "your_client_id"
				const client_secret = "your_client_secret"
				var [error, res] = await uni.request({
					url:"https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id="+client_id+"&client_secret="+client_secret+"&"
				});
				let access_token = res.data.access_token;
				
				var [error, res] = await uni.request({
					url:"https://aip.baidubce.com/rest/2.0/image-classify/v2/advanced_general",
					method:"post",
					header:{
						"Content-Type": "application/x-www-form-urlencoded"
					},
					data: {
						access_token:access_token,
						image: img64
					}
				});
				
				console.log(res);
				return res.data;
			},
			parseResults(result){
				uni.hideLoading();
				
				this.recResults=result;
				
				let itemList=[];
				let abs_result_index;
				for(let i=0;i<result.length;i++){
					if(result[i].score>.7){
						abs_result_index=i;
						break;
					}
					itemList.push(result[i].keyword+""+result[i].score);
				}
				
				if(abs_result_index>=0){
					this.selectedName = result[0].keyword
					// 需要账号支持
					// this.selectRecResult(abs_result_index);
					return;
				}
				
				uni.showActionSheet({
					itemList:itemList,
					success: (res) => {
						// this.selectRecResult(res.tapIndex);
					}
				});
			},
			
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
