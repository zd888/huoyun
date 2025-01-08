<template>
	<view>
		<!-- #ifdef MP-WEIXIN -->
		<view class="part1">

			<view class="name">
				<u-field v-model="name" placeholder="姓名" icon="account-fill" label-align="center">
				</u-field>
			</view>
			<view class="mobile">
				<u-field v-model="mobile" placeholder="联系电话" icon="phone-fill" type="number" label-align="center">
				</u-field>
			</view>
			<view class="address" @click="bindmap">
				<u-field v-model="cityaddress" placeholder="地址" :disabled="true" icon="map-fill" label-align="center">
				</u-field>
			</view>
			<view class="detailaddress">
				<u-field v-model="detailaddress" placeholder="详细地址" icon="map-fill" label-align="center">
				</u-field>
			</view>
			<!-- 	<view
				style="height: 100upx;background:#FFFFFF;display: flex;margin-top: 30upx;justify-content: space-between;padding: 0rpx 22rpx;">
				<view style="font-size: 30upx;color: #333333;text-align: left;padding: 30upx;width: 70%;">设为默认地址</view>
				<switch type="switch" :checked='isDefault === 1 ? true : false' @change="switch1Change"
					style="padding: 20upx;transform:scale(0.9)" color="#FF7F00" />
			</view> -->
			<view class="btn" @click="bindhelp">确定</view>
		</view>
		<!-- #endif -->
		<!-- #ifndef MP-WEIXIN -->
		<view class="part1">
			<view class="mobile">
				<u-field v-model="mobile" placeholder="联系电话" icon="phone-fill" label-align="center">
				</u-field>
			</view>
			<view class="name">
				<u-field v-model="name" placeholder="姓名" icon="account-fill" label-align="center">
				</u-field>
			</view>

			<view class="address">
				<u-field @click="bindmap" v-model="cityaddress" placeholder="地址" :disabled="true" icon="map-fill"
					label-align="center">
				</u-field>
			</view>
			<view class="detailaddress">
				<u-field v-model="detailaddress" placeholder="详细地址" icon="map-fill" label-align="center">
				</u-field>
			</view>
			<!-- 	<view
				style="height: 100upx;background:#FFFFFF;display: flex;margin-top: 30upx;justify-content: space-between;padding: 0rpx 22rpx;">
				<view style="font-size: 34upx;color: #333333;text-align: left;padding: 30upx;width: 70%;">设为默认地址</view>
				<switch type="switch" :checked='isDefault === 1 ? true : false' @change="switch1Change"
					style="padding: 20upx;transform:scale(0.9)" color="#FF7F00" />
			</view> -->
			<view class="btn" @click="bindhelp">确定</view>
		</view>
		<!-- #endif -->

	</view>
</template>

