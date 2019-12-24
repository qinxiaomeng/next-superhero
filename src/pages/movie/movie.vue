<template>
	<view class="page">
		<view class="player">
			<video 
				id="myTrailer"
				:src="trailerInfo.trailer"
				:poster="trailerInfo.poster" controls
				class="movie"></video>
		</view>
		<!-- 影片描述-->
		<view class="movie-info">
			<navigator :url="'../cover/cover?cover=' + trailerInfo.cover">
				<image :src="trailerInfo.cover" class="cover"></image>
			</navigator>
			<view class="movie-desc">
				<view class="title">{{trailerInfo.name}}</view>
				<view class="basic-info">{{trailerInfo.basicInfo}}</view>
				<view class="basic-info">{{trailerInfo.originalName}}</view>
				<view class="basic-info">{{trailerInfo.totalTime}}</view>
				<view class="basic-info">{{trailerInfo.releaseDate}}</view>
				<view class="score-block">
					<view class="big-score">
						<view class="score-words">综合评分：</view>
						<view class="movie-score">{{trailerInfo.score}}</view>
					</view>
					
					<view class="score-stars">
						<block v-if="trailerInfo.score >= 0">
							<trailerStars :innerScore="trailerInfo.score" :showNum="0"></trailerStars>
						</block>
						
						<view class="prise-counts">
							{{trailerInfo.prisedCounts}}  人点赞
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 影片描述-->
		<view class="line-wapper">
			<view class="line"></view>
		</view>
		<!-- 剧情介绍-->
		<view class="plots-block">
			<view class="plots-title">剧情介绍</view>
			<view class="plots-desc">{{trailerInfo.plotDesc}}</view>
		</view>
		<!-- 剧情介绍-->
		
		<!-- 演职人员 start -->
		<view class="scroll-block">
			<view class="plots-title">演职人员</view>
			<scroll-view scroll-x class="scroll-list">
				
				<view class="actor-wapper" v-for="(director,staffIndex) in directorArray">
					<image 
						:src="director.photo"
						class="single-actor"
						mode="aspectFill"
						@click="lookStaffs"
						:data-staffIndex="staffIndex"
					></image>
					<view class="actor-name">{{director.name}}</view>
					<view class="actor-role">{{director.actName}}</view>
				</view>
				
				<view class="actor-wapper" v-for="(actor,actorIndex) in actorArray">
					<image 
						:src="actor.photo"
						class="single-actor"
						mode="aspectFill"
						@click="lookStaffs"
						:data-staffIndex="actorIndex + directorArray.length"
					></image>
					<view class="actor-name">{{actor.name}}</view>
					<view class="actor-role">{{actor.actName}}</view>
				</view>
			</scroll-view>
		</view>
		<!-- 演职人员 end -->
		
		<!-- 剧照 start -->
		<view class="scroll-block">
			<view class="plots-title">剧照</view>
			<scroll-view scroll-x class="scroll-list">
				<image 
					v-for="(img, imgIndex) in plotPicsArray"
					:src="img"
					class="plot-image"
					mode="aspectFill"
					@click="lookMe"
					:data-imgIndex="imgIndex"
				></image>
			</scroll-view>
		</view>
		<!-- 剧照 end -->
	</view>
</template>

<script>
	import trailerStars from "../../components/trailerStars.vue"
	import common from "../../common/common.js"
	var serverUrl = common.serverUrl;
	export default {
		components:{
			trailerStars
		},
		data() {
			return {
				trailerInfo: {},
				plotPicsArray: [],
				directorArray: [],
				actorArray: []
			}
		},
		onReady() {
			this.videoContext = uni.createVideoContext("myTrailer");
		},
		onHide() {
			this.videoContext.pause();
		},
		onShow() {
			if(this.videoContext){
				this.videoContext.play();
			}
		},
		onLoad(params){
			let that = this;
			let trailerId = params.trailerId;
			
			//通过api的方式设置导航栏
			uni.setNavigationBarColor({
				backgroundColor: "#000000",
				frontColor: "#ffffff"
			})
			
			uni.request({
				url: serverUrl + '/search/trailer/' + trailerId + '?qq=280555437',
				method: 'POST',
				success: res => {
					if(res.data.status == 200){
						
						let trailerInfo = res.data.data;
						that.trailerInfo = trailerInfo;
						let plotPicsArray = JSON.parse(trailerInfo.plotPics);
						that.plotPicsArray = plotPicsArray;
					}
				},
				complete: () => {}
			});
			//导演
			uni.request({
				url: serverUrl + '/search/staff/' + trailerId + '/1?qq=280555437',
				method: 'POST',
				success: res => {
					if(res.data.status == 200){
						that.directorArray = res.data.data;
						console.log(that.directorArray);
					}
				},
				complete: () => {}
			});
			//演员
			uni.request({
				url: serverUrl + '/search/staff/' + trailerId + '/2?qq=280555437',
				method: 'POST',
				success: res => {
					if(res.data.status == 200){
						that.actorArray = res.data.data;
						console.log(that.actorArray);
					}
				},
				complete: () => {}
			});
		},
		//此函数仅支持在小程序端分享
		onShareAppMessage() {
			var me = this;
			return {
				title: me.trailerInfo.name,
				path: '/page/movie/movie?trailerid=' + me.trailerInfo.id,
				imageUrl: me.trailerInfo.cover
			}
		},
		//5+APP、H5分享
		onNavigationBarButtonTap(e) {
			var index = e.index;
			// console.log(index);
			var me = this;
			var trailerInfo = me.trailerInfo;
			var trailerId = trailerInfo.id;
			var trailerName = trailerInfo.name;
			var cover = trailerInfo.cover;
			var poster = trailerInfo.poster;
			
			// index 为0 则分享
			if (index == 0) {
				uni.share({
					provider: "weixin",
					scene: "WXSenceTimeline",
					type: 0,
					href: "http://www.imovietrailer.com/#/pages/movie/movie?trailerId=" + trailerId,
					title: "NEXT超英预告：《" + trailerName + "》",
					summary: "NEXT超英预告：《" + trailerName + "》",
					imageUrl: cover,
					success: function (res) {
						console.log("success:" + JSON.stringify(res));
					}
				});
			}
		},
		methods: {
			lookMe(e){
				var me = this;
				var imgIndex = e.currentTarget.dataset.imgindex;
				
				uni.previewImage({
					current: me.plotPicsArray[imgIndex],
					urls: me.plotPicsArray
				})
			},
			lookStaffs(e) {
				var me = this;
				var staffIndex = e.currentTarget.dataset.staffindex;
				
				// 拼接导演和演员的数组，成为一个新数组
				var directorArray = me.directorArray;
				var actorArray = me.actorArray;
				var newStaffArray = [];
				newStaffArray = newStaffArray.concat(directorArray).concat(actorArray);
				
				var urls = [];
				for (var i = 0; i < newStaffArray.length ; i ++) {
					var tempPhoto = newStaffArray[i].photo;
					urls.push(tempPhoto);
				}
				
				uni.previewImage({
					current: urls[staffIndex],
					urls: urls
				})
			}
		}
	}
</script>

<style>
	@import url("movie.css");
</style>
