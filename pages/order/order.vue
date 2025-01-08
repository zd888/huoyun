<template>
	<view class="order" style="padding-bottom: 40rpx;">
		<!-- #ifdef H5 -->
		<view style="position: fixed;top:0rpx;width: 100%;z-index: 999;">
			<!-- #endif -->
			<!-- #ifndef H5 -->
			<view style="position: fixed;top: 0;width: 100%;z-index: 999;">
				<!-- #endif -->
				<u-tabs :list="list" :is-scroll="true" active-color="#03A859" inactive-color=" #666666" :bold="bold"
					:current="current" @change="change" :show-bar="true"></u-tabs>
			</view>

			<view class="ifbox">

				<view class="order_box" v-for="(item,index) in order" :key="index"
					@click="bindorderDetail(item.indentNumber)">
					<view class="order_success">
						<view v-if="item.indentType==1"> 立即用车</view>
						<view v-if="item.indentType==2">预约用车</view>
						<view class="order_name" v-if="item.indentState==1">待接单</view>
						<view class="order_name" v-if="item.indentState==2">已接单</view>
						<view class="order_name" v-if="item.indentState==3">装货中</view>
						<view class="order_name" v-if="item.indentState==0">待支付</view>
						<view class="order_name" v-if="item.indentState==4">运输中</view>
						<view class="order_name" v-if="item.indentState==5">卸货中</view>
						<view class="order_name" v-if="item.indentState==6">已完成</view>
						<view class="order_name" v-if="item.indentState==7">完成待补费</view>
						<view class="order_name" v-if="item.indentState==8">已取消</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="order_city">
						<view v-if="item.indentType == 1" class="flex align-center margin-bottom"
							style="margin-left: -5rpx;">
							<image src="../../static/image/my/data.png" style="width: 28rpx;height: 28rpx;"></image>
							<view class="margin-left-xs">下单时间：{{item.createTime}}</view>
						</view>
						<view v-if="item.indentType == 2 && item.hopeTime" class="flex align-center margin-bottom"
							style="margin-left: -5rpx;">
							<image src="../../static/image/my/data.png" style="width: 28rpx;height: 28rpx;"></image>
							<view class="margin-left-xs">预约时间：{{formatDateTime(item.hopeTime)}}出发</view>
						</view>
						<view class=" flex align-center ">
							<view class="lv"></view>
							<!-- <view class="margin-left-sm">出发地点：{{item.startAddress}}</view> -->
							<view class="margin-left-sm flex align-center" style="width: 90%;">
								<view style="width: 150rpx;">出发地点：</view>
								<view class="text-cut">{{item.startAddress}}</view>
							</view>
						</view>
						<view class="" style="margin-left:6rpx;">
							<image src="../../static/image/order_jt.png" style="width: 6rpx;height: 24rpx;"></image>
						</view>
						<view class="flex align-center ">
							<view class="huang"></view>
							<view class="margin-left-sm flex align-center" style="width: 90%;">
								<view style="width: 150rpx;">到达地点：</view>
								<view class="text-cut">{{item.endAddress}}</view>
							</view>
						</view>
						<view class="margin-top margin-left" v-if="item.customPhones">联系电话：{{item.customPhones}}</view>
						<view class="margin-top" v-if="item.type==3">预约时间：021-06-11 11:39</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="flex align-center justify-between padding">
						<view class="price">¥
							<text class="text-bold" style="font-size:38rpx;">{{item.indentMoney}}</text>
						</view>
						<view class="order_btn">
							<view class="btn2" v-if="item.indentState==0||item.indentState==1 || item.indentState==2"
								@tap.stop="bindorderOff(item)">取消订单</view>

							<view class="btn2 margin-left" v-if="item.indentState==6 || item.indentState==8"
								@click.stop="backindex()">再来一单
							</view>
							<view class="btn2 margin-left" v-if="item.indentState==0"
								@click.stop="bindorderDetail(item.indentNumber)">立即支付</view>
							<view class="btn2 margin-left" v-if="item.indentState==7"
								@click.stop="bindorderDetail(item.indentNumber)">立即补费</view>
							<view class="btn2 margin-left" v-if="item.indentState == 6 &&!item.evaluateMessage"
								@tap.stop="bindcomment(item)">
								去评价
							</view>
							<view class="btn2 margin-left" v-if="item.indentState == 6 " @tap.stop="bindtousu(item)">
								去投诉
							</view>
						</view>
					</view>
				</view>
				<view class="empty" v-if="order.length == 0">
					<view
						style="width: 90%; margin: 0 auto; position: fixed;top: 30%;left: 0rpx;right: 0rpx;text-align: center;">
						<image src="../../static/image/emety.png" style="width: 300rpx;height: 300rpx;"></image>
						<view style="color: #CCCCCC;">暂无内容</view>
					</view>
				</view>
			</view>
		</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
					name: '全部',
					id: ''
				}, {
					name: '待支付',
					id: 0
				}, {
					name: '待接单',
					id: 1
				}, {
					name: '已接单',
					id: 2
				}, {
					name: '装货中',
					id: 3
				}, {
					name: '运输中',
					id: 4
				}, {
					name: '卸货中',
					id: 5
				}, {
					name: '已完成',
					id: 6
				}, {
					name: '完成待补费',
					id: 7
				}, {
					name: '已取消',
					id: 8
				}],
				current: 0,
				bold: false,
				page: 1,
				limit: 10,
				order: [],
				pages: 0,
				arr: [],
				showModal: true,
				closeOrderTime: '',
				creditScore: 0,
				AllcreditScore: 0,
			}
		},
		onLoad() {
			let that = this;
			that.$Request.getT('/app/common/type/268').then(res => { //接单成功通知
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.arr.push(res.data.value)
					}
				}
			})
			that.$Request.getT('/app/common/type/269').then(res => { //订单完成通知
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.arr.push(res.data.value)
					}
				}
			})

			that.$Request.getT('/app/common/type/366').then(res => { // 每次取消扣除的信用分数量 366
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
			// this.order = []
			this.closeOrderTime = uni.getStorageSync('closeOrderTime')
			console.log(this.closeOrderTime, '111111')
			let token = this.$queue.getData('token')
			this.userId = this.$queue.getData('userId')
			if (token) {
				let index = uni.getStorageSync('current')
				if (index) {
					this.current = index
					this.page = 1;
					this.orderList()
					uni.removeStorageSync('current')
				} else {
					this.current = 0
					this.page = 1;
					this.orderList()
				}

				let that = this
				that.time = setInterval(function() {
					that.orderList()
				}, 30000)
			} else {
				uni.stopPullDownRefresh();
				uni.showModal({
					title: '提示',
					content: '请先去登录',
					success: function(res) {
						if (res.confirm) {
							uni.navigateTo({
								url: '/pages/my/register'
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			}
		},
		onUnload() {
			uni.$off('send')
			uni.$off('current')
		},
		methods: {
			bindtousu(e) { //投诉
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				uni.navigateTo({
					url: '/my/order/complaint?indentNumber=' + e.indentNumber + '&riderUserId=' + e
						.riderUserId
				})
			},
			//格式化时间为年月日时分，不要秒
			formatDateTime(datetime) {
				let time = ''
				if (datetime) {
					time = datetime.substring(0, 16);
				} else {
					time = ''
				}

				return time
			},
			backindex() {
				uni.navigateTo({
					url: '/pages/index/index'
				})
			},

			change(index) {
				this.current = index;
				this.page = 1;
				this.orderList()
			},
			// 订单获取
			orderList() {
				let data = {
					indentState: this.list[this.current].id,
					page: this.page,
					limit: this.limit
				}
				this.$Request.getT('/app/indent/findUserIndent', data).then(res => {
					uni.stopPullDownRefresh()
					if (res.code === 0) {
						this.pages = res.data.pages
						if (this.page == 1) {
							this.order = res.data.records
						} else {
							this.order = [...this.order, ...res.data.records]
						}

					}
					uni.hideLoading()

				});
			},
			// 订单详情
			bindorderDetail(indentNumber) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				console.log(indentNumber)
				// uni.navigateTo({
				// 	url: '/pages/order/orderDetail/detail?indentNumber=' + indentNumber
				// })
				uni.navigateTo({
					url: '/my/order/pay?indentNumber=' + indentNumber
				})
			},
			// 再来一单
			bindorder(e) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				console.log(e)
				let index = e.indentType
				let current = e.buyType
				if (e.indentType == 1 || e.indentType == 2) {
					uni.navigateTo({
						url: '/pages/Helpsend/Helpsend?indentNumber=' + e.indentNumber + '&index=' + index
					})
				} else if (e.indentType == 3) {
					uni.navigateTo({
						url: '/pages/Helppay/Helppay?indentNumber=' + e.indentNumber + '&index=' + index +
							'&current=' + current
					})
				} else if (e.indentType == 4) {
					uni.navigateTo({
						url: '/pages/Cityservice/Cityservice?indentNumber=' + e.indentNumber + '&index=' + index
					})
				}
			},
			// 去评价
			bindcomment(e) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				console.log(e)
				uni.navigateTo({
					url: '/my/order/comments?indentNumber=' + e.indentNumber + '&riderUserId=' + e
						.riderUserId
				})
			},
			// 取消订单
			bindorderOff(e) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				let indentNumber = e.indentNumber
				let content = ''
				if (e.indentState == 2) {
					content = `师傅接单后${this.closeOrderTime}分钟内可取消，取消订单将会扣除信用分` + this.creditScore + '分,信用分低于' + this
						.AllcreditScore +
						'分无法下单，是否取消？'
					// closeOrderTime

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
							that.$Request.postT('/app/indent/userCancleIndent?indentNumber=' + indentNumber)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单取消成功'
										});
										that.page = 1;
										that.orderList()
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
			//确认订单
			bindconfirm(e) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				// console.log(e)
				let indentNumber = e.indentNumber
				console.log(indentNumber)
				this.$Request.postT('/app/indent/userDelivery?indentNumber=' + indentNumber).then(res => {
					console.log(res)
					if (res.code == 0) {
						uni.showToast({
							title: '订单确认成功'
						});
						this.page = 1;
						this.orderList()
					} else {
						uni.hideLoading();
						uni.showModal({
							showCancel: false,
							title: '订单确认失败',
							content: res.msg
						});
					}
				});
			},
			// 立即付款
			bindorderpay(e) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendorderMsg')) {
					uni.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(re,'**********')
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				// #endif
				uni.navigateTo({
					url: '/pages/order/pay/pay?indentNumber=' + e.indentNumber
				})
			},
			// 开启订阅消息
			openMsg() {
				var that = this
				wx.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendorderMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							uni.setStorageSync('sendorderMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										uni.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										that.showModal = false
									} else if (res.cancel) {
										that.showModal = true
									}
								}
							})
						}
					}
				})
			}
		},
		onReachBottom: function() {
			if (this.page < this.pages) {
				this.page = this.page + 1;
				this.orderList();
			}

		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.orderList();
		},
	}
