<template>
	<view class="user">
		<view class="top">
			<image src="../../static/images/history.png" mode=""></image>
			<view class="text">浏览历史</view>
		</view>
		<view class="content">
			<view class="row" v-for="item in listArr" @click="goDetail(item)">
				<newDetail :item="item"></newDetail>
			</view>			
		</view>
		
		<view class="nohistory" v-if="!listArr.length">
			<image src="../../static/images/nohis.png" mode="widthFix"></image>
			<view class="text">暂无浏览记录</view>
		</view>
		
		
	</view>
</template>

<script setup>
	import { ref } from 'vue'
	import { onShow} from '@dcloudio/uni-app'
	const listArr = ref([])
	onShow(() => {
		getData()
	})
	const goDetail = (item) => {
		uni.navigateTo({
			url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
		})
	}
	const getData = () => {
		let hisArr=uni.getStorageSync("historyArr") || []
		listArr.value = hisArr
	}
</script>

<style lang="scss" scoped>
.user{
	.top{
		padding:50rpx 0;
		background: #F8F8F8;
		color:#666;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image{
			width: 150rpx;
			height: 150rpx;
		}
		.text{
			font-size: 38rpx;		
			padding-top: 20rpx;
		}
	}
	.content{
		padding:30rpx;
		.row{
			border-bottom:1px dotted #efefef;
			padding:20rpx 0;
		}
	}
	.nohistory{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image{
			width: 450rpx;
		}
		.text{
			font-size: 26rpx;
			color:#888;
		}
	}
}
</style>>
