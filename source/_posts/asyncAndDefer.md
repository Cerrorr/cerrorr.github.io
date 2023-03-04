---
title: script标签中的async和defer
date:
cover: https://cdn.jsdelivr.net/gh/Cerrorr/Images@main/202303012143769.jpeg 
categories:
  - 重读JavaScript高级程序设计
tags:
  - JavaScript
---
> > - 作者：[乔珂力](https://juejin.cn/user/1961184474432830)
> - 链接：[图解script标签中的async和defer](https://juejin.cn/post/6894629999215640583)
> - 来源：[稀土掘金](https://juejin.cn)

在 HTML 中会遇到以下三类 script：

- <script src='xxx'></script>
- <script src='xxx' async></script>
- <script src='xxx' defer></script>

那么这三类 script 有什么区别呢？
<a name="Xb6zB"></a>
## script
浏览器在解析 HTML 的时候，如果遇到一个没有任何属性的 script 标签，就会暂停解析，先发送网络请求获取该 JS 脚本的代码内容，然后让 JS 引擎执行该代码，当代码执行完毕后恢复解析。整个过程如下图所示：<br />![](https://cdn.nlark.com/yuque/0/2023/webp/29584060/1677942129424-2a16b133-cc34-43e9-8d11-d549113f1d18.webp#averageHue=%23f4f2f2&clientId=u2a811950-2748-4&from=paste&id=uc475f225&originHeight=256&originWidth=1678&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u96cababb-2234-4a2a-bece-cf6294ca75f&title=)<br />可以看到，script 阻塞了浏览器对 HTML 的解析，如果获取 JS 脚本的网络请求迟迟得不到响应，或者 JS 脚本执行时间过长，都会导致白屏，用户看不到页面内容。
<a name="CPS1x"></a>
## async script
async 表示异步，例如七牛的源码中就有大量的 async 出现：<br />![](https://cdn.nlark.com/yuque/0/2023/webp/29584060/1677942129413-bb7d08e0-7860-4dbd-8eeb-85a6194e9210.webp#averageHue=%23f5faf5&clientId=u2a811950-2748-4&from=paste&id=u4527a5e7&originHeight=564&originWidth=2754&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u55d4323c-bee1-4f36-add4-4de55e283da&title=)当浏览器遇到带有 async 属性的 script 时，请求该脚本的网络请求是异步的，不会阻塞浏览器解析 HTML，一旦网络请求回来之后，如果此时 HTML 还没有解析完，浏览器会暂停解析，先让 JS 引擎执行代码，执行完毕后再进行解析，图示如下：<br />![](https://cdn.nlark.com/yuque/0/2023/webp/29584060/1677942129575-c72d6460-a494-4a95-9d17-80324f1d0489.webp#averageHue=%23f6f4f4&clientId=u2a811950-2748-4&from=paste&id=ufd2eab84&originHeight=256&originWidth=1678&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u1b33789f-0db8-42b0-90db-da48320c95d&title=)<br />当然，如果在 JS 脚本请求回来之前，HTML 已经解析完毕了，那就啥事没有，立即执行 JS 代码，如下图所示：<br />![](https://cdn.nlark.com/yuque/0/2023/webp/29584060/1677942129436-a0d741ab-58e8-45f6-8ed4-9106a05b9aa2.webp#averageHue=%23f8f6f6&clientId=u2a811950-2748-4&from=paste&id=u3dd39152&originHeight=256&originWidth=1679&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=ua82cf2ee-9bfb-4eca-b0a1-fa5cbd9b642&title=)<br />所以 async 是不可控的，因为执行时间不确定，你如果在异步 JS 脚本中获取某个 DOM 元素，有可能获取到也有可能获取不到。而且如果存在多个 async 的时候，它们之间的执行顺序也不确定，完全依赖于网络传输结果，谁先到执行谁。
<a name="n2l9i"></a>
## defer script
defer 表示延迟，例如掘金的源码中就有大量的 defer 出现：<br />![](https://cdn.nlark.com/yuque/0/2023/webp/29584060/1677942129448-bdf85e3e-d603-413b-98ca-4e4e21f4aa35.webp#averageHue=%23eeeae2&clientId=u2a811950-2748-4&from=paste&id=ub72e14c0&originHeight=482&originWidth=2772&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=uab81edfd-dc4b-46e4-935a-d23beac64af&title=)<br />当浏览器遇到带有 defer 属性的 script 时，获取该脚本的网络请求也是异步的，不会阻塞浏览器解析 HTML，一旦网络请求回来之后，如果此时 HTML 还没有解析完，浏览器不会暂停解析并执行 JS 代码，而是等待 HTML 解析完毕再执行 JS 代码，图示如下：<br />![](https://cdn.nlark.com/yuque/0/2023/webp/29584060/1677942129857-9ea56f2a-69c5-4fa4-a517-484363ecedd5.webp#averageHue=%23bb908d&clientId=u2a811950-2748-4&from=paste&id=uccfa0bc0&originHeight=256&originWidth=1678&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u7b204583-9f55-4623-a3b4-6b2ff2646f5&title=)<br />如果存在多个 defer script 标签，浏览器（IE9及以下除外）会保证它们按照在 HTML 中出现的顺序执行，不会破坏 JS 脚本之间的依赖关系。<br />最后，根据上面的分析，不同类型 script 的执行顺序及其是否阻塞解析 HTML 总结如下：

| script 标签 | JS 执行顺序 | 是否阻塞解析 HTML |
| --- | --- | --- |
| <script> | 在 HTML 中的顺序 | 阻塞 |
| <script async> | 网络请求返回顺序 | 可能阻塞，也可能不阻塞 |
| <script defer> | 在 HTML 中的顺序 | 不阻塞 |
