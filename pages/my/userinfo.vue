<template>
	<view>
		<view class="">
			<view class="usermain-item ">
				<view>头像</view>
				<view>
					<image src="../../static/image/logo.png" v-if="avatar==null" mode=""
						style="width: 111rpx;height: 111rpx;border-radius: 50%;"></image>
					<!-- #ifdef MP-WEIXIN -->
					<button v-else open-type="chooseAvatar" @chooseavatar="onChooseAvatar">
						<image style="width: 100rpx;height: 100rpx;border-radius: 50%" :src="avatar"></image>
					</button>
					<!-- #endif -->
					<!-- #ifndef MP-WEIXIN -->
					<image v-else :src="avatar" style="width: 100rpx;height: 100rpx;border-radius: 100rpx;"
						@click="uploadImg()">
					</image>
					<!-- #endif -->
					<!-- <image v-else :src="avatar" mode="" style="width: 111rpx;height: 111rpx;border-radius: 50%;">
					</image> -->
				</view>
			</view>
			<view class="usermain-item item-padding ">
				<view>用户名</view>
				<view>
					<view class="cu-form-group">
						<input type="nickname" v-model="userName" placeholder="请输入用户名" />
					</view>
				</view>
			</view>
			<view class="usermain-item item-padding ">
				<view>年龄</view>
				<view>
					<view class="cu-form-group">
						<input v-model="age" />
					</view>
				</view>
			</view>
			<!-- <view class="usermain-item item-padding">
				<view  >姓名</view>
				<view class="cu-form-group">
					<input    v-model="realName" placeholder="请填写您的真实姓名" />
				</view>
			</view> -->

			<!-- <view class="usermain-item item-padding ">
				<view>职业</view>
				<view>
					<view class="cu-form-group">
						<input  v-model="occupation" placeholder="请输入职业"/>
					</view>
				</view>
			</view> -->
		</view>
		<view class="footer-btn">
			<view class="usermain-btn" @click="messagebtn()">保存</view>
		</view>
	</view>
</template>

