<template xlang="wxml" minapp="mpvue">
	<view class="_cpimg">
		<canvas id="_myCanvas" canvas-id="_myCanvas" :style="{width:cWidth+'px',height:cHeight+'px'}" />
	</view>
</template>

<script>
export default {
	name: "cpimg",
	props: {
		maxWidth: {
			type: Number,
			default: 750
		},
		minWidth: {
			type: Number,
			default: 0
		},
		type: {
			type: String,
			default: 'url'
		},
		ql: {
			type: Number,
			default: 0.92
		},
		src: {
			type: String,
			default: ''
		},
		size: {
			type: Number,
			default: 500
		},
	},
	data() {
		return {
			cWidth: 750,
			cHeight: 750,
		}
	},
	onUnload: function () {
	},
	methods: {
		// 选择照片
		_changImg() {
			let that = this;
			if (that.src == '') {
				uni.chooseImage({
					count: 1, //默认9
					// sizeType: ['original'], //可以指定是原图还是压缩图，默认二者都有
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
					success: function (res) {
						uni.showLoading({
							title: "处理中",
							mask: true
						});
						that._cpImg(res.tempFilePaths[0])
					},
					fail: function (e) {
						that._err(e)
						// console.log(JSON.stringify(res.tempFilePaths));
					}
				});
			} else {
				_cpImg(that.src)
			}
		},
		// 压缩图片
		_cpImg(resPath) {
			let that = this
			let imgFile = resPath
			let sysInfo = uni.getSystemInfoSync()
			// 获取图片信息，
			uni.getImageInfo({
				src: imgFile,
				success: function (image) {
					// 如果图片的大小大于设定值才压缩
					uni.getFileInfo({
						filePath: imgFile,
						success: function (res) {
							_img(image,res.size)
						},
						fail: function (e) {
							that._err(e)
						}
					})
				},
				fail: function (e) {
					that._err(e)
				}
			});
			// 处理图片
			function _img(image, size) {
				let oW = image.width, oH = image.height, scaleWidth = 1, scaleHeight = 1;
				if (size / 1024 >= that.size || image.width >= that.maxWidth) {
					// 控制上传图片的宽高
					if (image.width >= image.height && image.width >= that.maxWidth) {
						image.height = that.maxWidth * image.height / image.width;
						image.width = that.maxWidth;
					} else if (image.width < image.height && image.height >= that.maxWidth) {
						image.width = that.maxWidth * image.width / image.height;
						image.height = that.maxWidth;
					}
					scaleWidth = image.width / oW
					scaleHeight = image.height / oH
				}
				const ctx = uni.createCanvasContext('_myCanvas');
				that.cWidth = image.width;
				that.cHeight = image.height;
				// 旋转图片
				switch (image.orientation) {
					case 'right': // 旋转90度
						that.cWidth = image.height;
						that.cHeight = image.width;
						ctx.rotate(Math.PI / 2);
						ctx.drawImage(imgFile, 0, -image.height, oW * scaleWidth, oH * scaleHeight);
						break;
					case 'down': // 旋转180度
						ctx.rotate(Math.PI);
						ctx.drawImage(imgFile, -image.width, -image.height, oW * scaleWidth, oH * scaleHeight);
						break;
					case 'left': // 旋转-90度
						that.cWidth = image.height;
						that.cHeight = image.width;
						ctx.rotate(3 * Math.PI / 2);
						ctx.drawImage(imgFile, -image.width, 0, oW * scaleWidth, oH * scaleHeight);
						break;
					case 'up':
						ctx.drawImage(imgFile, 0, 0, oW * scaleWidth, oH * scaleHeight);
						break;
					default:
						ctx.drawImage(imgFile, 0, 0, oW * scaleWidth, oH * scaleHeight);
						break;
				}
				ctx.draw(false, () => {
					let dWidth = 0, dHeight = 0
					//#ifdef MP-WEIXIN
					dWidth = Number(that.cWidth)
					dHeight = Number(that.cHeight)
					//#endif
					//#ifdef APP-PLUS
					dWidth = Math.round(Number(that.cWidth) / Number(sysInfo.pixelRatio))
					dHeight = Math.round(Number(that.cHeight) / Number(sysInfo.pixelRatio))
					//#endif
					uni.canvasToTempFilePath({
						width: Number(that.cWidth),
						height: Number(that.cHeight),
						destWidth: dWidth,
						destHeight: dHeight,
						canvasId: '_myCanvas',
						fileType: 'jpg',
						quality: Number(that.ql),
						success: function (res) {
							if (that.type == 'base64') {
								let img = '';
								// let platform = uni.getSystemInfoSync().platform
								// if (platform == 'ios') {
								// 	// 兼容处理：ios获取的图片上下颠倒
								// 	img = that._reverseImgData(res)
								// }
								//#ifdef MP-WEIXIN
								img = 'data:image/jpeg;base64,' + wx.getFileSystemManager().readFileSync(res.tempFilePath, "base64")
								that._result(img)
								//#endif
								//#ifdef APP-PLUS
								// console.log(JSON.stringify(res))
								plus.io.resolveLocalFileSystemURL(res.tempFilePath, function (entry) {
									entry.file(function (file) {
										let fileReader = new plus.io.FileReader();
										// console.log("getFile:" + JSON.stringify(file));
										fileReader.readAsDataURL(file);
										fileReader.onloadend = function (evt) {
											// console.log(JSON.stringify(evt))
											if (evt.target.readyState == 2) {
												that._result(evt.target.result)
											} else {
												that._err(evt)
											}
										}
									});
								}, function (e) {
									that._err(e)
								});
								//#endif
							} else {
								that._result(res.tempFilePath)
							}
						},
						fail: function (e) {
							that._err(e)
						}
					})
				});
			}
		},
		// ios翻转图片
		_reverseImgData(res) {
			var w = res.width
			var h = res.height
			let con = 0
			for (var i = 0; i < h / 2; i++) {
				for (var j = 0; j < w * 4; j++) {
					con = res.data[i * w * 4 + j]
					res.data[i * w * 4 + j] = res.data[(h - i - 1) * w * 4 + j]
					res.data[(h - i - 1) * w * 4 + j] = con
				}
			}
			return res
		},
		// 返回图片数据
		_result(src) {
			uni.hideLoading();
			this.$emit("result", src);
		},
		_err(src) {
			uni.hideLoading();
			this.$emit("err", src);
		}
	}
}
</script>

<style>
._cpimg {
  position: fixed;
  top: -99999upx;
  left: -99999upx;
  z-index: -99999;
}
</style>
