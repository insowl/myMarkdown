# 封装微信小程序http请求

- Promise处理异步请求
- 打印日志
- Loading标识
> httpUtil.js

```javascript
const util = require('./util.js')

const BASE_URL = 'https://example.base_url.com/'
const SHOW_LOG = true  //正式发布前修改

function req(api, method, params) {
  //开始显示navigationBar loading
  wx.showNavigationBarLoading()
  if (SHOW_LOG) {
    //打印请求发起时间戳
    var ts = new Date().getTime()
    util.printLog('>>>>>>>>>>REQ_' + ts + '_' + api + '<<<<<<<<<<', params)
  }
  return new Promise((resolve, reject) => {
    wx.request({
      url: BASE_URL + api,
      method: method,
      data: params,
      success: res => {
        if (SHOW_LOG) {
          //打印响应时间戳与响应消耗时间
          let resTs = new Date().getTime()
          util.printLog('==========RES_' + ts + '_' + api + '==========[' + (resTs - ts) + 'ms]', res)
        }
        resolve(res)
        //停止navigationBar loading
        wx.hideNavigationBarLoading()
      },
      fail: err => {
        if (SHOW_LOG) {
          //打印报错时间戳与响应消耗时间
          let errTs = new Date().getTime()
          util.printLog('==========ERR_' + ts + '_' + api + '==========[' + (errTs - ts) + 'ms]' + err)
        }
        reject(err)
        wx.showToast({
          title: '网络异常',
          icon: 'none'
        })
        //停止navigationBar loading
        wx.hideNavigationBarLoading()
      }
    })
  })
}

module.exports = {
  req: req
}
```
> util.js

```javascript
//将Log以key: value 形式打印
const printLog = function(objStr, obj) {
  let printObj = {}
  let k = objStr
  let v = obj
  printObj[k] = v
  console.log(printObj)
}

module.exports = {
  printLog: printLog
}
```
> page/index.js

```javascript
const http = require('../../utils/httpUtil.js')

Page({
	data: {},
  onLoad: function(options){
		http.req('apiName', 'GET', {
    	a: '1',
      b: '2'  //具体的请求参数
    }).then(res => {
    	//业务逻辑
    }).catch(err => {
    	//异常逻辑
    })
  }  
})
```


