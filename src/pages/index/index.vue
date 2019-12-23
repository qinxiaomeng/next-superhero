<template>
	<view class="page">
		<!-- 轮播图 start -->
		<swiper :indicator-dots="true" :autoplay="true" class="carousel">
			<swiper-item v-for="carousel in carouselList" :key="carousel.id">
				<image :src="carousel.image" class="carousel"></image>
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
						<image :src="superhero.cover" class="poster"></image>
					</view>
					<view class="movie-name">{{superhero.name}}</view>
					<view class="movie-score-wapper">
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-gray.png" class="star-ico"></image>
						<view class="movie-score">
							9.1
						</view>
					</view>
				</view>
			</view>
			
		</scroll-view>
		<!-- 热门超英 -->
	</view>
</template>

<script>
	import common from "../../common/common.js"
	export default {
		data() {
			return {
				carouselList: [],
				hotSuperheroList: []
			}
		},
		onLoad() {

		},
		methods: {

		},
		onLoad() {
			let that = this;
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
		}
	}
</script>

<style>
	@import url("index.css");
</style>
