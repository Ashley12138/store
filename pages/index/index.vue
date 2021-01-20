<template>
	<view class="all">
		<view class="main-container">
			<view class="title">
				<text>欢迎使用 {{appName}}</text>
			</view>
			<view class="describe">
				<text>授权微信头像、昵称</text>
			</view>
			<image src="https://n.sinaimg.cn/sinacn20106/212/w2048h1364/20190828/cded-icuacsa3953451.jpg"></image>
			<view class="detail">
				<text>为提供优质服务，{{appName}}需要获取你的以下信息：</text>
				<ul>
					<template v-for="(sItem,sIndex) of auths">
						<li :key="sIndex">
							<text>{{sItem.text}}</text>
						</li>
					</template>
				</ul>
			</view>
			<button class="auth-button" type="primary" open-type="getUserInfo" @getuserinfo="appLoginWx">授权进入{{appName}}</button>
		</view>
	</view>

</template>

/**
* @description 授权页面
*
*/
<script>
	export default {
		data() {
			return {
				appName: "美食汇",
				auths: [{
					id: 0,
					text: "你的公开信息(头像、昵称等)",
				}, ]
			}
		},
		onLoad() {

		},
		methods: {
			appLoginWx() {
				// #ifdef MP-WEIXIN
				uni.getProvider({
					service: 'oauth',
					success: function(res) {
						if (~res.provider.indexOf('weixin')) {
							uni.login({
								provider: 'weixin',
								success: (res2) => {
									uni.getUserInfo({
										provider: 'weixin',
										success: (info) => { //这里请求接口
											console.log(res2);
											console.log(info);
										},
										fail: () => {
											uni.showToast({
												title: "微信登录授权失败",
												icon: "none"
											});
										}
									})

								},
								fail: () => {
									uni.showToast({
										title: "微信登录授权失败",
										icon: "none"
									});
								}
							})

						} else {
							uni.showToast({
								title: '请先安装微信或升级版本',
								icon: "none"
							});
						}
					}
				});
				//#endif
			},
			appLogoutWx() {
				uni.logout({
					success: function(res) {
						console.log(res)
					},
					fail: function(res) {
						console.log(res)
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.all{
		background-color: #e9e9e9;
		height: 100vh;
	}
	.main-container {
		display: flex;
		flex-direction: column;
		margin:0 30rpx;
		padding: 30rpx 0;
		image {
			width: 300rpx;
			height: 300rpx;
			border-radius: 50%;
			margin-top: 30rpx;
		}
	}

	.title {
		text {
			font-size: 45rpx;
			font-weight: 500;
		}
	}

	.describe {
		margin-top: 20rpx;

		text {
			color: #C8C7CC;
		}
	}

	image {
		margin-top: 20rpx;
		align-self: center;
		width: 100%;
		height: 500rpx;
	}

	.detail {
		margin-top: 60rpx;
		border-top: 1rpx solid #999;
		padding-top: 80rpx;

		ul {
			display: flex;
			flex-direction: column;
			margin-top: 10rpx;
			padding-inline-start: 12rpx;
		}

		li {
			list-style: none;

			text {
				font-weight: 300;
				font-size: 20rpx !important;
				color: #b9b8bc;
			}
		}

		li:before {
			content: "";
			display: inline-flex;
			width: 10rpx;
			height: 10rpx;
			background-color: #b9b8bc;
			border-radius: 50%;
			margin-right: 5px;
		}
	}

	.detail text:nth-child(1) {
		font-weight: 500;
		font-size: 25rpx;
	}

	.auth-button {
		font-size: 36rpx;
		margin-top: 60rpx;
		border-radius: 40rpx;
		height: 80rpx;
		width: 80%;
		line-height: 80rpx;
	}
</style>
