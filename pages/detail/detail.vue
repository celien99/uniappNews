<template>
	<view class="detail">
		<view class="title">{{detail.title}}</view>
		<view class="info">
			<view class="author">编辑: {{detail.author}}</view>
			<view class="time">发布日期: {{detail.posttime}}</view>
		</view>
		<view class="content">
			<rich-text :nodes="detail.content"></rich-text>
		</view>
		<view class="description">
			声明：本站的内容均采集与腾讯新闻，如果侵权请联系管理（513894357@qq.com）进行整改删除，本站进行了内容采集不代表本站及作者观点，若有侵犯请及时联系管理员，谢谢您的支持。
		</view>
	</view>
</template>

<script setup>
	import { ref } from 'vue'
	import { onLoad } from '@dcloudio/uni-app'
	import {parseTime} from "@/utils/tool.js"
	const options = ref()
	const detail = ref({})
	onLoad((e) => {
		options.value = e
		getDetail()
	})
	const getDetail = () => {
		uni.request({
			url:"https://ku.qingnian8.com/dataApi/news/detail.php",
			data:options.value,
			success:res => {
				res.data.posttime=parseTime(res.data.posttime)
				res.data.content=res.data.content.replace(/<img/gi,'<img style="max-width:100%"')
				detail.value = res.data
				saveHistory()
				uni.setNavigationBarTitle({
					title:detail.value.title
				})
			}
		})
	}
	const saveHistory = () => {
		let historyArr = uni.getStorageSync("historyArr") || []
		let item = {
			id: detail.value.id,
			classid: detail.value.classid,
			picurl: detail.value.picurl,
			title: detail.value.title,
			looktime:parseTime(Date.now())
		}
		let index = historyArr.findIndex(i => {
			return i.id == detail.value.id
		})
		if(index >= 0) {
			historyArr.splice(index, 1)
		}
		historyArr.unshift(item)
		historyArr= historyArr.splice(0,10)
		uni.setStorageSync("historyArr", historyArr)
	}
</script>

<style lang="scss" scoped>
.detail{
	padding:30rpx;
	.title{
		font-size: 46rpx;
		color:#333;
	}
	.info{
		background: #F6F6F6;
		padding:20rpx;
		font-size: 25rpx;
		color:#666;
		display: flex;
		justify-content: space-between;
		margin:40rpx 0;
	}
	.content{
		padding-bottom:50rpx;		
	}
	.description{
		background: #FEF0F0;
		font-size: 26rpx;
		padding:20rpx;
		color:#F89898;
		line-height: 1.8em;
	}
}
</style>
