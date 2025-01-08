<template>
	<view>
		<!-- 顶部 -->
		<view class="headTop sticky">
			<view class="flex align-center" @click="gomy()">
				<view class="margin-right-sm">
					<image :src="image_url?image_url:'../../static/image/logo.png'"
						style="width: 66rpx;height: 66rpx;border-radius: 50%;">
					</image>
				</view>
				<view class="">
					{{nickName}}
				</view>
				<!-- <view>{{city}}</view>
				<image src="../../static/image/whiteright.png" style="width: 14rpx;height: 22rpx;margin-left: 8rpx;">
				</image> -->
			</view>
			<!-- 发布类型 -->
			<view class="cartype">
				<scroll-view scroll-x="true" class="scroll-view_H">
					<view class="hd-box-items" @click="bindtype(index)" v-for="(item,index) in carlist" :key="index"
						:class="carIndex==index?'act':''">
						{{item.name}}
					</view>
				</scroll-view>
			</view>
		</view>
		<!-- 中间白色框架 -->
		<view class="box ">
			<!-- 货运类型 -->
			<view class="tabtop">
				<scroll-view scroll-x="true" class="scroll-view_H">
					<view class="hd-box-item" @click="bindopen(index,item)" v-for="(item,index) in list" :key="index"
						:class="current==index?'act':''">
						{{item.serviceName}}
					</view>
				</scroll-view>
			</view>

			<view class="margin-top text-center" style="padding:0 30rpx">
				<!-- 车厢类型 -->
				<scroll-view scroll-x="true" class="scroll-view_Weight">
					<view :class="carType.length>1?'flex align-center':'flex align-center justify-center'">
						<view class="box-item-image" @click="bindcatyp(index,carType)" v-for="(item,index) in carType"
							:key="index" :class="item.isTrue&&carType.length>1?'activeImg':''">
							<image :src="item.exampleImg?item.exampleImg:'../../static/image/che.png'"
								style="width: 200rpx;height: 150rpx;"></image>
							<view>{{item.typeName}}</view>

							<view :class="item.isTrue?'typeimg':''" v-if="item.isTrue">
								<image src="../../static/image/xuanz.png" style="width: 50rpx;height:29rpx;"></image>
							</view>
						</view>
					</view>
				</scroll-view>

				<view class="margin-top-sm flex align-center justify-center" v-if="listWeight">
					<scroll-view scroll-x="true" class="scroll-view_Weight">
						<view class="weight-box-items">
							<view class="weigbox" v-if="listWeight.maxWeight">
								<view>载重</view>
								<view class="wel">{{listWeight.minWeight}}~{{listWeight.maxWeight}}吨</view>
							</view>
							<view class="weigbox" v-if="listWeight.maxCarHeight">
								<view>高度</view>
								<view class="wel">{{listWeight.minCarHeight}}~{{listWeight.maxCarHeight}}米</view>
							</view>
							<view class="weigbox" v-if="listWeight.maxCubicMeter">
								<view>容积</view>
								<view class="wel">{{listWeight.minCubicMeter}}~{{listWeight.maxCubicMeter}}方</view>
							</view>
							<view class="weigbox" v-if="listWeight.carriageMaxLong">
								<view>厢长</view>
								<view class="wel">{{listWeight.carriageMinLong}}~{{listWeight.carriageMaxLong}}米</view>
							</view>

						</view>
					</scroll-view>
				</view>
			</view>
			<!-- 车辆类型 -->
			<view class="padding-top" v-if="CarModelList.length!=0">
				<scroll-view scroll-x="true" class="scroll-view_modelist" style="padding: 0 30rpx;">
					<view class="flex align-center" :class="CarModelList.length>=4?'':'justify-center'">
						<view class="hd-box-modelist" @click="bindmode(index)" v-for="(item,index) in CarModelList"
							:key="index" :class="item.isTrue?'actmodelist':''">
							<view>
								{{item.modelName}}
							</view>
							<view :class="item.isTrue?'xz':''" v-if="item.isTrue">
								<image src="../../static/image/xuanz.png" style="width:38rpx;height: 26rpx;"></image>
							</view>
						</view>
					</view>
				</scroll-view>
			</view>


			<view class="controls-box">
				<view class="controls-tabs">
					<view v-for="(item,index) in classifyLsit" :key="index" v-if="index<4" @tap="change(index,item)"
						:class="classifyIndex == index?'active':'atabs_ds'">
						<view>{{item.name}}</view>
						<view :class="{btna:classifyIndex == index}"></view>
					</view>
				</view>

				<view class="margin-lr ">
					<view class="addbox " @click="changeAdd(1)">
						<view class="add_cont">
							<view class="green"></view>
							<view class="add_tit" v-if="addlist==''">从哪发货？</view>
							<view class="add_tit" v-else>{{addlist}}</view>
						</view>
						<view>
							<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;">
							</image>
						</view>
					</view>
					<view style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
					<view class="addbox " @click="changeAdd(2)">
						<view class="add_cont">
							<view class="orgin"></view>
							<view class="add_tit" v-if="addlists==''">货送去哪？</view>
							<view class="add_tit" v-else>{{addlists}}</view>
						</view>
						<view>
							<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;">
							</image>
						</view>
					</view>
					<view v-if="classifyIndex==1" @click="timeShow = true">
						<view style="width: 100%;height: 1rpx;background: #E6E6E6;"></view>
						<view class="addbox bg">
							<view class="add_cont">
								<image src="../../static/image/data.png" style="width: 36rpx;height: 36rpx;"></image>
								<view class="margin-left-sm text-bold">{{hopeTime?hopeTime:'预约时间'}}</view>
							</view>
							<view>
								<image src="../../static/image/go.png" style="width: 16rpx;height: 25rpx;">
								</image>
							</view>
						</view>
					</view>
				</view>
			</view>

			<view class="orderpay" @click="gopay" v-if="addressId && addressIds">下单</view>
		</view>

		<!-- <view class="hongbao" v-if="HBShow">
			<view style="width: 52%;margin: 0 auto;position: relative;">
				<view @click="HBShow=false"
					style="position: absolute;right: -10rpx;top: -10rpx;font-size: 32rpx;font-weight: bold;">X
				</view>
				<image src="../../static/image/hb_bg.png" class="hb_img" @click="takemoney()"></image>
			</view>
		</view> -->
		<u-popup v-model="HBShow" mode="center">
			<image @click="takemoney()" class="hb_img" src="../../static/image/hb_bg.png" mode="widthFix"></image>
		</u-popup>
		
		
		<!-- 预约时间 -->
		<u-picker v-model="timeShow" :params="params" mode="time" @confirm="confirm"></u-picker>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				is_focus: false,
				remarks: '', //需求备注
				HBShow: false,


				latitude: '',
				longitude: '',
				// count: "",
				carlist: [{
					id: 1,
					name: '同城'
				}, {
					id: 2,
					name: '长途'
				}],
				carIndex: 0,

				list: [],
				listWeight: [],
				current: 0,
				serviceId: '',

				carType: [],
				cartypeIndex: 0,
				typeIds: [],
				CarModelList: [],
				modeIndex: -1,
				modelIds: [],
				addlist: '', //出发地址
				addressId: '',
				addlists: '', //目的地
				addressIds: "",
				// 用户红包
				newUserFlag: 2,

				token: '',


				tuiguang: '',
				tuiguangImg: '',
				arr: [],
				showModal1: true,
				invitationCode: '',

				city: '',
				classifyLsit: [{
					name: '立即用车',
					id: 1
				}, {
					name: '预约用车',
					id: 2
				}],
				classifyIndex: 0,
				hopeTime: '', //预约时间
				timeShow: false,
				params: {
					year: false,
					month: true,
					day: true,
					hour: true,
					minute: true,
					second: false
				},
				nickName: '',
				image_url: '',
				
			}
		},
		onLoad(e) {
			let that = this
			// #ifdef MP-WEIXIN
			if (e.scene) {
				const scene = decodeURIComponent(e.scene);
				that.$queue.setData('invitation', scene.split(',')[0]);
			}
			// #endif

			// 获取邀请码保存到本地
			if (e.invitation) {
				that.$queue.setData('invitation', e.invitation);
			}

			// uni.getLocation({
			// 	type: 'gcj02',
			// 	geocode: true, //设置该参数为true可直接获取经纬度及城市信息
			// 	success: function(res) {
			// 		console.log(res, '地理位置')
			// 		that.latitude = res.latitude
			// 		that.longitude = res.longitude
			// 		uni.setStorageSync('latitude', res.latitude)
			// 		uni.setStorageSync('longitude', res.longitude)

			// 		// #ifdef APP-PLUS
			// 		that.city = res.address.city
			// 		uni.setStorageSync('city', res.address.city)
			// 		// that.getAdd(that.longitude, that.latitude)

			// 		// #endif

			// 		// #ifdef H5

			// 		that.selectCity(that.longitude, that.latitude);
			// 		// #endif

			// 		// #ifdef MP-WEIXIN
			// 		uni.request({
			// 			url: 'https://apis.map.qq.com/ws/geocoder/v1/?location=' + that.latitude +
			// 				',' + that
			// 				.longitude + '&key=TMWBZ-MZJ3O-QR2WO-SDQZY-HRNWO-3ABYP',
			// 			success(re) {
			// 				if (re.statusCode === 200) {
			// 					let citydata = re.data.result.address_component.city
			// 					console.log("获取城市名称成功", citydata)
			// 					that.city = citydata ? citydata : '未知'
			// 					uni.setStorageSync('city', citydata)
			// 					// that.getAdd(that.longitude, that.latitude)
			// 				} else {
			// 					console.log("获取信息失败，请重试！")
			// 				}
			// 			}
			// 		});
			// 		// #endif
			// 	},
			// 	fail: function() {
			// 		console.log('获取地址失败')
			// 	}
			// })
			this.getcarType()
		},
		onShow() {
			if (uni.getStorageSync('token')) {
				this.nickName = uni.getStorageSync('nickName')
			} else {
				this.nickName = '登录'
			}
			//获取选择的地址
			let that = this
			uni.$on('startAddress', (data) => {
				that.addlist = data.address
				that.addressId = data.addressId
			})
			uni.$on('endAddress', (data) => {
				that.addlists = data.address
				that.addressIds = data.addressId
			})
			this.image_url = uni.getStorageSync('image_url')

			this.token = uni.getStorageSync('token')
			if (this.token) {
				this.getuserinfo()
			} else {
				this.image_url = '../../static/image/logo.png';
			}
			this.$Request.getT('/app/common/type/307').then(res => { //用户端骑手取消订单通知 307
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.arr.push(res.data.value)
					}
				}
			})
			if (this.showModal1) {
				// #ifdef MP-WEIXIN
				this.openMsg()
				// #endif
			}
		},
		onShareAppMessage(res) { //发送给朋友
			return {
				title: this.tuiguang,
				path: '/pages/index/index?invitation=' + this.invitationCode,
				imageUrl: this.tuiguangImg,
			}
		},
		onShareTimeline(res) { //分享到朋友圈
			return {
				title: this.tuiguang,
				path: '/pages/index/index?invitation=' + this.invitationCode,
				imageUrl: this.tuiguangImg,
			}
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
			openYouhui() {
				if (this.youhuiList.length == 0) {
					uni.showToast({
						title: '当前暂无可使用的优惠劵',
						icon: 'none'
					})
					return
				}
				this.youhuiShow = true
			},
			bindtype(index) { //货运类型
				this.carIndex = index
				this.current = 0
				this.getcarType()
			},
			bindopen(index, item) {
				this.current = index
				this.serviceId = item.serviceId
				this.listWeight = item
				this.getcartype()
				this.getCarModelList()
			},
			getcarType() { //货运车型
				let type = this.carlist[this.carIndex].id
				let data = {
					type: type,
				}
				this.$Request.getT('/app/serviceType/getServiceList', data).then(res => {
					if (res.code == 0) {
						this.list = res.data.records
						this.serviceId = res.data.records[0].serviceId
						this.listWeight = res.data.records[0]
						this.getcartype()
						this.getCarModelList()
					}
				})
			},
			bindcatyp(ind, carType) {
				this.cartypeIndex = ind
				let arr = []
				let skust = this.carType
				skust.map((item, index) => {
					let data
					if (index == this.cartypeIndex) {
						item.isTrue = !item.isTrue
					}
				})
				skust.map(item => {
					if (item.isTrue) {
						arr.push(item.typeId)
					}
				})
				this.typeIds = arr
			},
			getcartype() {
				this.$Request.getT('/app/carType/getCarTypeList?serviceId=' + this.serviceId).then(res => {
					if (res.code == 0) {
						let skust = res.data.records
						let arr = []
						skust.map(item => {
							let data = {}
							item.isTrue = true
							data = item.typeId
							arr.push(data)
						})
						this.carType = skust
						this.typeIds = arr
						// console.log(this.typeIds.toString())
					}
				})
			},
			bindmode(ind) {
				this.modeIndex = ind
				let arr = []
				let skust = this.CarModelList
				skust.map((item, index) => {
					let data
					if (index == this.modeIndex) {
						item.isTrue = !item.isTrue
					}
				})
				skust.map(item => {
					if (item.isTrue) {
						arr.push(item.modelId)
					}
				})
				this.modelIds = arr
			},
			getCarModelList() {
				this.$Request.getT('/app/carModel/getCarModelList?serviceId=' + this.serviceId).then(res => {
					if (res.code == 0) {
						let skust = res.data.records
						skust.map(item => {
							item.isTrue = false
						})
						this.CarModelList = skust
						// console.log(this.CarModelList)
					}
				})
			},



			gomy() {
				if (uni.getStorageSync('token')) {
					uni.navigateTo({
						url: '/pages/my/my'
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}

			},
			changeAdd(index) {
				let token = uni.getStorageSync('token')
				if (token) {
					uni.navigateTo({
						url: '/my/address/index?index=' + index
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}
			},
			change(index, item) {
				this.classifyIndex = index;
			},
			gopay() {
				if (this.typeIds.length == 0) {
					uni.showToast({
						title: '请选择车厢类型',
						icon: 'none'
					})
					return
				}
				if (this.addressId == '') {
					uni.showToast({
						title: '请选择发货地址',
						icon: 'none'
					})
					return
				}
				if (this.addressIds == '') {
					uni.showToast({
						title: '请选择送货地址',
						icon: 'none'
					})
					return
				}

				let modelIds = this.modelIds.toString()
				let typeIds = this.typeIds.toString()
				let data = {
					serviceId: this.serviceId, //发布类型 短途 长途
					typeIds: typeIds, //车厢类型id
					modelIds: modelIds, //车辆类型id
					indentType: this.classifyLsit[this.classifyIndex].id, //订单类型 1立即用车 2预约用车
					fhadd: this.addressId,
					shadd: this.addressIds,
					hopeTime: this.hopeTime
				}
				uni.navigateTo({
					url: '/my/order/orderDet?data=' + JSON.stringify(data)
				})
				// this.addlist = ''
				// this.addlists = ''
				// this.addressId = ''
				// this.addressIds = ''
			},
			// 分享文案和图片
			getZiZhi() {
				this.$Request.getT('/app/common/type/276').then(res => {
					if (res.code === 0) {
						this.tuiguang = res.data.value;
					}
				});
				this.$Request.getT('/app/common/type/277').then(res => {
					if (res.code === 0) {
						this.tuiguangImg = res.data.value;
					}
				});
			},
			goWeb(url) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendMsg')) {
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
				console.log(url.indexOf('/pages/') !== -1)
				return
				if (url.indexOf('/pages/') !== -1) {
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
			},

			// 跳转发布页面
			goNav(e) {
				// #ifdef MP-WEIXIN
				if (uni.getStorageSync('sendMsg')) {
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
				if (this.token) {
					uni.navigateTo({
						url: e
					})
				} else {
					uni.navigateTo({
						url: '/pages/my/register'
					})
				}

			},

			selectCity(longitude, latitude) {
				this.$Request.getT('/app/Login/selectCity?lat=' + latitude + '&lng=' + longitude).then(res => {
					if (res.code === 0) {
						this.city = res.data.city
						uni.setStorageSync('city', this.city)
					}
				});
			},

			// 获取用户信息
			getuserinfo() {
				this.$Request.getT('/app/userinfo/findUserInfoById').then(res => {
					console.log(res)
					if (res.code == 0 && res.data) {
						if (res.data.newUserFlag) {
							this.newUserFlag = res.data.newUserFlag
							console.log(this.newUserFlag)
							if (this.newUserFlag == 1) {
								this.HBShow = true
							} else {
								this.HBShow = false
							}
						}

					}
				});
			},
			// 红包
			takemoney() {
				this.$Request.getT('/app/userinfo/newUserCoupon').then(res => {
					if (res.code === 0) {
						this.HBShow = false
						uni.showToast({
							title: '领取成功',
							icon: 'none',
							duration: 3000
						})
						this.getuserinfo()
						// setTimeout(function() {
						// 	uni.navigateTo({
						// 		url: '/pages/my/hongbao/hongbao'
						// 	})
						// }, 100)
					} else {
						uni.showToast({
							title: res.msg,
							icon: "none"
						})
						this.newUserFlag = ''
					}
				});
			},
			// 开启订阅消息
			openMsg() {
				console.log('订阅消息')
				var that = this
				uni.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						console.log(ret.subscriptionsSetting, '------------------')
						// if (ret.subscriptionsSetting.itemSettings && Object.keys(ret.subscriptionsSetting.itemSettings).length == 2) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							console.log(99999)
							uni.setStorageSync('sendMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										console.log(that.arr)
										wx.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												console.log(JSON.stringify(re),
													'++++++++++++++')
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
													// uni.setStorageSync('sendMsg', true)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										// uni.setStorageSync('sendMsg', true)
										console.log('确认')
										that.showModal1 = false
									} else if (res.cancel) {
										console.log('取消')
										// uni.setStorageSync('sendMsg', false)
										that.showModal1 = true
									}
								}
							})
						}
					}
				})
			},
		}
	}
