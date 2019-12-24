<template name="trailerStars">
	<view class="movie-score-wapper">
		<image v-for="yellow in yellowScore" src="/static/icos/star-yellow.png" class="star-ico"></image>
		<image v-for="gray in grayScore" src="/static/icos/star-gray.png" class="star-ico"></image>
		<view class="movie-score" v-if="showNum == 1">
			{{innerScore}}
		</view>
	</view>
</template>

<script>
	export default {
		name: "trailerStars",
		props: {
			innerScore: {
				type: Number,
				default: 0
			},
			showNum: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				yellowScore: 0,
				grayScore: 5
			};
		},
		created() {
			let tempScore = 0;
			if (this.innerScore != null && this.innerScore != undefined) {
				tempScore = this.innerScore > 10 ? 10 : this.innerScore;
			}

			let yellowScore = parseInt(tempScore / 2);
			let grayScore = 5 - yellowScore;
			this.yellowScore = yellowScore;
			this.grayScore = grayScore;
		}
	}
</script>

<style>
	.movie-score-wapper {
		display: flex;
		flex-direction: row;
	}

	.star-ico {
		width: 20rpx;
		height: 20rpx;
		margin-top: 6rpx;
	}

	.movie-score {
		font-size: 12px;
		color: grey;
		margin-left: 8rpx;
	}
</style>