<script>
	// import pickerAddress from '@/components/wangding-pickerAddress/wangding-pickerAddress.vue'

	export default {
		components: {

		},
		data() {
			return {
				// provinces: '选择地址',
				// consignee: '',
				// mobile: '',
				// detail: '',
				// createAt: '',
				id: '',
				isDefault: 0,
				// id: 0,
				mobile: '',
				name: '',
				cityaddress: '',
				detailaddress: '',
				latitude: '',
				longitude: '',
				province: '',
				city: '',
				county: '',
			}
		},
		onLoad(d) {
			let id = d.addressId;
			if (id) {
				this.id = d.addressId;
				uni.setNavigationBarTitle({
					title: '编辑地址'
				});
				this.getAddressList(id);
			} else {
				uni.setNavigationBarTitle({
					title: '添加地址'
				});
			}
		},
		methods: {
			change(data) {
				this.provinces = data.data.join('')
			},
			bindmap() {
				var that = this
				// if (that.ciaddress == '') {
				uni.chooseLocation({
					success: function(res) {
						// console.log(res)
						console.log('位置名称：' + res.name);
						console.log('详细地址：' + res.address);
						console.log('纬度：' + res.latitude);
						console.log('经度：' + res.longitude);
						that.detailaddress = res.name
						that.latitude = res.latitude
						that.longitude = res.longitude
						that.shengcheng(res.longitude, res.latitude)
					}
				});
				// }
			},
			shengcheng(longitude, latitude) {
				this.$Request.get('/app/Login/selectCity?lat=' + latitude + '&lng=' + longitude).then(res => {
					if (res.code === 0) {
						this.province = res.data.province
						this.city = res.data.city
						this.county = res.data.district
						this.cityaddress = res.data.province + res.data.city + res.data.district
						// console.log(this.address)
					}
				});

			},
			getAddressList(id) {
				uni.showLoading({
					title: '加载中...'
				});
				this.$Request.getT('/app/address/getAddressInfo?addressId=' + id).then(res => {
					if (res.code == 0) {
						this.name = res.data.userName ? res.data.userName : '';
						this.mobile = res.data.userPhone ? res.data.userPhone : '';
						this.cityaddress = res.data.province + res.data.city + res.data.county
						this.detailaddress = res.data.address;
						this.isDefault = res.data.isDefault;
						this.province = res.data.province
						this.city = res.data.city
						this.county = res.data.county
						this.userId = res.data.userId;
						this.latitude = res.data.lat;
						this.longitude = res.data.lng;
					}
					uni.hideLoading();
				});
			},
			bindhelp() {
				if (this.id) {
					this.$queue.showLoading('修改中...')
				} else {
					this.$queue.showLoading('添加中...')
				}

				if (this.mobile.length < 11) {
					uni.hideLoading();
					uni.showToast({
						icon: 'none',
						title: '请输入正确的电话信息'
					});
					return;
				}
				if (this.name != undefined && this.name != '') {} else {
					uni.hideLoading();
					uni.showToast({
						icon: 'none',
						title: '收货人不能为空'
					});
					return;
				}
				if (this.cityaddress == '选择地址') {
					uni.hideLoading();
					uni.showToast({
						icon: 'none',
						title: '请选择地址信息'
					});
					return;
				}
				if (this.detailaddress != undefined && this.detailaddress != '') {} else {
					uni.hideLoading();
					uni.showToast({
						icon: 'none',
						title: '地址信息不能为空'
					});
					return;
				}
				let userId = this.$queue.getData('userId');
				let data = {
					userName: this.name,
					userPhone: this.mobile,
					address: this.detailaddress,
					lat: this.latitude,
					lng: this.longitude,
					province: this.province,
					city: this.city,
					county: this.county,
					userId: userId,
					addressId: this.id,
					isDefault: this.isDefault,
					addressId: this.id
				}

				this.$Request.postT('/app/address/saveAddress', data).then(res => {
					if (res.code == 0) {
						uni.hideLoading()
						this.$queue.showToast("添加成功!");
						setTimeout(d => {
							uni.navigateBack();
						}, 1000)
					} else {
						uni.hideLoading()
						this.$queue.showToast(res.msg);
					}
				});
			},
			//校验手机格式
			checkMobile(mobile) {
				return RegExp(/^1[34578]\d{9}$/).test(mobile);
			},
			switch1Change(e) {
				this.isDefault = e.detail.value == true ? 1 : 0;
			}
		}
	}
</script>

<style>
	page {
		background: #FFFFFF;
	}

	.part1 {
		width: 100%;
		background: #ffffff;
		margin-top: 24rpx;
		padding-bottom: 40upx;
	}

	.btn {
		width: 90%;
		height: 80upx;
		background: #02B595;
		border-radius: 14upx;
		margin: 0 auto;
		color: white;
		text-align: center;
		line-height: 80upx;
		letter-spacing: 2upx;
		margin-top: 40upx;
	}

	.u-icon__icon {
		font-size: 45rpx !important;
	}

	.u-field {
		padding: 35rpx 28rpx !important;
	}

	.u-label {
		flex: 0 0 42px !important;
	}

	.u-field__input-wrap {
		font-size: 30rpx !important;
	}

	.u-textarea-class {
		font-size: 30rpx !important;
	}
</style>