</script>

<style lang="less">
	page {
		// background: #FFFFFF;
		background: #02B595;
	}

	/deep/.u-mode-center-box {
		background-color: rgba(255, 255, 255, 0) !important;
	}

	.headTop {
		background: #02B595;
		color: #FFFFFF;
		position: sticky;
		top: 0;
		left: 0;
		right: 0;
		z-index: 99;
		/* #ifndef H5 */
		padding: 65rpx 30rpx 25rpx;
		/* #endif */
		/* #ifdef H5 */
		padding: 30rpx 30rpx 25rpx;
		/* #endif */
	}

	.cartype {
		margin-top: 20rpx;
	}

	.box {
		background: #FFFFFF;
		height: 100vh;
		border-radius: 40rpx 40rpx 0 0;
		padding-top: 30rpx;
	}

	.tabtop {
		background: #FFFFFF;
		margin: 0rpx 30rpx;
	}

	.scroll-view_H {
		width: 100%;
		white-space: nowrap;

	}

	.hd-box-item {
		display: inline-block;
		padding: 10rpx 25rpx 10rpx 25rpx;
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #333333;
		margin-right: 10rpx;
	}

	.hd-box-items {
		display: inline-block;
		padding: 10rpx 25rpx 10rpx 25rpx;
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #FFFFFF;

		margin-right: 25rpx;
	}

	.act {
		background: #F2FFF9;
		border: 1rpx solid #02B595;
		border-radius: 30rpx;
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #02B595;
	}

	// 车辆图片样式
	.box-item-image {
		border: 1rpx solid #FFFFFF;
		padding: 0rpx 20rpx 10rpx 20rpx;
		// margin: 0 15rpx;
		margin-right: 15rpx;
	}

	.activeImg {
		border: 1rpx solid #02B595;
		background: #F2FFF9;
		border-radius: 15rpx;
		padding: 0rpx 20rpx 10rpx 20rpx;
		position: relative;

	}

	.typeimg {
		position: absolute;
		top: 0rpx;
		right: 0rpx;
	}

	// 车高度重量样式
	.scroll-view_Weight {
		width: 100%;
		white-space: nowrap;


	}

	.weight-box-items {
		display: flex;
		align-items: center;
		justify-content: center;

	}

	.weigbox {
		margin-right: 20rpx;
		font-size: 24rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #999999;
	}

	.wel {
		color: #333333;
		font-size: 26rpx;
	}

	// 车型
	.scroll-view_modelist {
		width: 100%;
		white-space: nowrap;

	}

	.hd-box-modelist {
		display: inline-block;
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #333333;
		margin-right: 25rpx;
		background: #F7F7F7;
		position: relative;
		border: 1rpx solid #F7F7F7;
		padding: 12rpx 35rpx;
		border-radius: 12rpx;
	}

	.xz {
		position: absolute;
		top: -5rpx;
		right: 0rpx;

	}

	.actmodelist {
		background: #F2FFF9;
		border: 1rpx solid #02B595;
		// border-radius: 30rpx;
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 500;
		color: #02B595;
		border-radius: 12rpx;
	}



	.controls-box {
		background: #F7F7F7;
		margin: 40upx 30rpx 20rpx;
		border-radius: 32upx;
		padding-bottom: 20rpx;
	}

	/* tab选项卡 */
	.controls-tabs {
		/* width: 100%; */
		height: 90rpx;
		display: flex;
		font-size: 28rpx;
		overflow-x: auto;
		justify-content: space-between;
		align-items: center;
		margin-top: 10rpx;
		padding: 0 120rpx;
	}

	.atabs_ds {
		text-align: center;
		height: 55rpx;
	}

	.active {
		color: #02B595;
	}

	.btna {
		background: #02B595;
		width: 40rpx;
		height: 6rpx;
		/* margin: 9rpx 10rpx 10rpx 29rpx; */
		margin: 5rpx auto;
		border-radius: 55rpx;

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
		font-size: 34rpx;
		font-family: PingFang SC;
		font-weight: bold;
		color: #333333;
	}

	.bg {
		background: #F5F5F5;
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

	.orderpay {
		background: #02B595;
		color: #FFFFFF;
		// margin-top: 60rpx;
		margin: 60rpx 30rpx;
		border-radius: 24rpx;
		text-align: center;
		padding: 25rpx 0;
	}
</style>