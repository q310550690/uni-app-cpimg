# uni-app 图片压缩插件、带图片自动旋转修正
### 作者：诗小柒
## 说明
uni-app 图片压缩插件、带图片自动旋转修正，支持APP 微信小程序 H5(size、type属性无效)

## 开始使用

### 1.引入组件
```javascript
// template 使用
<cpimg ref="cpimg" @result="cpimgOk" @err="cpimgErr" size="500" maxWidth="1000" ql="0.9" type="url"></cpimg>

// 引入组件
import cpimg from "../../components/cpimg/cpimg.vue"

// 注册组件
components: {
    cpimg
},
```

### 2.属性
`size` Number
```
size 照片大小超过多少KB就压缩  默认值：500
```
`maxWidth` Number
```
maxWidth 照片宽度超过此值就压缩为此宽度  默认值：750
```
`ql` Number 范围 0-1
```
ql 照片压缩比  最大1  默认值：0.92
```
`type` String   可选 url base64
```
type 照片输出格式   默认值：url
```

### 3.方法
`_changImg()` Function
```
_changImg() 选择照片并开始压缩
```

### 4.回调
`result` Function
```
@result="resultFn" 压缩成功的回调 并返回结果
```
`err` Function
```
@err="errFn" 压缩失败的回调 并返回结果
```

### 5.问题
1.H5平台 size 属性无效,type 属性无效（返回的都是base64）
2.无法压缩png