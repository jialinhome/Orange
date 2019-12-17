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

我相信我爱你
蒙上眼手交给你
慢慢的安心在黑暗中
共有一双眼睛
我要不断地爱你
不断拼凑了自己
生命中所有好不好的过去
仿佛都在等我遇见你


