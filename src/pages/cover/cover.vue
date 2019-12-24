<template>
	<view class="black">
		<image :src="imageUrl" 
			mode="widthFix" 
			class="cover"
			@longpress="operator"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imageUrl: ""
			}
		},
		onLoad(params) {
			this.imageUrl = params.cover;
		},
		methods: {
			operator() {
				var me = this;
				uni.showActionSheet({
					itemList: ["保存图片到本地"],
					success: function(res){
						// 下标为0则下载
						if (res.tapIndex == 0) {
							// console.log("进入下载。。。");
							uni.showLoading({
								title: "图片保存中..."
							})
							uni.downloadFile({
								url: me.cover,
								success: function(result) {
									var tempFilePath = result.tempFilePath;
									// console.log(tempFilePath);
									uni.saveImageToPhotosAlbum({
										filePath: tempFilePath,
										success: function() {
											uni.showToast({
												title: "保存成功",
												duration: 2000
											})
										},
										complete: function() {
											uni.hideLoading();
										}
									})
								}
							})
						}
					}
				})
			}
		}
	}
</script>

<style>
.black {
	width: 100%;
	height: 100%;
	background-color: #000000;
	
	display: flex;
	flex-direction: column;
	justify-content: center;
	position: fixed;
}

.cover {
	align-self: center;
}
</style>
