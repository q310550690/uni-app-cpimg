# uni-app 图片压缩插件、带图片自动旋转修正
### 作者：诗小柒
## 说明
uni-app 图片压缩插件、带图片自动旋转修正，支持APP 微信小程序 H5(size、type属性无效)

### 使用方法
在 `script` 中引入组件
``` javascript
import cpimg from "../../components/cpimg/cpimg.vue"
export default {
    components: {cpimg}
}
```
在 `template` 中使用
``` javascript
<cpimg ref="cpimg" @result="cpimgOk" @err="cpimgErr" size="500" maxWidth="1000" ql="0.9" type="url"></cpimg>
```

### 属性
|属性名|类型|默认值|可选值|说明|
|:-|:-:|:--:|:--:|-:|
|size|Number|500| |照片大小超过多少KB就压缩|
|maxWidth|Number|750| |照片宽度超过此值就压缩为此宽度|
|ql|Number|0.92|0-1 |照片压缩比 范围 0-1|
|type|String|url|url、base64|照片压缩比 范围 0-1|


### 方法
|方法名|参数|默认值|说明|
|:-|:-:|:--:|-:|
|_changImg()| | |选择照片并开始压缩|

### 事件
|事件名|返回值|说明|
|:-|:-:|-:|
|result|图片base64或临时地址|压缩成功的回调 并返回结果|
|err| |压缩失败的回调 并返回结果|

### 问题
1.H5平台 size 属性无效,type 属性无效（返回的都是base64）
2.无法压缩png