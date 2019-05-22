<template xlang="wxml">
	<view class="content">
		<button @tap="addPhoto">添加图片</button>
		<!-- <cpimg ref="cpimg" @ok="cpimgOk" @err="cpimgErr" size="500" maxWidth="300" ql="0.5" type="base64"></cpimg> -->
		<cpimg ref="cpimg" @result="cpimgOk" @err="cpimgErr" :size="500" :maxWidth="1000" :ql="0.9" type="url"></cpimg>
		<view class="img">
			<image v-for="(v,i) in src" :key="i" :src="v" class="showimg" mode="aspectFit" />
		</view>
		<view class="imgPath">
			压缩后文件路径请看控制台
			<!-- <view>{{src}}</view> -->
		</view>
	</view>
</template>

<script>
import cpimg from "../../components/cpimg/cpimg.vue"
export default {
	data() {
		return {
			src: [],
		};
	},
	methods: {
		// 添加图片
		addPhoto() {
			let that = this
			that.$refs.cpimg._changImg()
		},
		cpimgOk(file) {
			let that = this
			that.src = file
			console.log(file)
		},
		cpimgErr(e) {
			console.log(e)
		},
	},
	components: {
		cpimg
	},
}
</script>

<style>
.content {
	flex: 1;
	justify-content: center;
	align-items: center;
}
.showimg {
	margin: 20upx 0;
	width: 750upx;
}
.imgPath {
	font-size: 20upx;
	word-break: break-all;
}
</style>