<script>
	import configdata from '../../common/config.js';
	export default {
		data() {
			return {
				occupation: '',
				avatar: '../../static/image/logo.png',
				userName: '',
				nickName: '',
				userId: '',
				realName: '',
				weChatId: "",
				password: '',
				platform: '',
				createTime: '',
				money: '',
				jiFen: '',
				status: '',
				zhiFuBao: '',
				zhiFuBaoName: '',
				age: 0
			};
		},
		onLoad(e) {
			this.getUserInfo()
		},
		methods: {
			//微信填写能力获取头像
			onChooseAvatar(e) {
				// console.log(e.detail.avatarUrl)
				let that = this;
				let token = uni.getStorageSync('token');
				uni.showLoading({
					title: '上传中...'
				});
				uni.uploadFile({
					url: that.config("APIHOST1") + '/alioss/upload', //仅为示例，非真实的接口地址
					// url: 'https://jianzhi.xianmaxiong.com/sqx_fast/alioss/upload', //仅为示例，非真实的接口地址
					filePath: e.detail.avatarUrl,
					// filePath: res.tempFilePaths[0],
					header: {
						token: token
					},
					name: 'file',
					success: uploadFileRes => {
						let url = JSON.parse(uploadFileRes.data).data;
						that.$Request.postJson(
							'/app/user/updateUserImageUrl?avatar=' + url).then(
							res => {
								uni.hideLoading();
								if (res.code === 0) {
									that.$queue.showToast(
										"更新成功");
									that.getUserInfo();
								}
							});
					}
				});

			},
			goMyAddress() {
				uni.navigateTo({
					url: '../jifen/myaddress'
				});
			},
			uploadImg() {
				let token = uni.getStorageSync('token')

				if (!token) {
					this.goLoginInfo();
					return;
				}
				let that = this;
				var url = null;
				uni.showActionSheet({
					// itemList按钮的文字接受的是数组
					itemList: ["查看头像", "从相册选择图片"],
					success(e) {
						var index = e.tapIndex
						if (index === 0) {
							// 用户点击了预览当前图片
							// 可以自己实现当前头像链接的读取
							let url = that.avatar;
							let arr = []
							arr.push(url)
							uni.previewImage({
								// 预览功能图片也必须是数组的
								urls: arr
							})
						} else if (index === 1) {
							uni.chooseImage({
								count: 1, //默认9
								sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
								sourceType: ['album'], //从相册选择
								success: function(res) {
									uni.showLoading({
										title: '上传中...'
									});
									let token = uni.getStorageSync('token');
									let userId = uni.getStorageSync('userId');
									uni.uploadFile({
										// url: that.config("APIHOST1") +'/alioss/upload', //真实的接口地址
										url:'https://pt2.0.xianmxkj.com/sqx_fast/alioss/upload', //真实的接口地址
										filePath: res.tempFilePaths[0],
										header: {
											token: token
										},
										name: 'file',
										success: uploadFileRes => {
											url = JSON.parse(uploadFileRes.data);
											that.avatar = url.data
											uni.hideLoading();
										}
									});
								}
							});
						}
					}
				})
			},
			config: function(name) {
				var info = null;
				if (name) {
					var name2 = name.split("."); //字符分割
					if (name2.length > 1) {
						info = configdata[name2[0]][name2[1]] || null;
					} else {
						info = configdata[name] || null;
					}
					if (info == null) {
						let web_config = cache.get("web_config");
						if (web_config) {
							if (name2.length > 1) {
								info = web_config[name2[0]][name2[1]] || null;
							} else {
								info = web_config[name] || null;
							}
						}
					}
				}
				return info;
			},
			getUserInfo() {
				let userId = uni.getStorageSync('userId')
				this.$Request.getT("/app/userinfo/findUserInfoById").then(res => {
					if (res.code == 0) {
						this.$queue.setData('avatar', res.data.avatar);
						this.$queue.setData('userId', res.data.userId);
						this.$queue.setData('userName', res.data.userName);
						this.$queue.setData('age', res.data.age);
						this.age = res.data.age ? res.data.age : 0;
						this.occupation = res.data.occupation;
						this.avatar = res.data.avatar;
						this.userName = res.data.nickName;
					}
					uni.hideLoading();
				});


			},
			// 保存
			messagebtn() {
				if (!this.userName) {
					// this.$queue.showToast('用户名不能为空');
					uni.showToast({
						title: "用户名不能为空",
						icon: "none"
					})
				} else {
					uni.showModal({
						title: '温馨提示',
						content: '确定保存信息',
						success: e => {
							if (e.confirm) {
								this.$Request.postJson("/app/user/updateUser", {
									nickName: this.userName,
									avatar: this.avatar,
									occupation: this.occupation,
									age: this.age
								}).then(res => {
									if (res.code === 0) {
										uni.showToast({
											title: '保存成功',
											icon: "none"
										})
										setTimeout(function() {
											uni.navigateBack()
										}, 1000)
									} else {
										uni.showToast({
											title: res.msg,
											icon: "none"
										})
									}
								})
							}
						}
					});
				}
			}
		},
		// userphone(){
		// 	uni.navigateTo({
		// 		url:'/pages/my/userphone'
		// 	})
		// }

	};
</script>

<style>
	page {
		/* background: #1c1b20; */
		background: #FFFFFF;
	}

	button::after {
		border: none;
		background-color: none;
	}

	button {
		position: relative;
		display: block;
		margin-left: auto;
		margin-right: auto;
		padding-left: 0px;
		padding-right: 0px;
		box-sizing: border-box;
		text-decoration: none;
		/* line-height: 1.35; */
		overflow: hidden;
		color: #666666;
		/* background-color: #fff; */
		background-color: rgba(255, 255, 255, 0) !important;
		width: 100%;
		height: 100%;
		/* margin-top: 90rpx; */

	}


	.usermain-item {
		display: flex;
		align-items: center;
		margin: 0 40rpx;
		padding: 10rpx 0;
		justify-content: space-between;
		border-bottom: 1rpx solid #e5e5e5;
		/* border-bottom: 2rpx solid #f2f2f2; */
	}

	.usermain-item.item-padding {
		/* padding: 0; */
	}

	.cu-form-group {
		padding: 0;
		background: #ffffff;
		text-align: right;
	}

	.cu-form-group input {
		background: #ffffff;
		font-size: 28rpx;
		/* color: #fff; */

	}

	.footer-btn {
		margin-top: 150rpx;
	}

	.footer-btn .usermain-btn {
		color: #FFFFFF;
		background: #02B595;
		text-align: center;
		width: 450rpx;
		height: 80rpx;
		font-size: 28rpx;
		line-height: 80rpx;
		margin: 0 auto;
		border-radius: 40rpx;
	}
</style>