</script>

<style lang="less">
	body {
		background: #F5F5F5;
	}

	/* #ifndef MP-WEIXIN */
	page {
		background: #F2EDED;
	}

	/* #endif */

	.empty {
		width: 100%;
		background: #ffffff;
		height: 100vh;
	}

	.ifbox {
		/* #ifdef H5 */
		padding-top: 85rpx;
		/* #endif */
		/* #ifndef H5 */
		padding-top: 86rpx;
		/* #endif */
	}

	.lv {
		width: 16rpx;
		height: 16rpx;
		background: #03A859;
		border-radius: 50%;
	}

	.huang {
		width: 16rpx;
		height: 16rpx;
		background: #FBAC04;
		border-radius: 50%;
	}

	.order_box {
		background: #FFFFFF;
		border-radius: 24rpx;
		margin: 30rpx;

		.order_success {
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 30rpx;

			.order_name {
				color: #03A859;
			}
		}

		.order_city {
			padding: 30rpx;
			color: #666666;
		}

		.price {
			text {
				font-size: 32rpx;
				font-weight: 800;
			}

			font-size: 26rpx;
			font-family: PingFang SC;
			color: #FF4B36;
		}

		.order_btn {

			display: flex;
			align-items: center;
			justify-content: flex-end;

			.btn2 {
				width: 150rpx;
				height: 60rpx;
				background: #03A859;
				border-radius: 16rpx;
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #FFFFFF;
				display: flex;
				align-items: center;
				justify-content: center;
			}

			.btn1 {
				width: 150rpx;
				height: 60rpx;
				border: 1px solid #999999;
				border-radius: 16rpx;
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #999999;
				display: flex;
				align-items: center;
				justify-content: center;
				margin-right: 20rpx;
			}
		}
	}
</style>