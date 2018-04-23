﻿# vue-music

> Vue全家桶音乐播放器

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report

# 切换移动端调试 刷新后滚动正常
```

## 预览
![此处输入图片的描述][1]
![此处输入图片的描述][2]
![此处输入图片的描述][3]

## 目录结构
```
    .
├── build                                       // webpack配置文件
├── config                                      // 项目打包路径
├── src                                         // 项目核心文件
│   ├── api                                     // 数据抓取API
|       └── config                              // 请求公共配置
|       └── getLyric                            // 歌词抓取
|       └── handlesongurl                       // 歌曲url处理
|       └── rank                                // 排行榜数据抓取
|       └── recommend                           // 推荐数据抓取
|       └── search                              // 搜索数据抓取
|       └── singer                              // 歌手数据抓取
│   ├── base                                    // 基础组件
|       └── confirm                             // 对话框组件
|       └── listview                            // 歌手页面子组件
|       └── loading                             // 加载中样式展示组件
|       └── no-result                           // 无内容样式展示组件
|       └── progress-bar                        // 播放器进度条
|       └── progress-circle                     // 迷你播放器圆环进度条
|       └── scroll                              // 页面滚动组件
|       └── search-box                          // 搜索框组件
|       └── search-list                         // 历史搜索记录列表
|       └── slide                               // 轮播图组件
|       └── song-list                           // 歌曲列表子组件
|       └── switches                            // 选项卡按钮
|       └── top-tip                             // 头部通知框
│   ├── common                                  // 公共静态资源
|       └── fonts                               // 字体图标
|       └── image                               // 静态图片资源
|       └── js                                  // 公共js
|           └── cache                           // localstorage相关
|           └── config                          // 公共配置文件
|           └── dom                             // Dom相关方法
|           └── jsonp                           // promise版本jsonp封装
|           └── mixin                           // 组件mixin
|           └── singFactory                     // 处理歌手格式
|           └── songFactory                     // 处理歌曲格式
|           └── utils                           // js工具库
|       └── less                                // 公共less
│   ├── components                              // 业务组件
|       └── add-song                            // 播放列表添加歌曲组件
|       └── disc-detail                         // 歌单详情
|       └── m-header                            // 头部
|       └── music-list                          // 歌曲列表
|       └── player                              // 播放器
|       └── playlist                            // 播放列表
|       └── rank                                // 排行榜
|       └── recommend                           // 推荐
|       └── search                              // 搜索
|       └── singer                              // 歌手
|       └── singer-detail                       // 歌手详情
|       └── suggest                             // 搜素结果
|       └── tab                                 // 导航
|       └── top-detail                          // 排行榜详情
|       └── user-center                         // 用户中心
|   ├── router                                  // 路由
|   ├── store                                   // vuex
|       └── actions                             // actions
|       └── getters                             // getters
|       └── index                               // store入口
|       └── mutation-types                      // mutation-types
|       └── mutations                           // mutations
|       └── state                               // state
│   ├── App.vue                                 // 组件入口
│   ├── main.js                                 // 入口文件
├── index.html                                  // 模板html文件
├── prod.server.js                              // 测试打包后的服务器
.

```
### 总结
#### 准备工作
*. icomoon制作字体图标
*. 基础less
> a. 颜色规范
> b. mixin
> c. reset
> d. icon
      
*. eslint规则改写
*. babel-runtime babel-ployfill  // es6一些API polyfill

*. fastclick
```
    import fastclick from 'fastclick'; // 消除移动端点击延迟
    fastclick.attach(document.body);
```
*. vue-lazyload // 图片懒加载
```
    Vue.use(VueLazyLoad, {
      loading: require('common/image/default.png')
    });
```
*. 目录结构
> src => api base common components router store

*. webpack配置别名路径
```
extensions: ['.js', '.vue', '.json', '.less'],
    alias: {
      '@': resolve('src'),
      'common': resolve('src/common'),
      'components': resolve('src/components'),
      'api': resolve('src/api'),
      'base': resolve('src/base')
    }
```

#### 知识点
*. slider基础组件封装
```
// 基于better-scroll
/*
    props: 
      1.loop 是否无缝
      2.autoPlay: 是否自动播放
      3.interval: 播放间隔
    slot: 幻灯片内容列表
    实现:
      1.初始化slide-item宽度和外层容器宽度,并加上组件内写好的slide-item类名
      2.初始化better-scroll
      3.监听scrollEnd通过this.slider.getCurrentPage().pageX 获取当前索引
      4.自动播放实现: scrollEnd时开始setimeout beforeScrollStart时清除定时器
      5.组件初始化：mouted时初始化 resize时 重新计算宽度 this.slider.refresh()
*/
```
*. jsonp Promise版封装
```
    import originJsonp from 'jsonp'
    export function jsonp(url, data, options) {
        url = (url.indexOf('?') < 0 ? '?' : '') + params(data)
        return new Promise((resolve, reject) => {
            originJsonp(url, options, (err, data) => {
                if (err) {
                    reject(err)
                } else {
                    resolve(data)
                }
            })
        })
    }
    function params(data) {
     let ret = ''
     for (let k in data) {
        var value = data[k] === undefined ?  '' : data[k]
        ret +=  `&${k}=${encodeURIComponent(value)}`
     }
     return ret.substr(1)
    }
