<template>
	<view class="main-container">
		<scroll-view scroll-y="true" enable-flex="true" class="scroll">
			<view style="display: flex;flex-direction: column;height: 850rpx;">
				<!--头部展示账户余额 -->
				<uni-group>
					<uni-icons type="arrowright" size="20"></uni-icons>
					<text class="balance-font">
						<text>账户余额：</text>
						<text>{{balance}}{{unit}}</text>
					</text>
				</uni-group>
				<!--选择充值金额 -->
				<uni-group>
					<view class="title">
						<text>充值数量</text>
					</view>
					<view>
						<uni-grid :column="3" :show-border="false" :square="false" :highlight="false" @change="choiceChange">
							<uni-grid-item v-for="(sItem,sIndex) of firmMoneyChoices" :key="sIndex" :index="sIndex">
								<view class="choice-block" :class="sIndex === choiceIndex ? 'choice-highlight' : ''">
									<text>{{sItem.text}}</text>
								</view>
							</uni-grid-item>
							<!-- 用户自定义填写其他金额 -->
							<uni-grid-item :index="8">
								<template v-if="userChoosingMoney">
									<view class="choice-block">
										<input
											type="number"
											:value="moneyInputDisplay"
											@input="moneyInput"
											focus
											auto-blur
										/>
										<text>{{unit}}</text>
									</view>
								</template>
								<template v-else>
									<view class="choice-block">
										<text>其他数量</text>
									</view>
								</template>
							</uni-grid-item>
						</uni-grid>
					</view>
				</uni-group>
				<!--选择支付方式 -->
				<uni-group>
					<view class="title">
						<text>支付方式</text>
					</view>
					<view>
						<uni-grid :column="4" :show-border="false" :square="false" :highlight="false" @change="payMethodChange">
							<template v-for="(sItem,sIndex) of payMethods">
								<uni-grid-item :key="sIndex" :index="sIndex">
									<view class="pay-method-block" :class="sIndex === method ? 'pay-method-highlight' : ''">
										<image :src="sItem.icon" mode="aspectFit"></image>
										<text>{{sItem.text}}</text>
									</view>
								</uni-grid-item>
							</template>
						</uni-grid>
					</view>
				</uni-group>
				<!--说明 -->
				<view class="foot-font">
					<label>
						<checkbox :value="touch" checked="true" /><text>阅读并同意</text>
					</label>
					<uni-link href="" text="《充值服务协议》" showUnderLine="false" fontSize="10" color="#FF0000"></uni-link>
				</view>
				<view class="right">
					<uni-link href="" text="充值帮助>" showUnderLine="false" fontSize="10" color="#FF0000"></uni-link>
				</view>
			</view>
		</scroll-view>
		<view class="pay">
			<button type="primary" :disabled="money <= 0">立即支付{{moneyToFixed}}{{unit}}</button>
		</view>
	</view>
</template>

/**
 * @des 充值页面
 * 
 */
<script>
	export default {
		data(){
			const moneyChoices=[
				{
					money:1,
					text:"1元",
				},
				{
					money:10,
					text:"10元",
				},
				{
					money:50,
					text:"50元",
				},
				{
					money:100,
					text:"100元",
				},
				{
					money:500,
					text:"500元",
				},
				{
					money:1000,
					text:"1000元",
				},
				{
					money:5000,
					text:"5000元",
				},
				{
					money:10000,
					text:"10000元",
				},
				{
					money:-1,
					text:"其他数量",
				},
			];
			
			return {
				touch:'',
				unit:"元",
				balance:0,
				moneyChoices,
				choiceIndex:0,
				money:moneyChoices[0].money,
				userChoosingMoney:false,
				method:-1,
				payMethods:[
					{
						id:0,
						icon:"/static/icon/weixin.png",
						text:"微信",
						tip:"",
					}
				],
			}
		},
		methods:{
			// 充值金额选择
			choiceChange({detail:{index}}){
				this.choiceIndex=index;
				if(index !== 8){
					this.money=this.moneyChoices[index].money;
					this.userChoosingMoney=false;
				}
				else{
					this.money=0;
					this.userChoosingMoney=true;
				}
			},
			// 付款方式选择
			payMethodChange({detail:{index}}){
				this.method=index;
			},
			// 用户自定义金额输入数字
			moneyInput({detail:{value}}){
				value=parseInt(value);
				if(value >= 0){
					this.money=value;
				}else{
					this.money=0;
				}
			},
			// 提交充值信息
			async submitPayment(){
				money=this.money;	// 充值金额
				method=this.method;	// 充值方式
			}
		},
		computed:{
			firmMoneyChoices(){
				return this.moneyChoices.slice(0,8);
			},
			moneyInputDisplay(){
				return this.money > 0 ? this.money : "";
			},
			moneyToFixed(){
				return this.money.toFixed(1);
			}
		}
	}
</script>

<style lang="scss">
	$border-color:#d1d1d1;
	$background-color:#f8f8f8;
	$highlight-color:#ff8000;
	$border-width:2rpx;
	
	page {
		background-color: $background-color;
	}
	.main-container {
		display: flex;
		flex-direction: column;
		height:85vh;
	}
	.scroll {
		background-color: $background-color;
		top:var(--top-window-height) ;
		height:820rpx ;
	}
	.balance-font text:nth-child(1) {
		font-size: 25rpx ;
	}
	.balance-font text:nth-child(2) {
		font-size:35rpx ;
		color:$highlight-color ;
	}
	.title text{
		font-size: 30rpx ;
	}
	.choice-block {
		display:inline-flex ;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		margin:20rpx 10rpx ;
		height:60rpx ;
		width:200rpx ;
		border:$border-width solid $border-color ;
		border-radius: 15rpx;
		overflow: hidden;
		input {
			width:80% ;
			text-align: right !important;
		}
		text {
			font-size:20rpx ;
		}
	}
	.choice-highlight {
		color:$highlight-color ;
		border:1px solid $highlight-color ;
	}
	.foot-font{
		padding: 10rpx 20rpx;
	}
	.pay-method-block {
		display:flex;
		flex-direction: column;
		margin:20rpx 10rpx ;
		border:$border-width solid $border-color ;
		border-radius: 15rpx;
		justify-content: center ;
		align-items: center ;
		overflow: hidden ;
		image {
			height: 40rpx;
			width:40rpx ;
		}
		text {
			font-size: 20rpx ;
		}
	}
	.pay-method-highlight {
		border:$border-width solid $highlight-color ;
	}
	.foot text{
		font-size: 25rpx;
	}
	.right {
		text-align: right;
		margin-right: 20rpx;
	}
	.pay {
		margin-top: 7vh;
		button {
			width:90%;
			border-radius: 20rpx;
		}
	}
</style>
