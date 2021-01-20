<template>
	<view class="main-container">
		<view class="head">
			<view class="date-picker">
				<picker mode="date" :value="date" fields="month" @change="dateChange">
					<text>{{date}}</text>
					<uni-icons type="arrowdown" />
				</picker>
			</view>
			<view class="brief">
				<text>支出  ¥{{spend}}</text>
				<text>收入  ¥{{income}}</text>
			</view>
		</view>
		<view class="bill">
			<uni-list>
				<template v-for="(sItem,sIndex) of bills">
					<uni-list-item :key="sIndex" direction="row">
						<view slot="header" class="bill-header">
							<image :src="sItem.icon"></image>
						</view>
						<view slot="body" class="bill-body">
							<text style="font-size: 30rpx;font-weight: 600;">{{sItem.title}}</text>
							<text>{{sItem.time}}</text>
						</view>
						<view slot="footer" class="bill-footer">
							<strong><text>{{sItem.money}}</text></strong>
						</view>
					</uni-list-item>
				</template>
			</uni-list>
		</view>
		<uni-load-more
		:status="nomoreBills ? 'contentnomore' : bottomLoading ? 'loading' : 'contentrefresh'"
		:contentText="{contentrefresh:'加载中'}"
		></uni-load-more>
	</view>
</template>

<script>
	const dt=new Date();
	const date=`${dt.getFullYear()}-${dt.getMonth()<10?"0"+(dt.getMonth()+1):dt.getMonth()+1}`;
	export default {
		data(){
			return {
				spend:100,
				income:200,
				date,
				page:1,
				bottomLoading:false,
				nomoreBills:false,
				bills:[
					{
						icon:"/static/icon/money.png",
						title:"消费标题",
						type:"",
						time:"11:00",
						money:100,
						direction:0
					}
				]
			}
		},
		methods:{
			// picker选择日期
			dateChange(e){
				this.date=e.target.value;
			},
			//请求账单数据
			async getBills(){
				const year=this.year;	// 账单年份
				const month=this.month;	// 账单月份
				const page=this.page+1;	// 请求第几页
				await new Promise(resolve => setTimeout(resolve, 2000));
				for(let i=0;i<10;i++){
					this.bills.push({
						icon:"/static/icon/money.png",
						title:"消费标题",
						type:"",
						time:"11:00",
						money:100,
						direction:0
					});
				}
				uni.stopPullDownRefresh();
				this.bottomLoading=false;
			},
		},
		// 页面刚开始加载拉取账单数据
		onReady(){
			this.getBills();
		},
		// 页面下滑到底部拉去新一页账单数据
		onReachBottom(){
			this.bottomLoading=true;
			this.getBills();
		},
		// 页面下拉重新拉去该年月第一页数据
		// 下拉刷新需要在pages.json文件额外配置
		onPullDownRefresh(){
			this.page=1;
			this.bills=[];
			this.getBills();
		},
		computed:{
			year(){
				return parseInt(this.date.split("-")[0]);
			},
			month(){
				return parseInt(this.date.split("-")[1]);
			}
		},
	}
</script>

<style lang="scss">
	$background-color:#f5f5f5;
	page {
		background-color: $background-color;
	}
	.main-container {
		display: flex;
		flex-direction: column;
		margin-top:10rpx;
	}
	.head {
		display: flex;
		flex-direction: column;
		margin-left: 10rpx;
	}
	.date-picker {
		display: inline-flex;
		picker {
			text {
				margin-right: 10rpx;
			}
		}
	}
	.brief text:nth-child(n+1) {
		color:#999999 ;
		font-size:20rpx ;
		margin-right:40rpx ;
	}
	.head view {
		padding-top:10rpx ;
	}
	.bill {
		margin-top: 10rpx;
	}
	.bill-header {
		image {
			width: 80rpx;
			height: 80rpx;
			border-radius: 15%;
		}
	}
	.bill-body {
		display:flex;
		flex-direction: column;
		margin-left: 20rpx ;
		margin-right: auto;
	}
	.bill-body text:nth-child(n+2) {
		color:#C0C0C0 ;
	}
	.bill-footer {
		margin-right: 20rpx ;
		color: #FAAC58;
		padding-top: 20rpx;
	}
</style>
