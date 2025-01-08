<template>
	<view>
		<!-- <view class="hedtop">
			<view class="cs">
				{{city}}
				<image src="../static/up.png" style="width:20rpx;height: 17rpx;margin-left: 5rpx;"></image>
			</view>
			<view class="seach">
				<u-search v-model="keyword" placeholder="搜索地址" shape="square" :clearabled="false" :animation="true"
					bg-color="#F5F5F5" height="75"></u-search>
			</view>
		</view> -->


		<view class="addlist">
			<view class="box" v-for="(item,index) in list" :key="index" :class="listIndex==index?'active':''"
				@click="changetan(index,item)">
				<view class="">
					<view>
						<view class="text-lg">{{item.province}}{{item.city}}{{item.county}}{{item.address}}</view>
						<view class="add">{{item.userName}}{{item.userPhone}}</view>
					</view>
				</view>
				<!-- <view v-if="listIndex==index">
					<image src="../static/duigou.png" style="width: 40rpx;height: 34rpx;"></image>
				</view> -->
				<view class="flex justify-end">
					<view style="font-size: 30upx;color: #999999;" @tap='deleteAddressList(item)'>
						删除</view>
					<view style="font-size: 30upx;color: #999999;margin-left: 40rpx; " @tap='goAddress(item.addressId)'>编辑</view>
				</view>
			</view>
			
		</view>


		<view class="tabber">
			<view class="btn" @click="goadd()">添加地址</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				city: '',
				keyword: '',
				page:1,
				limit:10,
				list: [],
				listIndex: '-1',
				index: 0
			}
		},
		onLoad(option) {
			if (option.index) {
				this.index = option.index
			}
			this.getAddlist()
		},
		onShow() {
			this.city = uni.getStorageSync('city')
		},
		methods: {
			goAddress(addressId){
				uni.navigateTo({
					url:'/my/address/add?addressId='+addressId
				})
			},
			openMap() { //打开地图选择位置
				let that = this
				uni.chooseLocation({
					success: function(res) {
						console.log('位置名称：' + res.name);
						console.log('详细地址：' + res.address);
						console.log('纬度：' + res.latitude);
						console.log('经度：' + res.longitude);
						// let data = {
						// 	address: res.name, //地址
						// 	addressDetail: res.address,
						// 	addressLongitude: res.longitude, //地址经度
						// 	addressLatitude: res.latitude, //地址维度
						// 	addressId: '',
						// 	addressDefault: '',
						// }
						// that.$Request.postJson('/app/indent/addUserAddress', data).then(res => {
						// 	if (res.code === 0) {
						// 		that.getAddlist()
						// 	}
						// });
					}
				});

			},

			getAddlist() {
				let data={
					page:this.page,
					limit:this.limit
				}
				this.$Request.getT('/app/address/getAddressList',data).then(res => {
					if (res.code === 0) {
						if (this.page == 1) {
							this.list = res.data.records;
						} else {
							this.list = [...this.list, ...res.data.records]
						}
					}
				});

			},
			changetan(index, item) {
				if (this.index == 1) {
					uni.setStorageSync('addressId', item.addressId)
					this.listIndex = index
					uni.navigateBack()
				} else if (this.index == 2) {
					uni.setStorageSync('addressIds', item.addressId)
					this.listIndex = index
					uni.navigateBack()
				}

			}
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.getAddlist();
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getAddlist();
		}
		
	}
</script>

<style lang="less">
	page {
		background: #FFFFFF;
	}

	.hedtop {
		display: flex;
		align-items: center;
		padding: 0 30rpx;
		position: fixed;
		/* #ifdef H5 */
		top: 85rpx;
		/* #endif */
		/* #ifndef H5 */
		top: 0rpx;
		/* #endif */
		left: 0;
		right: 0;
		z-index: 99;
		background: #FFFFFF;

		.cs {
			width: 20%;
		}

		.seach {
			width: 80%;
		}
	}

	.addlist {
		/* #ifdef H5 */
		// margin-top: 120rpx;
		/* #endif */
		/* #ifndef H5 */
		// margin-top: 120rpx;
		/* #endif */
		.box {
			// width: 100%;
			margin: 20rpx 30rpx;
			background: #F7F7F7;
			border-radius: 8rpx;
			padding: 30rpx;
			font-family: PingFang SC;
			font-weight: bold;
			color: #333333;
			// display: flex;
			// justify-content: space-between;
			// align-items: center;

			.add {
				// font-size: 26rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #7c7c7c;
				margin-top: 10rpx;
			}
		}

		.active {
			border: 2rpx solid #45AEFF;
			border-radius: 8rpx;
			background: #FFFFFF;
		}
	}
.tabber{
	position: fixed;
	bottom: 0rpx;
	left: 0;
	right: 0;
	background: #FFFFFF;
}
	.btn {
		background: #02B595;
		border-radius: 24rpx;
		margin: 30rpx;
		padding: 20rpx 0;
		text-align: center;
		color: #FFFFFF;
	}
</style>