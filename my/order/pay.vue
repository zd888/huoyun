<template>
	<view>
		<view class="headtop"></view>
		<view class="content">
			<!-- <view class="stte">正在寻找车主，请等待</view> -->
			<!-- <view class="stte">司机已接单，正在赶往当前地点</view> -->
			<view class="stte" v-if="order.indentState==0">订单待支付，请先完成支付</view>
			<view class="stte" v-if="order.indentState==1">正在呼叫附近货车司机</view>
			<view class="stte" v-if="order.indentState==2">司机已接单</view>
			<view class="stte" v-if="order.indentState==3">装货中</view>
			<view class="stte" v-if="order.indentState==4">运输中</view>
			<view class="stte" v-if="order.indentState==5">卸货中</view>
			<view class="stte" v-if="order.indentState==6">订单已完成</view>
			<view class="stte" v-if="order.indentState==7">订单完成待补费</view>
			<view class="stte" v-if="order.indentState==8">订单已取消</view>
			<view class="boxa" v-if="order.indentState!=0&&order.indentState!=1&&order.indentState!=8">
				<view class="flex align-center justify-between margin-lr">
					<view>
						<view v-show="order.numberPlate" class="text-38 text-bold">{{order.numberPlate}}</view>
						<!-- <view class="text-bold margin-top-xs">黑·奔驰</view> -->
						<view class="margin-tb-sm flex align-center" style="color: #999999;font-size: 26rpx;">
							<view v-show="order.riderNickName">{{order.riderNickName}}</view>
							<view class="margin-lr-sm" style="width: 1rpx;height: 20rpx;background: #A0A0A0;">
							</view>
							<view v-show="order.indentCount">接单{{order.indentCount}}次</view>
						</view>
					</view>
					<view>
						<image src="../static/che.png" style="width: 237rpx;height:116rpx;"></image>
					</view>
				</view>
				<view class="margin-tb-sm" style="width: 100%;height:1rpx;background: #F2F2F2;"></view>

				<view class="flex align-center justify-between " style="padding: 0 70rpx;">
					<view class="flex align-center">
						<image src="../static/kefu.png" style="width: 46rpx;height: 42rpx;"></image>
						<view @click="changekefu()" class="margin-left-sm">联系客服</view>
					</view>
					<view style="width: 1rpx;height: 59rpx;background: #E6E6E6;"></view>
					<view class="flex align-center" @click="callPhone(order.riderPhone)">
						<image src="../static/phone.png" style="width: 44rpx;height: 42rpx;"></image>
						<view class="margin-left-sm">联系司机</view>
					</view>
				</view>
			</view>
			<view class="box padding-lr">
				<view class="addbox " v-show="order.startAddress">
					<view class="add_cont" @click="openMap(order.startLat,order.startLng,order.startAddress)">
						<view class="green"></view>
						<view>
							<view class="add_tit">{{order.startAddress}}</view>
							<view class="flex align-center">
								<view class="add_tit margin-top-xs">{{order.startUserName}}</view>
								<view class="add_tit margin-left-xl margin-top-xs">{{order.startUserPhone}}</view>
							</view>
						</view>
					</view>

				</view>
				<view style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
				<view v-show="order.endAddress" class="addbox ">
					<view class="add_cont" @click="openMap(order.endLat,order.endLng,order.endAddress)">
						<view class="orgin"></view>
						<view>
							<view class="add_tit">{{order.endAddress}}</view>
							<view class="flex align-center">
								<view class="add_tit margin-top-xs">{{order.endUserName}}</view>
								<view class="add_tit margin-left-xl margin-top-xs">{{order.endUserPhone}}</view>
							</view>
						</view>
					</view>

				</view>
			</view>


			<view class="box padding" style="color: #1C1C1C;">
				<view class="titls">订单信息</view>
				<view v-show="order.indentNumber" class="flex align-center justify-between margin-top">
					<view class="text-gray">订单号码</view>
					<view @click.stop="copyClick(order.indentNumber)">{{order.indentNumber}}
						<image src="../static/copy.png" style="width:28rpx;height: 28rpx;"></image>
					</view>
				</view>
				<view v-show="order.createTime" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">订单时间</view>
					<view>{{order.createTime}}</view>
				</view>
				<view v-if="order.indentType == 2" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">预约时间</view>
					<view>{{order.hopeTime}}</view>
				</view>
				<view v-show="order.peopleNum" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">跟车人数</view>
					<view>{{order.peopleNum}}人</view>
				</view>
				<view v-show="order.customPhone" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">联系方式</view>
					<view>{{order.customPhone}}</view>
				</view>
				<view v-if="order.modelName" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">订单车型</view>
					<view>{{order.modelName}}</view>
				</view>
				<view v-if="order.typeName" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">订单车厢</view>
					<view>{{order.typeName}}</view>
				</view>
				<view v-if="order.couponMoney" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">优惠金额</view>
					<view>{{order.couponMoney}}元</view>
				</view>
				<view v-if="order.remarks" class="flex align-center justify-between margin-tb-xl">
					<view class="text-gray">订单备注</view>
					<view>{{order.remarks}}</view>
				</view>
				<view v-if="order.indentState!=0 && order.indentState!=8" class="flex align-center justify-between">
					<view class="text-gray">支付方式</view>
					<view v-if="Number(order.modeOfPayment) === 0">余额支付</view>
					<view v-if="Number(order.modeOfPayment) === 1">微信支付</view>
					<view v-if="Number(order.modeOfPayment) === 2">支付宝支付</view>
				</view>
			</view>
			<view class="box padding">
				<view class="flex align-center justify-between">
					<view class="text-lg text-bold">费用明细</view>
					<!-- <view class="text-38 text-bold" style="color: #FF4B36;">￥{{order.indentMoney}}</view> -->
				</view>

				<view v-if="order.moneyJson&&order.moneyJson.startPrice" style="margin-top: 20rpx;"
					class="flex align-center justify-between">
					<view class="text-gray">起步价</view>
					<view>￥{{order.moneyJson.startPrice}}</view>
				</view>
				<view v-if="order.moneyJson&&order.moneyJson.startKilometer" style="margin-top: 20rpx;"
					class="flex align-center justify-between">
					<view class="text-gray">起步公里数</view>
					<view>{{order.moneyJson.startKilometer}}km</view>
				</view>
				<view v-if="order.moneyJson&&order.moneyJson.distance" style="margin-top: 20rpx;"
					class="flex align-center justify-between">
					<view class="text-gray">总公里数</view>
					<view>{{order.moneyJson.distance}}km</view>
				</view>
				<view v-if="order.moneyJson&&order.moneyJson.metre" style="margin-top: 20rpx;"
					class="flex align-center justify-between">
					<view class="text-gray">超出公里数</view>
					<view>{{order.moneyJson.metre}}km</view>
				</view>
				<view v-if="order.moneyJson&&order.moneyJson.outKilometerMoney" style="margin-top: 20rpx;"
					class="flex align-center justify-between">
					<view class="text-gray">超出公里数部分费用</view>
					<view>￥{{order.moneyJson.outKilometerMoney}}</view>
				</view>
				<view v-if="order.moneyJson&&order.moneyJson.couponMoney" style="margin-top: 20rpx;"
					class="flex align-center justify-between">
					<view class="text-gray">优惠券减免金额</view>
					<view>￥{{order.moneyJson.couponMoney}}</view>
				</view>

				<view v-show="order.indentMoney" style="margin-top: 20rpx;" class="flex align-center justify-between">
					<view class="text-gray">订单金额</view>
					<view>￥{{order.indentMoney}}</view>
				</view>
				<view v-if="order.feesMoney" style="margin-top: 20rpx;" class="flex align-center justify-between">
					<view class="text-gray">补费金额</view>
					<view>￥{{order.feesMoney}}</view>
				</view>

			</view>
			<view class="box padding" v-if="order.transportImg" style="color: #999999;">
				<view class="titls">装货确认图片</view>
				<view style="margin-top: 20rpx;" class="imagess flex align-center justify-between">
					<image @click="chooseImg(index,order.transportImg.split(','))"
						v-for="(item,index) in order.transportImg.split(',')" :src="item"
						style="width: 200rpx;height: 200rpx;border-radius: 24rpx;" mode="">
					</image>
				</view>
			</view>
			<view class="box padding" v-if="order.finishImg" style="color: #999999;">
				<view class="titls">订单确认图片</view>
				<view style="margin-top: 20rpx;" class="imagess flex align-center justify-between">
					<image @click="chooseImg(index,order.finishImg.split(','))"
						v-for="(item,index) in order.finishImg.split(',')" :src="item"
						style="width: 200rpx;height: 200rpx;border-radius: 24rpx;" mode="">
					</image>
				</view>
			</view>
			<view v-if="order.indentState == 7" class="box padding flex align-center justify-between">
				<view class="text-lg text-bold">补费金额</view>
				<view class="text-38 text-bold" style="color: #FF4B36;">￥{{order.feesMoney}}</view>
			</view>


			<view class="btne">
				<!-- <view class="bnt1">取消订单</view> -->
				<!-- <view class="bnt2">去支付</view> -->
				<view class="bnt1" :style="order.indentState == 1?'width:100%;':''"
					v-if="order.indentState==0||order.indentState==1" @click="bindorderOff()">
					取消订单</view>
				<view class="bnt1" style="width: 100%;" v-if="order.indentState==2" @click="bindorderOff()">取消订单</view>
				<!-- <view class="btn2">抵达起点</view> -->
				<!-- <view class="btn1 margin-left" v-if="order.indentState!=3" @click="backindex()">再来一单</view> -->
				<view class="bnt2 margin-left " v-if="order.indentState==0" @click="orderpay()">立即支付</view>
				<view class="bnt2 margin-left " style="width: 100%;" v-if="order.indentState==7" @click="bufeiPay()">
					立即补费</view>

				<!-- <view class="btn1 margin-left" v-if="order.indentState == 4 &&!order.evaluateMessage"
					@click="bindcomment()">去评价</view> -->
				<!-- <view class="btn1 margin-left" v-if="order.indentState==4" @click="bindtousu()">去投诉</view> -->
			</view>
		</view>
		<u-popup v-model="showpay" mode="bottom">
			<view class="popup_pay">
				<view class=" margin-top padding-lr radius">
					<view style="padding: 0 20upx;margin-top: 36rpx;">
						<view
							style="display: flex;height: 100upx;align-items: center;padding: 20upx 0;justify-content: center;"
							v-for="(item,index) in openLists" :key='index'>
							<image :src="item.image" style="width: 55upx;height: 55upx;border-radius: 50upx;">
							</image>
							<view style="font-size: 30upx;margin-left: 20upx;width: 70%;">
								{{item.name}}
							</view>
							<radio-group name="openWay" style="margin-left: 45rpx;" @tap='selectWay(item)'>
								<label class="tui-radio">
									<radio color="#3699FF" :checked="openWay == item.id ? true : false" />
								</label>
							</radio-group>
						</view>
					</view>
				</view>
				<view class="pay_btns" @click="order.indentState==7?pays():pay()">确认支付</view>
			</view>
		</u-popup>
	</view>
