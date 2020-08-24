# 2019-12-12

## [Chrome DevTools调试小技巧](https://zhuanlan.zhihu.com/p/42059158)

刚才看到了个不错的浏览器调试小技巧，主要包括以下几点：

1. 将选中元素滚动到视口内
2. 将请求保存为一个fetch请求，可以copy到console面板打印
3. 阻塞一个请求
4. DOM断点调试
5. 截屏(cmd + shift + p）

## [0.1+0.2=0.30000000000000004?](https://juejin.im/post/5cf667b6f265da1bc23f6536)

javascript的number类型在计算机上采用双精度格式存储，占用64个比特位，分别是1个符号位、11个指数为和52个尾数位。这里就存在一个越界问题，也就是说如果数字的二进制形式超过了52位，计算机就会采用一定的策略进行截取，以致数字丢失了精度。

这里，0.1与0.2的二进制表示都是无限的，所以计算机会进行截取，导致失真。

## [JSON.stringify()](https://juejin.im/post/5decf09de51d45584d238319?utm_source=gold_browser_extension)

平时用的`JSON.stringify()`其实有很多细节需要注意，比如当`undefined`、`Symbol`以及`function`为value值的情况下，序列化的结果如何表现，这个要是没接触过肯定不会知道。

```javascript
const data = {
  a: "aaa",
  b: undefined,
  c: Symbol("dd"),
  fn: function() {
    return true;
  },
  d: "ddd"
};
JSON.stringify(data); // 输出：？
// "{"a":"aaa","d":"ddd"}"

JSON.stringify(["aaa", undefined, function aa() {
    return true
  }, Symbol('dd'),"eee"])  // 输出：？

// "["aaa",null,null,null,"eee"]"
```

通过上面的代码，可以看出端倪。

# 2019-12-13

## 尝试系统中引入react-hot-loader，以失败告终😿

本打算引入react-hot-loader，但是没有生效，试了很久，初步发现可能是引入顺序的问题，因为dll会先于react-hot-loader引入，而且react-hot-loader也快要被废弃了，索性就不引用了吧

## [script error 复现](https://juejin.im/post/5df3522751882512302db3ca?utm_source=gold_browser_extension)

1. 录制视频

a) canvas 截取图片，该方法的实现思路是利用 canvas 将网页生成图片，然后缓存起来，为了使得生成的视频流畅，我们一秒中需要生成大约 25 帧，也就是需要 25 张截图，然后在出现 script error 时将缓存起来的页面图片进行上报，再在分析系统通过技术将页面浏览进行还原。

b) 该方法主要是记录用户页面 dom 的变化，然后在出现 script error 时将对应的记录进行上报，然后在分析系统里通过技术将页面还原。

> 1. 进入页面，生成页面的虚拟dom全量快照；
> 2. 运用 API：MutationObserver，记录用户变化的 dom，同时记录用户的一些行操作（click，select，input，scroll 等事件）；
> 3. 当出现 script error 时将对应快照信息上报；
> 4. 在分析系统中将快照与用用户的操作还原。

# 2019-12-17

## 雨人