```
*.api处理
```
 // qq音乐某些接口可以直接通过jsonp获取
 // 某些接口做了限制，需要后端代理
 
 1.普通jsonp
    export function getRecommend() { // 获取幻灯片数据
        let url = 'https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg'
        const data = {
            g_tk: 1928093487,
            inCharset: 'utf-8',
            outCharset: 'utf-8',
            notice: 0,
            format: 'jsonp',
            platform: 'h5',
            uin: 0,
            needNewCode: 1
        }
        const options = {
            param: 'jsonpCallback'
        }
        return jsonp(url, data, options);
    }
 2.后端代理
    // 后端
    app.get('/api/getDiscList', (req, res) => { // 获取歌单信息
        const url = 'https://c.y.qq.com/splcloud/fcgi-bin/fcg_get_diss_by_tag.fcg';
        axios.get(url, {
            header: {
                referer: 'https://c.y.qq.com',
                host: 'c.y.qq.com'
            },
            params: req.query
        }).then((response) => {
            res.json(response.data)
        }).catch((err) => {
            console.log(err)
        })
    })
    
    // 前端
    export funtion getDiscList() {
        const data = {
            platform: 'yqq',
            hostUin: 0,
            sin: 0,
            ein: 29,
            sortId: 5,
            needNewCode: 0,
            categoryId: 10000000,
            rnd: Math.random(),
            format: 'json',
            g_tk: 1928093487,
            inCharset: 'utf-8',
            outCharset: 'utf-8',
            notice: 0
        }
        axios.get('/api/getDiscList', {
            params: data
        }).then((res) => {
            return Promise.resolve(res.data)
        })
    }
  
  3. qq音乐歌曲媒体url 需要 mid处理 然后拼成
  // 前端
  // url_mid获取
  let _uid = ''
  function genUrlMid(mids, types) {
      function getUid() {
          if (_uid) {
            return _uid;
          }
          if (!_uid) {
            const t = (new Date).getUTCMilliseconds();
            _uid = '' + Math.round(2147483647 * Math.random()) * t % 1e10;
          }
          return _uid
      }
      const guid = getUid()
      return {
        module: 'vkey.GetVkeyServer',
        method: 'CgiGetVkey',
        param: {
          guid,
          songmid: mids,
          songtype: types,
          uin: '0',
          loginflag: 0,
          platform: '23'
        }
      }
  }
  // 参数mids:[song1.mid, song2.mid, song3.mid....] types[0, 0 ,0, ..song.length]
  
  // 获取purl
  const urlMid = genUrlMid(mids, types);
  const data = {
    g_tk: 5381,
    format: 'json',
    platform: 'h5',
    needNewCode: 1,
    uin: 0,
    inCharset: 'utf-8',
    outCharset: 'utf-8',
    notice: 0
  }
  axios.post('/api/getPurlUrl', {
    comm: data,
    url_mid: urlMid
  }).then((res) => {
    let infos =  res.url_mid.data.midurlinfo;
    songs.forEach((song, index) => {
      song.url = `http://dl.stream.qqmusic.qq.com/${info[index].purl}`; // 拼接真正有效的url
    })
  })
  
 // 后端
 app.post('/api/getPurlUrl', bodyParser.json(), function (req, res) {
    const url = 'https://u.y.qq.com/cgi-bin/musicu.fcg'
    axios.post(url, req.body, {
      headers: {
        referer: 'https://y.qq.com/',
        origin: 'https://y.qq.com',
        'Content-type': 'application/x-www-form-urlencoded'
      }
    }).then((response) => {
      res.json(response.data)
    }).catch((e) => {
      console.log(e)
    })
  })
```

  [1]: https://ws1.sinaimg.cn/large/e8323205gy1fqkjptsymkg20qk0hkx6p.jpg
  [2]: https://ws1.sinaimg.cn/large/e8323205gy1fqkknfftvog20qi0hkx6s.jpg
  [3]: https://ws1.sinaimg.cn/large/e8323205gy1fqkkiz79q2g20qi0hkb2a.jpg