</template>

<script>
	import orderVue from '../../pages/order/order.vue'
	export default {
		data() {
			return {
				showpay: false,
				openWay: 1,
				openLists: [],
				order: '',
				indentNumber: '',
				creditScore: 0,
				AllcreditScore: 0,
				closeOrderTime: '',
			}
		},
		onLoad(option) {
			if (option.indentNumber) {
				this.indentNumber = option.indentNumber
				this.getDetail()
			}

			// #ifdef MP-WEIXIN
			this.openLists = [{
				id: 1,
				image: '../../static/image/icon_weixin.png',
				name: '微信'
			}, {
				id: 3,
				image: '../../static/image/lingian.png',
				name: '余额'
			}]
			this.openWay = 1;
			// #endif
			// #ifdef H5
			let ua = navigator.userAgent.toLowerCase();
			if (ua.indexOf('micromessenger') !== -1) {
				//公众号是否自动登录  416
				this.$Request.get('/app/common/type/416').then(res => {
					if (res.data && res.data.value && res.data.value == '是') {
						this.openLists = [{
							id: 1,
							image: '../../static/image/icon_weixin.png',
							name: '微信'
						}, {
							id: 2,
							image: '../../static/image/zhifubao.png',
							name: '支付宝'
						}, {
							id: 3,
							image: '../../static/image/lingian.png',
							name: '余额'
						}];
						this.openWay = 1;
					} else {
						this.openLists = [{
							id: 2,
							image: '../../static/image/zhifubao.png',
							name: '支付宝'
						}, {
							id: 3,
							image: '../../static/image/lingian.png',
							name: '余额'
						}];
						this.openWay = 2;
					}
				})
			} else {
				this.openLists = [{
					id: 2,
					image: '../../static/image/zhifubao.png',
					name: '支付宝'
				}, {
					id: 3,
					image: '../../static/image/lingian.png',
					name: '余额'
				}];
				this.openWay = 2;
			}
			// #endif
			// #ifdef APP-PLUS
			this.openLists = [{
					id: 1,
					image: '../../static/image/icon_weixin.png',
					name: '微信'
				},
				{
					id: 2,
					image: '../../static/image/zhifubao.png',
					name: '支付宝'
				}, {
					id: 3,
					image: '../../static/image/lingian.png',
					name: '余额'
				}
			]
			this.openWay = 1;
			// #endif
			let that = this
			that.$Request.getT('/app/common/type/366').then(res => { // 每次取消扣除的信用分数量 364
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.creditScore = res.data.value
					}
				}
			})
			that.$Request.getT('/app/common/type/367').then(res => { // 低于多少不能接单和下单 367
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.AllcreditScore = res.data.value
					}
				}
			})
		},
		onShow() {
			this.closeOrderTime = uni.getStorageSync('closeOrderTime')
		},
		methods: {
			//预览图片
			chooseImg(index, urls) {
				uni.previewImage({
					current: index,
					urls: urls
				})
			},
			pays() {
				if (this.openWay == 0) {
					this.$queue.showToast('请选择支付方式!')
					return;
				}
				let data = {
					indentId: this.order.indentId,
				}
				this.showpay = false
				if (this.openWay == 1) { //微信支付
					// 微信app支付
					// #ifdef APP
					data.feeType = 1
					this.$Request.postT('/app/indent/feeOrders', data).then(res => {
						if (res.code == 0) {
							this.isCheckPay(res.code, 'wxpay', JSON.stringify(res.data));
						} else {
							uni.showToast({
								title: '支付失败！',
								icon: 'none'
							})
						}
					})
					// #endif
					//微信公众号支付
					// #ifdef H5
					let ua = navigator.userAgent.toLowerCase();
					if (ua.indexOf('micromessenger') !== -1) { //微信里打开
						data.feeType = 2
						this.$Request.postT('/app/indent/feeOrders', data).then(res => {
							if (res.code == 0) {
								this.callPay(res.data);
							} else {
								uni.showToast({
									title: '支付失败！',
									icon: 'none'
								})
							}
						})
					}
					// #endif
					//微信小程序
					// #ifdef MP-WEIXIN
					data.feeType = 3
					this.$Request.postT('/app/indent/feeOrders', data).then(res => {
						if (res.code == 0) {
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: res.data.timestamp,
								nonceStr: res.data.noncestr,
								package: res.data.package,
								signType: res.data.signType,
								paySign: res.data.sign,
								success: function(ret) {
									console.log(ret)
									uni.hideLoading();
									uni.showToast({
										title: '支付成功',
										icon: 'none'
									});
									setTimeout(function() {
										uni.navigateBack(1)
									}, 1000)
								},
								fail: function(err) {
									console.log('fail:' + JSON.stringify(err));
									uni.hideLoading();
									that.$queue.showToast('支付失败');
								}
							});
						} else {
							uni.showToast({
								title: '支付失败!',
								icon: 'none'
							})
						}
					})
					// #endif

				} else if (this.openWay == 2) { //支付宝支付
					// #ifdef H5
					data.feeType = 5
					this.$Request.postT('/app/indent/feeOrders', data).then(
						res => {
							if (res.code == 0) {
								uni.hideLoading()
								const div = document.createElement('div')
								div.innerHTML = res.data //此处form就是后台返回接收到的数据
								document.body.appendChild(div)
								document.forms[0].submit()
							} else {
								uni.showToast({
									icon: 'none',
									title: '支付失败!'
								});
							}
						});
					// #endif
					// #ifdef APP
					data.feeType = 4
					this.$Request.postT("/app/indent/feeOrders", data).then(res => {
						// console.log(JSON.stringify(res), '支付宝支付信息')
						this.isCheckPay(res.code, 'alipay', res.data)
					});
					// #endif
				} else { //余额支付
					data.feeType = 0
					let that = this
					uni.showModal({
						title: '温馨提示',
						content: '确定使用余额支付？',
						showCancel: true,
						cancelText: '取消',
						confirmText: '确认',
						success: res => {
							if (res.confirm) {
								uni.showLoading({
									title: '支付中...'
								})
								that.$Request.postT('/app/indent/feeOrders', data)
									.then(res => {
										if (res.code == 0) {
											uni.showToast({
												title: '订单支付成功'
											});
											uni.hideLoading();
											setTimeout(function() {
												uni.navigateBack()
											}, 1000)

										} else {
											uni.hideLoading();
											uni.showModal({
												showCancel: false,
												title: '提示',
												content: res.msg
											});
										}
									});
							}
						}
					});
				}
			},
			//打开支付方式弹框
			bufeiPay() {
				this.showpay = true
			},
			//联系客服
			changekefu() {
				let kefu = this.$queue.getData('kefu'); // 用户端联系方式 1 手机号 2企业微信
				let kefuPhone = this.$queue.getData('kefuPhone');
				if (kefu == 1) {
					uni.makePhoneCall({
						phoneNumber: kefuPhone //仅为示例
					});
				} else if (kefu == 2) {
					// #ifdef MP-WEIXIN
					let that = this
					try {
						wx.openCustomerServiceChat({
							extInfo: {
								url: that.$queue.getData('kefuUrl')
							},
							corpId: that.$queue.getData('kefuAppId'),
							success(res) {},
							fail(res) {
								console.error(res)
							}
						})
					} catch (error) {
						console.error("catchcatch" + error)
						uni.showToast({
							title: '请更新至微信最新版本'
						});
					}
					// #endif
					// #ifndef MP-WEIXIN
					let url = this.$queue.getData('kefuUrl');
					if (url.indexOf('/pages/') !== -1 || url.indexOf('/pageA/') !== -1 || url.indexOf('/my/') !== -1) {
						uni.navigateTo({
							url
						});
					} else {
						//#ifndef H5
						uni.navigateTo({
							url: '/pages/index/webView?url=' + url
						});
						//#endif
						//#ifdef H5
						window.location.href = url;
						//#endif
					}
					// #endif
				} else if (kefu == 3) {
					uni.navigateTo({
						url: '/my/kefu/kefu'
					});
				}
			},
			//呼叫司机
			callPhone(phone) {
				uni.makePhoneCall({
					phoneNumber: phone
				})
			},
			copyClick(copy) {
				uni.setClipboardData({
					data: copy,
					success: function(res) {
						uni.getClipboardData({
							success: function(res) {
								uni.showToast({
									title: "复制成功",
									icon: 'none',
								});
							},
						});
					},
				});
			},
			// 一键导航
			openMap(latitude, longitude, name) {
				uni.openLocation({
					latitude: latitude - 0, //要去的纬度-地址       
					longitude: longitude - 0, //要去的经度-地址
					name: name, //地址名称
					address: name, //详细地址名称
					success: function() {
						console.log('导航成功');
					},
					fail: function(error) {
						console.log(error)
					}
				});
			},
			getDetail() {
				let data = {
					indentNumber: this.indentNumber
				}
				this.$Request.getT('/app/indent/userIndentMessage', data).then(res => {
					if (res.code === 0) {
						this.order = res.data
						this.order.moneyJson = JSON.parse(this.order.moneyJson)
					}
					uni.hideLoading()

				});
			},
			// 取消订单
			bindorderOff() {
				let content = ''
				if (this.order.indentState == 2) {
					content = `师傅接单后${this.closeOrderTime}分钟内可取消，取消订单将会扣除信用分` + this.creditScore + '分,信用分低于' + this
						.AllcreditScore +
						'分无法下单，是否取消？'
				} else {
					content = '确定取消订单？'
				}
				let that = this
				uni.showModal({
					title: '温馨提示',
					content: content,
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							that.$Request.postT('/app/indent/userCancleIndent?indentNumber=' + this
									.indentNumber)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单取消成功'
										});
										that.getDetail()
									} else {
										uni.hideLoading();
										uni.showModal({
											showCancel: false,
											title: '订单失败',
											content: res.msg
										});
									}
								});
						}
					}
				});
			},
			selectWay: function(item) {
				this.openWay = item.id;
			},
			orderpay() {
				this.showpay = true
			},
			pay() {
				if (this.openWay == 0) {
					this.$queue.showToast('请选择支付方式!')
					return;
				}
				if (this.openWay == 1) {
					console.log('微信')
					// #ifdef APP-PLUS
					// 微信APP支付 根据订单id获取支付信息
					let data = {
						indentId: this.order.indentId
					}
					this.$Request.postT("/app/wxPay/payAppOrder", data).then(res => {
						// console.log(JSON.stringify(res), '微信支付信息')
						this.isCheckPay(res.code, 'wxpay', JSON.stringify(res.data));
					});
					// #endif
					// #ifdef MP-WEIXIN
					let that = this
					let data = {
						indentId: that.order.indentId
					}
					that.$Request.postT("/app/wxPay/wxPayJsApiOrder", data).then(res => {
						if (res.code == 0) {
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: res.data.timestamp,
								nonceStr: res.data.noncestr,
								package: res.data.package,
								signType: res.data.signType,
								paySign: res.data.sign,
								success: function(ret) {
									console.log(ret)
									uni.hideLoading();
									uni.showToast({
										title: '支付成功',
										icon: 'none'
									});
									setTimeout(function() {
										uni.navigateBack(1)
									}, 1000)
								},
								fail: function(err) {
									console.log('fail:' + JSON.stringify(err));
									uni.hideLoading();
									that.$queue.showToast('支付失败');
								}
							});
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					});
					// #endif

					// #ifdef H5
					// 微信h5支付 根据订单id获取支付信息
					let ua = navigator.userAgent.toLowerCase();
					if (ua.indexOf('micromessenger') !== -1) { //微信里打开
						let data = {
							indentId: this.order.indentId
						}
						this.$Request.postT('/app/wxPay/wxPayMpOrder', data).then(res => {
							if (res.code == 0) {
								this.callPay(res.data);
							} else {
								uni.showToast({
									icon: 'none',
									title: '支付失败!'
								});
							}
						});
					}
					// #endif
				} else if (this.openWay == 2) {
					// APP支付宝支付
					// #ifdef H5
					// this.form.payType = 5
					let data = {
						indentId: this.order.indentId
					}
					this.$Request.postT('/app/aliPay/payH5Order', data).then(
						res => {
							if (res.code == 0) {
								const div = document.createElement('div')
								div.innerHTML = res.data //此处form就是后台返回接收到的数据
								document.body.appendChild(div)
								document.forms[0].submit()
							} else {
								uni.showToast({
									icon: 'none',
									title: '支付失败!'
								});
							}
						});
					// #endif

					// #ifdef APP
					// this.form.payType = 4
					let data = {
						indentId: this.indentId
					}
					this.$Request.postT("/app/aliPay/payAppOrder", data).then(res => {
						// console.log(JSON.stringify(res), '支付宝支付信息')
						this.isCheckPay(res.code, 'alipay', res.data)
					});
					// #endif
				} else if (this.openWay == 3) {
					this.showpay = false
					uni.showModal({
						title: '温馨提示',
						content: '确定使用余额支付？',
						showCancel: true,
						cancelText: '取消',
						confirmText: '确认',
						success: res => {
							if (res.confirm) {
								uni.showLoading({
									title: '支付中...'
								})
								this.$Request.postT('/app/userMoney/moneyPayIndent?indentId=' + this.order
										.indentId)
									.then(res => {
										if (res.code == 0) {
											uni.showToast({
												title: '订单支付成功'
											});
											setTimeout(function() {
												uni.navigateBack()
											}, 1000)

										} else {
											uni.hideLoading();
											uni.showModal({
												showCancel: false,
												title: '提示',
												content: res.msg
											});
										}
									});
							}
						}
					});

				}
			},
			callPay: function(response) {
				uni.hideLoading();
				if (typeof WeixinJSBridge === "undefined") {
					if (document.addEventListener) {
						document.addEventListener('WeixinJSBridgeReady', this.onBridgeReady(response), false);
					} else if (document.attachEvent) {
						document.attachEvent('WeixinJSBridgeReady', this.onBridgeReady(response));
						document.attachEvent('onWeixinJSBridgeReady', this.onBridgeReady(response));
					}
				} else {
					this.onBridgeReady(response);
				}
			},
			onBridgeReady: function(response) {
				let that = this;
				// if (!response.package) {
				// 	return;
				// }
				console.log(response, 111111)
				WeixinJSBridge.invoke(
					'getBrandWCPayRequest', {
						"appId": response.appid, //公众号名称，由商户传入
						"timeStamp": response.timestamp, //时间戳，自1970年以来的秒数
						"nonceStr": response.noncestr, //随机串
						"package": response.package,
						"signType": response.signType, //微信签名方式：
						"paySign": response.sign //微信签名
					},
					function(res) {
						console.log(res, '/*-/*-/*-')
						if (res.err_msg === "get_brand_wcpay_request:ok") {
							// 使用以上方式判断前端返回,微信团队郑重提示：
							//res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。
							uni.showLoading({
								title: '支付成功'
							});
							setTimeout(function() {
								uni.hideLoading();
								uni.navigateBack()
							}, 1000);
						} else {
							uni.hideLoading();
						}
						WeixinJSBridge.log(response.err_msg);
					}
				);
			},
			isCheckPay(status, name, order) {
				if (status == 0) {
					this.setPayment(name, order);
				} else {
					uni.hideLoading();
					uni.showToast({
						title: '支付信息有误',
						icon: 'none'
					});
				}
			},
			setPayment(name, order) {
				console.log(name, '*-*-*', order)
				uni.requestPayment({
					provider: name,
					orderInfo: order, //微信、支付宝订单数据
					success: function(res) {
						console.log(res)
						uni.hideLoading();
						uni.showLoading({
							title: '支付成功'
						});
						setTimeout(function() {
							uni.navigateBack()
						}, 1000);
					},
					fail: function(err) {
						console.log(err)
						uni.hideLoading();
					},
					complete() {
						uni.hideLoading();
					}
				});
			},
		}
	}