![](http://m.imeitou.com/uploads/allimg/2019071518/y4gvnjovviz.jpg)

我相信我爱你

蒙上眼手交给你

慢慢的安心在黑暗中

共有一双眼睛

我要不断地爱你

不断拼凑了自己

生命中所有好不好的过去

仿佛都在等我遇见你

## 俄罗斯套娃

刚才看Promise的实现原理，突然联想到俄罗斯套娃，这个Promise不就是一个俄罗斯套娃么，哈哈哈，一个套一个，最后还是那个娃，哎呀呀，哈哈哈。

俄罗斯套娃的实现：

```javascript
function isFunction(fn) {
  return typeof fn === 'function';
}

const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

function Promise(executor) {
  const that = this;
  that.val = undefined;
  that.err = undefined;
  that.status = PENDING;
  that.fulfilledCallbacks = []; // 存放then方法注册的回调函数
  that.rejectedCallbacks = []; // 存放then方法注册的回调函数

  function resolve(val) {
    if (that.status === PENDING) { // 一旦状态改变，就不会再变
      // 用异步的方式调用，确保晚于then函数执行，同时可以确保即便是在execotor中同步调用resolve，promise还是异步的
      setTimeout(function () {
        that.val = val;
        that.status = FULFILLED;
        that.fulfilledCallbacks.forEach(function (fun) {
          fun(val);
        });
      }, 0);
    }
  }

  function reject(err) {
    if (that.status === PENDING) { // 一旦状态改变，就不会再变
      setTimeout(function () {
        that.val = err;
        that.status = REJECTED;
        that.rejectedCallbacks.forEach(function (fun) {
          fun(err)
        })
      });
    }
  }
  
  executor(resolve, reject);
}

Promise.prototype.then = function (onFulfilled, onRejected) {
  const that = this;
  let promise2;

  onFulfilled = isFunction(onFulfilled) ? onFulfilled : function (value) {
    return value
  };
  
  onRejected = isFunction(onRejected) ? onRejected : function (err) {
    throw err;
  };

  promise2 = new Promise(function (resolve, reject) {
    that.fulfilledCallbacks.push(function (value) {
      try {
        let x = onFulfilled(value);
        resolve(x);
      } catch (e) {
        reject(e);
      }
    });
    
    that.rejectedCallbacks.push(function (err) {
      try {
        let x = onRejected(err);
        resolve(x);
      } catch (e) {
        reject(e);
      }
    });
  });

  return promise2;
};
```

# 2019-12-26

## [家用路由器中间人劫持](https://www.cert.pl/en/large-scale-dns-redirection-on-home-routers-for-financial-theft/)

刚才看了有关路由器DNS劫持中间人攻击相关原理，在此记录：

![](https://www.cert.pl/wp-content/uploads/2014/02/2014-02-mitr-good.png)

正常：
(1). Router requests IP address of bank’s website. Gets bank’s server’s address in reponse.
(2). User connects to bank’s server.		

被劫持：
(1). Router requests IP address of bank’s website. Gets malicious server’s address in response.
(2). User connects to malicious server.
(3). Malicious server connects to bank’s server.

## [package-lock.json](https://www.zhihu.com/question/62331583)

**查阅资料得知，自npm 5.0版本发布以来，npm i的规则发生了三次变化。**

1、npm 5.0.x 版本，不管package.json怎么变，npm i 时都会根据lock文件下载package-lock.json file not updated after package.json file is changed · Issue #16866 · npm/npm这个 issue 控诉了这个问题，明明手动改了package.json，为啥不给我升级包！然后就导致了5.1.0的问题...

2、5.1.0版本后 npm install 会无视lock文件 去下载最新的npm 然后有人提了这个issue why is package-lock being ignored? · Issue #17979 · npm/npm控诉这个问题，最后演变成5.4.2版本后的规则。

3、5.4.2版本后  why is package-lock being ignored? · Issue #17979 · npm/npm大致意思是，如果改了package.json，且package.json和lock文件不同，那么执行`npm i`时npm会根据package中的版本号以及语义含义去下载最新的包，并更新至lock。如果两者是同一状态，那么执行`npm i `都会根据lock下载，不会理会package实际包的版本是否有新。

# 2019-12-27

## [package.json中 ~ 与 ^](https://yarn.bootcss.com/docs/dependency-versions/)

语义版本控制（Semantic Versioning）

版本可以表示为`major.minor.patch`这样的形式，用数字表示形如`1.2.3`这种，`major.minor.patch`都有哪些含义呢？

major：当对包的API进行重大更改或不兼容更改时，递增major
minor: 增加了新功能同时保持向后兼容的能力时，递增mior
patch: 修改了bug同时保持向后兼容能力时，递增patch

* 波浪符（~）范围

在指定的minor版本中使用〜可以更改补丁
仅在指定主要版本的情况下使用〜，将允许进行minor的更改。

~3.1.4 --> >=3.1.4 <3.2.0

~3.1	 --> 3.1.x or >=3.1.0 <3.2.0

~3     --> 3.x or >=3.0.0 <4.0.0


* 脱字符（^）范围

版本的第一个非零数字不可修改

^3.1.4 --> >=3.1.4 <4.0.0

^0.4.2 --> >=0.4.2 <0.5.0

^0.0.2 --> >=0.0.2 <0.0.3
