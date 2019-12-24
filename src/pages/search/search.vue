<template>
	<view class="page">
		<view class="search-block">
			
			<view class="search-ico-wapper">
				<image src="../../static/icos/search.png" class="search-ico"></image>
			</view>
			
			<input 
				placeholder="搜索预告" 
				maxlength="10" 
				class="search-text" 
				confirm-type="search"
				@confirm="searchMe"
			/>
			
		</view>
		
		<!-- List -->
		
		<view class="movie-list page-block">
			<view class="movie-wapper" v-for="trailer in trailerList">
				<navigator :url="'../movie/movie?trailerId='+trailer.id">
					<image :src="trailer.cover" class="poster"></image>
				</navigator>
			</view>
			
		</view>
	</view>
</template>

<script>
	import common from "../../common/common.js"
	var serverUrl = common.serverUrl;
	export default {
		data() {
			return {
				keywords: '',
				trailerList: [],
				page: 1,
				totalPages: 1
			}
		},
		onLoad() {
			let that = this;
			
			that.pagedTrailerList('', 1, 9)
			// uni.showLoading({
			// 	mask: true,
			// 	title: "正在努力加载..."
			// });
			
			// uni.request({
			// 	url: serverUrl + '/search/list?qq=280555437&keywords=&page=&pageSize=15',
			// 	method: 'POST',
			// 	success: res => {
			// 		if(res.data.status == 200){
			// 			let trailerList = res.data.data.rows;
			// 			that.trailerList = trailerList;
			// 		}
			// 	},
			// 	fail: () => {},
			// 	complete: () => {
			// 		uni.hideLoading();
			// 	}
			// });
		},
		onReachBottom() {
			let that = this;
			let page = that.page + 1;
			let kw = that.keywords;
			let totalPages = that.totalPages;
			
			if(page > totalPages){
				return;
			}
			
			that.pagedTrailerList(kw, page, 15);
		},
		methods: {
			pagedTrailerList: function(kw, page, pageSize){
				let that = this;
				uni.showLoading({
					mask: true,
					title: "正在努力加载..."
				});
				
				uni.request({
					url: serverUrl + '/search/list?qq=280555437&keywords='+kw
																	+'&page='+page
																	+'&pageSize='+pageSize,
					method: 'POST',
					success: res => {
						if(res.data.status == 200){
							let tempList = res.data.data.rows;
							that.trailerList = that.trailerList.concat(tempList);
							that.keywords = kw;
							that.page = res.data.data.page;
							that.totalPages = res.data.data.total;
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading();
					}
				});
			},
			searchMe: function(e){
				let that = this;
				let value = e.detail.value;
				this.keywords = value;
				this.trailerList = [];
				
				this.pagedTrailerList(value, 1, 15);
			}
		}
	}
</script>

<style>
	@import url("search.css");
</style>