</script>

<style lang="less">
	page {
		background: #F5F5F5;
	}

	.headtop {
		width: 100%;
		height: 325rpx;
		background: linear-gradient(to bottom, #06C484 5%, #F5F5F5 90%);
		position: fixed;
		top: 0;
		z-index: 1;
	}

	.content {
		position: relative;
		z-index: 99;
	}

	.stte {
		font-size: 38rpx;
		font-family: PingFang SC;
		font-weight: bold;
		color: #FFFFFF;
		padding: 40rpx 0 10rpx 40rpx;
	}

	.boxa {
		background: #FFFFFF;
		border-radius: 24rpx;
		margin: 30rpx;
		padding: 30rpx 0;
	}

	.box {
		background: #FFFFFF;
		border-radius: 24rpx;
		margin: 30rpx;

	}

	.titls {
		font-size: 32rpx;
		font-family: PingFang SC;
		font-weight: bold;
		color: #333333;
	}

	.addbox {
		display: flex;
		align-items: center;
		justify-content: space-between;
		// height: 110rpx;
		border-radius: 16rpx;
		padding: 20rpx 0rpx;
	}

	.add_cont {
		display: flex;
		align-items: center;
	}

	.add_tit {
		font-size: 30rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #1A1A1A;
	}


	.green {
		width: 16rpx;
		height: 16rpx;
		background: #1FC657;
		border-radius: 50%;
		margin-right: 20rpx;
	}

	.orgin {
		width: 16rpx;
		height: 16rpx;
		background: #FBAC04;
		border-radius: 50%;
		margin-right: 20rpx;
	}

	.btne {
		display: flex;
		align-items: center;
		justify-content: end;
		padding: 0 30rpx;

		.bnt1 {
			width: 333rpx;
			height: 100rpx;
			border: 1px solid #02B595;
			border-radius: 14rpx;
			font-size: 32rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #02B595;
			display: flex;
			align-items: center;
			justify-content: center;
			background: #FFFFFF;
		}

		.bnt2 {
			width: 333rpx;
			height: 100rpx;
			background: #02B595;
			border-radius: 14rpx;
			font-size: 32rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			display: flex;
			align-items: center;
			justify-content: center;
		}
	}

	/* 支付弹框 */
	.popup_pay {
		width: 100%;
	}

	.pay_btns {
		width: 90%;
		margin: 0 auto 40rpx;
		text-align: center;
		background: #02B595;
		height: 80rpx;
		border-radius: 16rpx;
		color: #ffffff;
		line-height: 80rpx;
		margin-top: 20rpx;
	}
</style>