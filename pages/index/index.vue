<template>
	<view class="home">
		<scroll-view scroll-x class="navscroll">
			<view class="item" :class="index == navIndex ? 'active' : ''" v-for="(item, index) in navArr" :key="item.id" @click="clickNav(index, item.id)">{{item.classname}}</view>
		</scroll-view>
		<view class="content">
			<div class="row" v-for="(item,index) in newsArr" :key="item.id" @click="goDetail(item)">
				<newDetail :item=item></newDetail>
			</div>
		</view>
		<view class="nodata" v-if="!newsArr.length">
			<image src="../../static/images/nodata.png" mode="widthFix"></image>
		</view>
		<view class="loading" v-if="newsArr.length">
			<view v-if="loading==1">数据加载中...</view>
			<view v-if="loading==2">没有更多了~~~</view>
		</view>
	</view>
</template>

<script setup>
	import { ref } from 'vue'
	import { onLoad, onReachBottom } from '@dcloudio/uni-app'
	const navArr = ref([])
	const navIndex = ref(0)
	const newsArr = ref([])
	const currentPage = ref(1)
	const currentId = ref(50)
	const loading = ref(0) // 0:默认  1:加载中 2:没有更多
	onLoad(() => {
		getNavDate()
		getNewsData()
	})
	onReachBottom(() => {
		console.log("到底部了")
		if(loading == 2) {
			return
		}
		currentPage.value = currentPage.value + 1
		loading.value = 1
		getNewsData()
	})
	// 获取导航栏
	const getNavDate = () => {
		uni.request({
			url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
			success: res => {
				navArr.value = res.data
			}
		})
	}
	// 获取新闻列表数据
	const getNewsData = () => {
		uni.request({
			url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
			data:{
				cid: currentId.value,
				page: currentPage.value
			},
			success:res => {
				if(res.data.length == 0) {
					loading.value = 2
				}
				newsArr.value = [...newsArr.value, ...res.data]
			}
		})
	}
	// 点击导航切换
	const clickNav = (index, id) => {
		navIndex.value = index
		currentPage.value = 1
		currentId.value = id
		newsArr.value = []
		loading.value = 0
		getNewsData(id)
	}
	// 跳转详情页
	const goDetail = (item) => {
		uni.navigateTo({
			url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
		})
	}
</script>

<style lang="scss" scoped>
	.navscroll{
		height: 100rpx;
		background: #F7F8FA;
		white-space: nowrap;
		position: fixed;
		top:var(--window-top);
		left:0;
		z-index: 10;
		::-webkit-scrollbar {
			width: 4px !important;
			height: 1px !important;
			overflow: auto !important;
			background: transparent !important;
			-webkit-appearance: auto !important;
			display: block;
		}
		.item{
			font-size: 40rpx;
			display: inline-block;
			line-height: 100rpx;
			padding: 0 30rpx;
			color: #333;
			&.active{
				color: #31c27c;
			}
		}
	}
	.content{
		padding: 30rpx;
		padding-top: 130rpx;
		.row{
			border-bottom: 1px dotted #efefef;
			padding: 20rpx 0;
		}
	}
	.nodata{
		display: flex;
		justify-content: center;
		image{
			width: 360rpx;
		}
	}
	.loading{
		text-align: center;
		font-size: 26rpx;
		color: #888;
		line-height: 2em;
	}
</style>
