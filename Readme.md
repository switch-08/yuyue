# 买 jd 预约抢购商品

### 此代码基于原作者 https://github.com/tychxn/jd-assistan 进行修改

## 单独支持预约-抢购-成功后直接提交订单的商品

狗东现有俩种抢购模式

1. 到时间直接抢购(例如口罩)
2. 到时间先添加购物车->到购物车提交订单(显卡)  
3. _请一定要搞清楚使用哪种模式, 否则抢不到_

## Quick Start

1 **`必须配置config文件`**, 在目录下新创建 `config.js` 文件。格式如下（可以直接复制过去填充内容）

```js
// 最简单只加 eid 和 fp
const config = {
  // 支持自定义 UA
  userAgent:
    'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36',
  // 是否微信公众号推送抢购结果, 非必填
  messenger: true, // true, false
  // key值在 http://sc.ftqq.com/3.version 查看申请使用的方法, 把key粘贴至这里, 关注网站中公众号即可
  sckey: '', // string, messenger 有则为必填, 不填写不影响抢购流程

  // 必填,
  // 方式1: jd登录 pc 端, 随便提交一个订单,利用谷歌浏览器devtools, 选择 `network`,
  // 查看 submitOrder (注意要勾选 preserve log, 否则提交订单跳转后看不到该接口了) 接
  // 口, 把接口提交的数据里面的 `eid`, 和 `fp` 俩个字段分别粘贴过来
  // 方式2: 或者电脑打开jd登陆页面, devtools, 选择 `Elements`, 搜索 eid 和 sessionId(即fp), 在搜索到的input元素上面
  // value 属性中的值复制过来
  eid: '', // string, 必填
  fp: '', // string, 必填
};

module.exports = config;
```

2 需要有 node 开发环境开发使用 nodejs v12.x, 没有 nodejs 官网 下载一个就行. 下载完安装完中终端执行 `node -v` 查看版本. 其自带包管理器 `npm`

3 在目录下执行 `npm install` 或者 `yarn`(不熟悉, 就使用 npm 一样的) 安装依赖, 已经默认配置使用 `taobao` 镜像地址下载依赖包

4 配置`tasks-pool.js`(以前在*index.js* 中, 现在迁移出来了) 中设置日期`date`和要抢购的 `skuId`, 格式如下

```js
// 年     月    日     时    分   秒     毫秒
// 2020, 0-11, 1-31, 0-24, 0-60  0-60  0-1000
// 例如 2020-3-4 10:00:00.400
// (2020, 2, 4, 10, 0, 0, 400)

// 修改使用的时间
// 2020/3/3 10:00:00.400
const dd1 = new Date(2020, 2, 3, 10, 0, 0, 400).getTime();
// 2020/3/3 20:00:00.400
const dd2 = new Date(2020, 2, 3, 20, 0, 0, 400).getTime();
// 2020/3/3 21:00:00.400
const dd3 = new Date(2020, 2, 3, 21, 0, 0, 400).getTime();

// 修改这里, 添加skuId, 和抢购时间 date, 需要更改 月/日 时:分:秒:毫秒
// skuId 获取方法， 打开任意一个商品详情页如 `https://item.jd.com/100011521400.html`, 则 `100011521400` 就是其skuId
exports.pool = [
  // { skuId: '100011521400', date: dd1 },
  { skuId: '100011551632', date: dd2 },
  { skuId: '100006394713', date: dd2 },
  // 此示例对应从添加购物车抢购流程, 目前就根据设置了 areaId
  // areaId 获取在第5步骤中
  { skuId: '100011621642', date: dd2, areaId: `2_2825_51936` },
];
// 要强制登录
// 因为 x东 24小时就要重新登录, 防止运行时登录状态有效
// 定时抢购时 cookie 过期了就尴尬了
// true 强制扫码不实用cookie, 登录过后频繁重启时记得关闭
// 否则一直要扫码
exports.forceLogin = false;
```

5 确定流程是 *预约-购物车-订单详情* 这种,否则忽略这步骤.还要配置地区 `areaId`. 请打开项目目录下 `area/你所在省份`,找到你所在地区 id 复制配置到 `tasks-pool.js` 中。 如 `area/2.上海.txt`, `'崇明县/东平镇:'2_2919_50783',` 对应 `'2_2919_50783'`, 按照上面示范填入`tasks-pool.js`文件中  

6 当前目录下执行 `node index`  

7 扫描终端中的二维码登录, 24 小时之内重启不需要再次登录, `cookie` 串会保留在本地文件中 `cookie.json` 中。过期的话必须重新扫码

## Todo
- [x] 针对从购物车提交订单流程。如果此商品已经在购物车中, 则直接抢购不需要执行添加购物车操作了  



## Notice

- *购物车提交订单* 流程的话,目前如果预约商品已经在购物车中了, 请先移除!!(目前针对显卡)
- 对 `windows` 系统不友好, 有问题反馈
- 目前最好每天上 jd, 去找商品, 距离开始前十几分钟启动
- 预约每个人都很容易拿到, 不用使用脚本执行预约, 但是不太需要, 而且没有加入定时
- 这是 node 版本, 不熟悉的可使用 python 版本.(本版本借鉴使用了下面部分功能资源)
- https://github.com/zhou-xiaojun/jd_mask
- https://github.com/tychxn/jd-assistant
- 功能大同小异, 我根据自身需求加了在终端中扫码, 多进程抢不同商品
- 关于 jd 口罩问题, 发现和地区有很大关系, 有的地区根本不会抢到(太久了没有在意口罩了把😄)
- 关于上面的问题, 几个类似库 issues 都有讨论
- https://github.com/zhou-xiaojun/jd_mask/issues/1
- https://github.com/tychxn/jd-assistant/issues/108#issuecomment-592947377
- 发现真的地区差异很大，上海一次没有，朋友江苏连续俩次 ---3-19 日最新更新
- 注意使用最好提前个 `1s`, 因为提交订单前要请求其他俩个接口, 延迟疫苗差不多正好
- 每天最好提前做一次扫码，或者多提前一会开启脚本

## 成功案例

- 订单<img src="https://user-images.githubusercontent.com/13815865/77068940-6ee2a180-6a22-11ea-91a9-e174fdd7a96a.png" />
- 订单<img src="https://user-images.githubusercontent.com/13815865/77068877-56728700-6a22-11ea-8102-925cc25a4b92.png" />

更多案例: [issues/2](https://github.com/meooxx/jd_by_mask/issues/2)

## Advanced(废弃)

_解决不了 mac 待机状态, 代码不执行问题后执行很延后_

熟悉 `nodejs` 和 `golang` 使用。正常 `master` 版本已经满足实际使用了， 这部分使用说明不会很详细  
分支 `feture-golang` 新加了 `golang` 的版本。 跟 `master` 分支上的区别:
`master` 上面全部是 `nodejs`代码，实际使用发现在定时功能和`cookie` 在会话间储存不是很高效。正好略懂 `golang`, 就用 `golang` 把提交订单的部分重写了。
这个分支上面的流程, `nodejs` 负责登录状态维护, 包括登录流程 和 初始 `cookie` 储存。 `golang` 只做定时提交订单这部分流程。下面这段时间，测测实际效果。
完整流程 nodejs 启动, 监听本地 8888 端口在后台。 golang 启动, func init 中访问 nodejs http://127.0.0.1:8888/getCookies, 获得一系列 cookie 等。然后等待预约时间, 提交订单

![流程图片](https://github.com/meooxx/jd_by_mask/blob/master/diagram.svg)

