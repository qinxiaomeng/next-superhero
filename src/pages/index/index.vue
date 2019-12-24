<template>
	<view class="page">
		
		<!-- 轮播图 start -->
		<swiper :indicator-dots="true" :autoplay="true" class="carousel">
			<swiper-item v-for="carousel in carouselList" :key="carousel.id">
				<navigator :url="'../movie/movie?trailerId='+carousel.movieId">
					<image :src="carousel.image" class="carousel"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 轮播图 end -->
		<!-- 热门超英 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/hot.png" class="hot-ico"></image>
				<view class="hot-title">
					热门超英
				</view>
			</view>
		</view>
		<scroll-view scroll-x="true" class="page-block hot">
			<view class="single-poster" v-for="superhero in hotSuperheroList">
				<view class="poster-wapper">
					<view >
						<navigator :url="'../movie/movie?trailerId='+superhero.id">
							<image :src="superhero.cover" class="poster"></image>
						</navigator>
					</view>
					<view class="movie-name">{{superhero.name}}</view>
					<trailer-stars :innerScore="superhero.score" :showNum="1"></trailer-stars>
				</view>
			</view>
			
		</scroll-view>
		<!-- 热门超英 -->
		
		<!-- 热门预告 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/interest.png" class="hot-ico"></image>
				<view class="hot-title">
					热门预告
				</view>
			</view>
		</view>
		
		<view class="page-block hot-movies">
			<video
				:id="trailer.id"
				:data-plaingIndex="trailer.id"
				@play="meIsPlaing"
				class="hot-movies-single"
				v-for="trailer in hotTrailerList"
				:poster="trailer.poster"
				:src="trailer.trailer" controls></video>
		</view>
		<!-- 热门预告 -->
		<!-- 猜你喜欢 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/guess-u-like.png" class="hot-ico"></image>
				<view class="hot-title">
					猜你喜欢
				</view>
			</view>
		</view>
		
		<view class="page-block guess-u-like">
			<view class="single-like-movie" v-for="(gul,gIndex) in guessULikeList">
				<navigator :url="'../movie/movie?trailerId='+gul.id">
					<image :src="gul.cover" class="like-movie"></image>
				</navigator>
				<view class="like-desc">
					<view class="like-title">{{gul.name}}</view>
					<trailer-stars :innerScore="gul.score"></trailer-stars>
					<view class="like-info">{{gul.basicInfo}}</view>
					<view class="like-info">{{gul.releaseDate}}</view>
				</view>
				<view class="movie-oper" :data-gIndex="gIndex" @click="praiseMe">
					<image src="../../static/icos/praise.png" class="praise-ico"></image>
					<view class="praise-me">赞一下</view>
					<view :animation="animationDataArr[gIndex]" class="praise-me animation-opacity">+1</view>
				</view>
			</view>
			
		</view>
		<!-- 猜你喜欢 -->
	</view>
</template>

<script>
	import common from "../../common/common.js"
	import trailerStars from "../../components/trailerStars.vue"
	export default {
		components:{
			trailerStars
		},
		data() {
			return {
				carouselList: [],
				hotSuperheroList: [],
				hotTrailerList: [],
				guessULikeList: [],
				animationData: {},
				animationDataArr: [{},{},{},{},{}]
			}
		},
		methods: {
			praiseMe: function(e){
				// #ifdef APP-PLUS || MP-WEIXIN
				let index = e.currentTarget.dataset.gindex;
				// console.log(index)
				// return;
				
				//构建动画数据，并且通过step表示这组动画的完成
				this.animation.translateY(-60).opacity(1).step({duration: 400});
				
				//导出动画数据到view组件，实现组件的动画效果
				this.animationData = this.animation;
				this.animationDataArr[index] = this.animationData.export();
				//还原动画
				setTimeout(function() {
					this.animation.translateY(0).opacity(0).step({duration: 0});
					this.animationData = this.animation;
					this.animationDataArr[index] = this.animationData.export();
				}.bind(this), 500);
				// #endif
			},
			refresh: function(){
				let that = this;
				let serverUrl = common.serverUrl;
				uni.showLoading({
					mask:true
				})
				//查询猜你喜欢
				uni.request({
					url: serverUrl + '/index/guessULike?qq=280555437',
					method: 'POST',
					success: res => {
						if (res.data.status == 200) {
							let guessULikeList = res.data.data;
							that.guessULikeList = guessULikeList;
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading();
						uni.stopPullDownRefresh();
					}
				});
			},
			meIsPlaing: function(e){
				var me = this;
				var trailerId = "";
				
				if(e){
					trailerId = e.currentTarget.dataset.plaingindex;
					me.videoContext = uni.createVideoContext(trailerId)
				}
				var hotTrailerList = me.hotTrailerList;
				for (var i = 0; i < hotTrailerList.length ; i ++) {
					var tempId = hotTrailerList[i].id;
					if (tempId != trailerId) {
						uni.createVideoContext(tempId).pause();
					}
				}
			}
		},
		onShow() {
			if(this.videoContext){
				this.videoContext.play();
			}
		},
		onHide() {
			if (this.videoContext) {
				this.videoContext.pause();
			}
		},
		onPullDownRefresh(){
			this.refresh();
		},
		onUnload() {
			//页面卸载的时候清除动画数据
			this.animationData = {}
		},
		onLoad() {
			let that = this;
			// #ifdef APP-PLUS || MP-WEIXIN
			//在页面创建的时候创建一个临时动画
			this.animation = uni.createAnimation();
			// #endif
			
			let serverUrl = common.serverUrl;
			uni.request({
				url: serverUrl + '/index/carousel/list?qq=280555437',
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						let carouselList = res.data.data;
						that.carouselList = carouselList;
					}
				}
			});
			//热门超英
			uni.request({
				url: serverUrl + '/index/movie/hot?qq=280555437&type=superhero',
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						let hotSuperheroList = res.data.data;
						that.hotSuperheroList = hotSuperheroList;
					}
				},
				fail: () => {},
				complete: () => {}
			});
			
			
			//视频
			uni.request({
				url: serverUrl + '/index/movie/hot?qq=280555437&type=trailer',
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						let hotTrailerList = res.data.data;
						that.hotTrailerList = hotTrailerList;
					}
				},
				fail: () => {},
				complete: () => {}
			});
			
			that.refresh();
		}
	}
</script>

<style>
	@import url("index.css");
</style>
