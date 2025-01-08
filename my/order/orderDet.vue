<template>
	<view>
		<view class="box padding-lr">
			<view class="addbox ">
				<view class="add_cont">
					<view class="green"></view>
					<view class="add_tit" v-if="addlist.length==0">从哪发货？</view>
					<view class="add_tit" v-else>{{addlist.address}}</view>
				</view>
				<view>
					<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;">
					</image>
				</view>
			</view>
			<view style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
			<view class="addbox ">
				<view class="add_cont">
					<view class="orgin"></view>
					<view class="add_tit" v-if="addlists.length==0">货送去哪？</view>
					<view class="add_tit" v-else>{{addlists.address}}</view>
				</view>
				<view>
					<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;">
					</image>
				</view>
			</view>
		</view>


		<view class="box padding">
			<view class="flexs ">
				<view class="text-bold">跟车人数</view>
				<view class="flex align-center ">
					<u-input v-model="peopleNum" type="number" placeholder="请输入跟车人数" inputAlign="right" />
				</view>
			</view>
			<!-- <view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
			<view class="flexs ">
				<view class="text-bold">联系电话</view>
				<view class="">
					<u-input v-model="customPhone" type="number" placeholder="请输入联系电话" inputAlign="right" />
				</view>
			</view> -->
			<block v-if="indentType==2">
				<view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
				<view class="flexs " @click="timeShow = true">
					<view class="text-bold">预约时间</view>
					<view class="">
						<u-input @click="timeShow = true" v-model="hopeTime" :disabled="true" placeholder="请选择预约时间"
							inputAlign="right" />
					</view>
				</view>
			</block>
			<view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
			<view class="flexs " @click="getyouhui">
				<view class="text-bold">优惠劵</view>
				<view class="flex align-center ">
					<u-input @click="getyouhui" v-model="couponName" :disabled="true" placeholder="请选择优惠劵"
						inputAlign="right" />
					<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;margin-left: 15rpx;">
				</view>
			</view>
			<view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
			<!-- <view class="flexs ">
				<view class="text-bold">预约时间</view>
				<view class="flex align-center ">
					<u-input v-model="phone" type="number" :disabled="true" placeholder="请选择预约时间" inputAlign="right" />
					<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;margin-left: 15rpx;">
				</view>
			</view>
			<view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view> -->
			<!-- 	<view class="flexs ">
				<view class="text-bold">额外需求</view>
				<view class="flex align-center ">
					<u-input v-model="phone" type="number" :disabled="true" placeholder="请选择搬运服务" inputAlign="right" />
					<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;margin-left: 15rpx;">
				</view>
			</view>
			<view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view> -->
			<!-- <view class="flexs padding-tb-xs">
				<view class="text-bold">付款时间</view>
				<view class="flex align-center">
					收到付款
					<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;margin-left: 15rpx;">
				</view>
			</view>
			<view class="margin-tb-sm" style="width: 100%;height: 1rpx;background: #E6E6E6;"></view> -->
			<view class=" ">
				<view class="text-bold">订单备注</view>
				<view class="">
					<u-input v-model="remarks" type="textarea" placeholder="请输入留言备注" inputAlign="left" height="210" />
				</view>
			</view>
		</view>
		<view class="margin-lr">
			<u-checkbox-group>
				<u-checkbox v-model="checked" shape="circle" active-color="#40BEA4">
					<view class="wenzi">我已阅读并同意<text @click.stop="goNav(314)">《货物托运居间服务协议》</text>及<text
							@click.stop="goNav(352)">《货运软件
							信息服务协议》</text>及<text@click.stop="goNav(361)">《意外险赠险须知授权》</text></view>
				</u-checkbox>
			</u-checkbox-group>
		</view>

		<view class="tabber">
			<view class="price">
				费用标准：
				<view class="num"><text>{{zongPrice}}</text>元</view>
				<view class="margin-left text-sm" style="color: #40BEA4;" @click="show = true">费用明细</view>
			</view>
			<view class="btn" @click="orderPay()">立即叫车</view>
		</view>
		<u-popup v-model="youhuiShow" mode="bottom" border-radius="14" height="750rpx" :closeable="true">
			<view style="padding: 30rpx;background: #F5F8FC;width: 100%;height: 100%;">
				<view class="text-lg text-bold text-center">优惠劵</view>
				<view class="flex align-center justify-end">
					<view class="deteJuan" @click="deteYouhuj">不使用优惠劵</view>
				</view>
				<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y">
					<view class="money_box" v-for="(item,index) in youhuiList" :key="index">
						<view class="box_tit">
							<view class="money_name">{{item.couponName}}</view>
							<view class="money_price"><text>￥</text>{{item.money}}</view>
						</view>
						<view class="money_data" v-if="item.expirationTime">有效期至{{item.expirationTime}}
						</view>
						<view class="money_data" v-else>永久有效</view>
						<view class="money_line">
							<u-line direction="row" color="#E6E6E6" border-style="dashed" />
						</view>
						<view class="box_bottom">
							<view class="money_use">满{{item.minMoney}}元可使用</view>
							<view class="money_btn">
								<view class="lj_use" @click="useryouhui(item)">
									立即使用
								</view>
							</view>
						</view>
					</view>
				</scroll-view>

			</view>
		</u-popup>
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
									<radio color="#02B595" :checked="openWay == item.id ? true : false" />
								</label>
							</radio-group>
						</view>
					</view>
				</view>
				<view class="pay_btns" @click="pay()">确认支付</view>
			</view>
		</u-popup>




		<u-popup v-model="show" mode="center" border-radius="14" :closeable="true" width="600rpx">
			<view class="padding">
				<view class="text-lg text-bold">费用明细</view>

				<view class="margin-top">
					<view class="padding-bottom">总公里数 ：{{allMoney.distance}}km</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;"></view>

					<view class="padding-tb" v-if="allMoney.startPrice">起步价 ：￥{{allMoney.startPrice}}</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.startPrice"></view>

					<view class="padding-tb" v-if="allMoney.startKilometer">起步公里数 ：{{allMoney.startKilometer}}km</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.startKilometer"></view>

					<view class="padding-tb" v-if="allMoney.metre">超出公里数 ：￥{{allMoney.metre}}</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.metre"></view>

					<view class="padding-tb" v-if="allMoney.outKilometerMoney">超出公里数部分费用
						：￥{{allMoney.outKilometerMoney}}</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.outKilometerMoney">
					</view>

					<view class="padding-tb" v-if="allMoney.realFreeWaitDuration">等待时长
						：{{allMoney.realFreeWaitDuration}}/h</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.realFreeWaitDuration">
					</view>

					<view class="padding-tb" v-if="allMoney.realFreeWaitMoney">最终等待时长收费金额
						：￥{{allMoney.realFreeWaitMoney}}</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.realFreeWaitMoney">
					</view>

					<view class="padding-tb" v-if="allMoney.couponMoney">优惠券减免金额
						：￥{{allMoney.couponMoney}}</view>
					<view style="width: 100%;height: 1rpx;background: #e6e9ec;" v-if="allMoney.couponMoney">
					</view>

					<view class="padding-tb">总费用 ：￥{{allMoney.allMoney}}</view>
				</view>
			</view>
		</u-popup>
		<!-- 预约时间 -->
		<u-picker v-model="timeShow" :params="params" mode="time" @confirm="confirm"></u-picker>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				peopleNum: '', //跟车人数
				customPhone: '',
				remarks: '',
				value: '',
				checked: false,
				youhuiList: [], //优惠劵
				couponName: '',
				couponId: '',
				scrollTop: 0,
				old: {
					scrollTop: 0
				},
				addlist: [], //出发地址
				addressId: '',
				addlists: [], //目的地
				addressIds: "",
				formData: {},
				isAdd: true,
				allMoney: '',
				showpay: false,
				openWay: 1,
				openLists: [],
				youhuiShow: false,
				zongPrice: 0, //总价
				hopeTime: '', //预约时间
				indentType: 1,
				timeShow: false,
				params: {
					year: false,
					month: true,
					day: true,
					hour: true,
					minute: true,
					second: false
				},
				show: false
			}
		},
		onLoad(option) {
			console.log(JSON.parse(option.data), '1111111111111111')
			if (option.data) {
				this.formData = JSON.parse(option.data)
				let formData = JSON.parse(option.data)
				if (formData.indentType == 2) {
					this.indentType = formData.indentType
					if (formData.hopeTime) {
						this.hopeTime = formData.hopeTime
					}

				}
				if (formData.fhadd && formData.shadd) {
					this.addressId = formData.fhadd
					this.addressIds = formData.shadd
					this.getAddress()
				}
				// if (formData.fhadd) {
				// 	this.addressId = formData.fhadd
				// 	this.getcfd()
				// }
				// if (formData.shadd) {
				// 	this.addressIds = formData.shadd
				// 	this.getzd()
				// }
				console.log(this.addressId, '111111111')
				console.log(this.addressIds, '22222222')
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
			// #endif
			// #ifdef H5
			let ua = navigator.userAgent.toLowerCase();
			if (ua.indexOf('micromessenger') !== -1) {
				//公众号是否自动登录  416
				this.$Request.get('/app/common/type/416').then(res => {
					if (res.data && res.data.value && res.data.value == '是') {
						this.openLists = [{
							id: 2,
							image: '../../static/image/zhifubao.png',
							name: '支付宝'
						}, {
							id: 1,
							image: '../../static/image/icon_weixin.png',
							name: '微信'
						}, {
							id: 3,
							image: '../../static/image/lingian.png',
							name: '余额'
						}];
						this.openWay = 3;
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
						this.openWay = 3;
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
				this.openWay = 3;
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
			this.openWay = 3;
			// #endif
		},
		methods: {
			isFutureDate(inputDate) {
				var date = new Date(inputDate);
				var currentDate = new Date();
				if (date.getTime() > currentDate.getTime()) {
					return true;
				} else {
					return false;
				}
			},
			//选择预约时间
			confirm(e) {
				let year = new Date().getFullYear()
				let time = year + '-' + e.month + '-' + e.day + ' ' + e.hour + ':' + e.minute + ':00'
				let isTime = this.isFutureDate(time)
				if (isTime) {
					this.hopeTime = year + '-' + e.month + '-' + e.day + ' ' + e.hour + ':' + e.minute
				} else {
					uni.showToast({
						title: '请选择当前之后的时间',
						icon: 'none'
					})
				}

			},
			//取消优惠券
			deteYouhuj() {
				this.couponName = ''
				this.couponId = ''
				this.payPricce()
			},
			goNav(e) {
				uni.navigateTo({
					url: '/my/xieyi/xieyi?type=' + e
				})
			},
			//使用优惠券
			useryouhui(item) {
				this.couponName = item.couponName
				this.couponId = item.id
				this.payPricce()
			},
			selectWay: function(item) {
				this.openWay = item.id;
			},
			async getAddress() {
				try {
					await Promise.all([this.getcfd(), this.getzd()]);
					this.payPricce();
				} catch (e) {
					uni.showToast({
						title: '地址获取异常，请返回重试',
						icon: 'none'
					})
					//TODO handle the exception
				}
			},
			//获取出发地
			getcfd() {
				return this.$Request.getT('/app/address/getAddressInfo?addressId=' + this.addressId).then(res => {
					if (res.code == 0 && res.data) {
						this.addlist = res.data
						if (this.addlists.length != 0 && this.isAdd) {
							this.isAdd = false
							// this.payPricce()
						}
					} else {
						this.addlist = []
					}
				}).catch(error => {
					throw error;
				});
			},
			//获取目的地
			getzd() {
				return this.$Request.getT('/app/address/getAddressInfo?addressId=' + this.addressIds).then(res => {
					if (res.code == 0 && res.data) {
						this.addlists = res.data
						if (this.addlists.length != 0 && this.isAdd) {
							this.isAdd = false
							// this.payPricce()
						}

					} else {
						this.addlists = []
					}
				}).catch(error => {
					throw error;
				});
			},
			payPricce() { //计算基础价格
				let data = {
					goLng: this.addlist.lng,
					goLat: this.addlist.lat,
					toLng: this.addlists.lng,
					toLat: this.addlists.lat,
					serviceId: this.formData.serviceId,
					couponId: this.couponId
				}
				this.$Request.getT('/app/indent/basicsMoney', data).then(res => {
					if (res.code === 0) {
						this.isAdd = true
						this.allMoney = res.data
						this.zongPrice = this.allMoney.allMoney

						this.youhuiShow = false
					} else {
						uni.showToast({
							title: res.msg,
							icon: "none"
						})
					}
				});
			},
			getyouhui() { //获取优惠劵
				let data = {
					indentType: 1,
					indentMoney: this.allMoney.allMoney
				}
				this.$Request.getT('/app/couponUser/getMyOrderCouponList', data).then(res => {
					if (res.code == 0 && res.data) {
						this.youhuiList = res.data.records
						if (this.youhuiList.length > 0) {
							this.youhuiShow = true
						} else {
							uni.showToast({
								title: '暂无可使用的优惠券',
								icon: 'none'
							})
						}
					} else {
						uni.showToast({
							title: '暂无可使用的优惠券',
							icon: 'none'
						})
					}
				});
			},
			orderPay() {
				if (this.addlist.length) {
					uni.showToast({
						title: '请选择出发地',
						icon: 'none'
					})
					return
				}
				if (this.addlists.length) {
					uni.showToast({
						title: '请选择目的地',
						icon: 'none'
					})
					return
				}
				if (!this.peopleNum) {
					uni.showToast({
						title: '请输入跟车人数',
						icon: 'none'
					})
					return
				}
				// if (!this.customPhone) {
				// 	uni.showToast({
				// 		title: '请输入联系电话',
				// 		icon: 'none'
				// 	})
				// 	return
				// }
				if (this.indentType == 2 && !this.hopeTime) {
					uni.showToast({
						title: '请选择预约时间',
						icon: 'none'
					})
					return
				}
				if (!this.checked) {
					uni.showToast({
						title: '请阅读并同意下单协议',
						icon: 'none'
					})
					return
				}
				let data = {
					serviceId: this.formData.serviceId,
					typeIds: this.formData.typeIds,
					modelIds: this.formData.modelIds,
					indentType: this.formData.indentType,
					customPhone: this.customPhone,
					peopleNum: this.peopleNum,
					
					startAddressId:this.addlist.addressId,
					startUserName:this.addlist.userName,
					startUserPhone:this.addlist.userPhone,
					startProvince: this.addlist.province,
					startCity: this.addlist.city,
					startCounty: this.addlist.county,
					startAddress: this.addlist.address,
					startLng: this.addlist.lng,
					startLat: this.addlist.lat,
					
					endAddressId:this.addlists.addressId,
					endUserName:this.addlists.userName,
					endUserPhone:this.addlists.userPhone,
					endProvince: this.addlists.province,
					endCity: this.addlists.city,
					endCounty: this.addlists.county,
					endAddress: this.addlists.address,
					endLng: this.addlists.lng,
					endLat: this.addlists.lat,
					remarks: this.remarks,
					couponId: this.couponId,
				}
				if (this.indentType == 2) {
					data.hopeTime = this.hopeTime + ':00'
				}
				this.$Request.postJson('/app/indent/addIndent', data).then(res => {
					if (res.code === 0 && res.data) {
						this.indentId = res.data.indentId
						this.showpay = true
						// uni.navigateTo({
						// 	url: '/my/order/pay?indentNumber='+res.data.indentNumber
						// })

					} else {
						uni.showToast({
							title: res.msg,
							icon: "none"
						})
					}
				});
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
						indentId: this.indentId
					}
					this.$Request.postT("/app/wxPay/payAppOrder", data).then(res => {
						// console.log(JSON.stringify(res), '微信支付信息')
						this.isCheckPay(res.code, 'wxpay', JSON.stringify(res.data));
					});
					// #endif
					// #ifdef MP-WEIXIN
					let that = this
					let data = {
						indentId: that.indentId
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
									uni.setStorageSync('current', 0)
									setTimeout(function() {
										uni.hideLoading();
										uni.navigateTo({
											url: '/pages/order/order',
										})
									}, 100)
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
							indentId: this.indentId
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
						indentId: this.indentId
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
								this.$Request.postT('/app/userMoney/moneyPayIndent?indentId=' + this.indentId)
									.then(res => {
										if (res.code == 0) {
											uni.showToast({
												title: '订单支付成功'
											});
											uni.setStorageSync('current', 0)
											setTimeout(function() {
												uni.hideLoading();
												uni.navigateTo({
													url: '/pages/order/order',
												})
											}, 100)

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
							uni.setStorageSync('current', 0)
							setTimeout(function() {
								uni.hideLoading();
								uni.navigateTo({
									url: '/pages/order/order',
								})
							}, 100)
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

	/deep/.u-checkbox {
		align-items: baseline !important;
	}

	.lj_use {
		width: 150rpx;
		border: 2rpx solid #FF130A;
		color: #FF130A;
		text-align: center;
		line-height: 48rpx;
		border-radius: 40rpx;
		font-size: 23rpx;
	}

	.box_bottom {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 40upx;
	}

	.money_use {
		flex: 1;
		color: #999999;
		font-size: 24rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.money_name {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2upx;
	}

	.money_price {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 48upx;
		font-weight: bold;
		color: red;

		text {
			font-size: 30rpx;
		}
	}

	.money_data {
		color: #999999;
		font-size: 24rpx;
		width: 90%;
		margin: 0 auto;
		margin-top: -8upx;
	}

	.box_tit {
		width: 90%;
		margin: 0 auto;
		height: 80upx;
		display: flex;
	}

	.deteJuan {
		width: 190rpx;
		border: 2rpx solid #FF130A;
		color: #FF130A;
		text-align: center;
		line-height: 48rpx;
		border-radius: 40rpx;
		font-size: 23rpx;
		margin: 20rpx 0;
	}

	//优惠劵
	.scroll-Y {
		height: 540rpx;
	}

	.money_box {
		// height: 300rpx;
		// line-height: 300rpx;
		// text-align: center;
		// font-size: 36rpx;
		width: 100%;
		margin: 0 auto;
		background: #ffffff;
		border-radius: 14upx;
		height: 220rpx;
		margin-bottom: 20upx;
		margin-top: 20rpx;
	}

	.flexs {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.box {
		background: #FFFFFF;
		border-radius: 24rpx;
		margin: 30rpx;
		// padding: 30rpx;
	}

	.addbox {
		display: flex;
		align-items: center;
		justify-content: space-between;
		// margin: 30rpx;
		// padding: 0 30rpx;
		height: 110rpx;
		border-radius: 16rpx;
	}

	.add_cont {
		display: flex;
		align-items: center;
	}

	.add_tit {
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: bold;
		color: #333333;
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


	.tabber {
		height: 120rpx;
		background: #FFFFFF;
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		z-index: 99;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0 30rpx;

		.price {
			display: flex;
			align-items: flex-end;

			.num {
				font-size: 28rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #F2371C;

				text {
					font-size: 40rpx;
				}
			}
		}

		.btn {
			// width: 100%;
			width: 269rpx;
			height: 90rpx;
			background: #02B595;
			border-radius: 45rpx;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 32rpx;
			font-family: PingFang SC;
			font-weight: bold;
			color: #FFFFFF;
		}
	}

	.wenzi {
		font-size: 26rpx;
		color: #333333;

		text {
			color: #02B595;
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