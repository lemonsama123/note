---

* [http://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&amp;mid=2247527715&amp;idx=2&amp;sn=fe893223e2f1c0a8655ae563e5717657&amp;chksm=e9c28ad4deb503c29896dec5a53519201265c604b1420997c2d0bd72dc172262f3f3de6d4930&amp;mpshare=1&amp;scene=1&amp;srcid=1017sFB8HtmySgPl6HiGx7ir&amp;sharer_sharetime=1665989116650&amp;sharer_shareid=11c46305be36f8c23bd557e8c7bec8c9#rd - 微信公众平台](http://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527715&idx=2&sn=fe893223e2f1c0a8655ae563e5717657&chksm=e9c28ad4deb503c29896dec5a53519201265c604b1420997c2d0bd72dc172262f3f3de6d4930&mpshare=1&scene=1&srcid=1017sFB8HtmySgPl6HiGx7ir&sharer_sharetime=1665989116650&sharer_shareid=11c46305be36f8c23bd557e8c7bec8c9#rd)
* websocket 协议原理深度解析！
* 2022-10-17 14:45:29

---

大家好，我是鱼皮。

平时我们打开网页，比如购物网站某宝。都是点一下 **列表商品** ，跳转一下网页就到了 **商品详情** 。

从HTTP协议的角度来看，就是点一下网页上的某个按钮， **前端发一次HTTP请求，网站返回一次HTTP响应** 。

这种由客户端主动请求，服务器响应的方式也满足大部分网页的功能场景。

但有没有发现，这种情况下，服务器从来就不会**主动**给客户端发一次消息。

就像你喜欢的女生从来不会主动找你一样。

但如果现在，你在刷网页的时候**右下角**突然弹出一个 **小广告** ，提示你【 **一个人在家偷偷才能玩哦** 】。

 **求知，好学，勤奋** ，这些刻在你DNA里的东西都动起来了。

你点开后发现。

长相平平无奇的古某提示你"道士9条狗，全服横着走"。

影帝某辉老师跟你说"系兄弟就来砍我"。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwfWIFPB96Go7zBq32apGiamxnya4nL1lgZC45T3RnSia2ZfPt36Bkxwcw/640?wx_fmt=png)​

来都来了，你就选了个角色进到了游戏界面里。

![](https://mmbiz.qpic.cn/mmbiz_jpg/AnAgeMhDIian44JhkLicH6Y000gliazulUwgGZMfEibWp092Sw6b0RGqvzLJOicGB4xnpSeNHfYjymEVyN3wFoiag1icw/640?wx_fmt=jpeg "创建角色页面")  
创建角色页面

这时候，上来就是一个小怪，从远处走来，然后疯狂拿木棒子抽你。

 **你全程没点任何一次鼠标** 。服务器就自动将怪物的移动数据和攻击数据源源不断发给你了。

这….太暖心了。

感动之余，问题就来了，

像这种 **看起来服务器主动发消息给客户端的场景** ，是怎么做到的？

在真正回答这个问题之前，我们先来聊下一些相关的知识背景。

### 使用HTTP不断轮询

其实问题的痛点在于，**怎么样才能在用户不做任何操作的情况下，网页能收到消息并发生变更。**

最常见的解决方案是，**网页的前端代码里不断定时发HTTP请求到服务器，服务器收到请求后给客户端响应消息。**

这其实时一种**伪**服务器推的形式。

它其实并不是服务器主动发消息到客户端，而是客户端自己不断偷偷请求服务器，只是用户无感知而已。

用这种方式的场景也有很多，最常见的就是 **扫码登录** 。

比如某信公众号平台，登录页面二维码出现之后，**前端**网页根本不知道用户扫没扫，于是不断去向**后端**服务器询问，看有没有人扫过这个码。而且是以大概1到2秒的间隔去不断发出请求，这样可以保证用户在扫码后能在1到2s内得到及时的反馈，不至于 **等太久** 。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUw8wX90LCMraEMcicTbsWN5NlEuK7fuCX95LzibBOUjMXhwOaNorLfuV6w/640?wx_fmt=png "使用HTTP定时轮询")  
使用HTTP定时轮询

但这样，会有两个比较明显的问题

* 当你打开F12页面时，你会发现满屏的HTTP请求。虽然很小，但这其实也消耗带宽，同时也会增加下游服务器的负担。
* 最坏情况下，用户在扫码后，需要等个1~2s，正好才触发下一次http请求，然后才跳转页面，用户会感到 **明显的卡顿** 。

使用起来的体验就是，二维码出现后，手机扫一扫，然后在手机上点个确认，这时候 **卡顿等个1~2s** ，页面才跳转。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwxiavD0AEPicw6A4USJibH27KZo2bJj7QmOTCNVgIEGwbL6eWVZzCGlm5w/640?wx_fmt=png "不断轮询查看是否有扫码")  
不断轮询查看是否有扫码

那么问题又来了，**有没有更好的解决方案？**

有，而且实现起来成本还非常低。

### 长轮询

我们知道，HTTP请求发出后，一般会给服务器留一定的时间做响应，比如3s，规定时间内没返回，就认为是超时。

如果我们的HTTP请求 **将超时设置的很大** ，比如30s，**在这30s内只要服务器收到了扫码请求，就立马返回给客户端网页。如果超时，那就立马发起下一次请求。**

这样就减少了HTTP请求的个数，并且由于大部分情况下，用户都会在某个30s的区间内做扫码操作，所以响应也是及时的。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwJlM61UwBmg51fVjrKic393gsGM6yickgQJJpZSroStyN3wosjMgvAIZA/640?wx_fmt=png "长轮询")  
长轮询

比如，某度云网盘就是这么干的。所以你会发现一扫码，手机上点个确认，电脑端网页就 **秒跳转** ，体验很好。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwiaDliaA3mPxiaNjmro3Brw0ELAibVmzELDIVcXIhc5ZxyOMG4E6wFBNFCQ/640?wx_fmt=png "长轮询的方式来替代")  
长轮询的方式来替代

真一举两得。

像这种发起一个请求，在较长时间内等待服务器响应的机制，就是所谓的 **长训轮机制** 。我们常用的消息队列RocketMQ中，消费者去取数据时，也用到了这种方式。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUw78nYzn8qtiba0NqIMdIfyHIItGmyI77ibVMuXDjfvoMx0KD2LzYXRgHA/640?wx_fmt=png "RocketMQ的消费者通过长轮询获取数据")  
RocketMQ的消费者通过长轮询获取数据

像这种，在用户不感知的情况下，服务器将数据推送给浏览器的技术，就是所谓的**服务器推送**技术，它还有个毫不沾边的英文名，**comet**技术，大家听过就好。

上面提到的两种解决方案，本质上，其实还是客户端主动去取数据。

对于像扫码登录这样的**简单场景**还能用用。

但如果是网页游戏呢，游戏一般会有大量的数据需要从服务器主动推送到客户端。

这就得说下**websocket**了。

### websocket是什么

我们知道TCP连接的两端， **同一时间里** ，**双方**都可以**主动**向对方发送数据。这就是所谓的 **全双工** 。

而现在使用最广泛的`HTTP1.1`​，也是基于TCP协议的， **同一时间里** ，客户端和服务器**只能有一方主动**发数据，这就是所谓的 **半双工** 。

也就是说，好好的全双工TCP，被HTTP用成了半双工。

为什么？

这是由于HTTP协议设计之初，考虑的是看看网页文本的场景，能做到 **客户端发起请求再由服务器响应** ，就够了，根本就没考虑网页游戏这种，客户端和服务器之间都要互相主动发大量数据的场景。

所以为了更好的支持这样的场景，我们需要另外一个 **基于TCP的新协议** 。

于是新的应用层协议**websocket**就被设计出来了。

大家别被这个名字给带偏了。虽然名字带了个socket，但其实socket和websocket之间，就跟雷峰和雷峰塔一样，二者接近 **毫无关系** 。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwvSbzUiajykET6jsrn7wNwHAyDtX1HeXPRMtiaTORIdwiallUyMXEIwINQ/640?wx_fmt=png "websocket在四层网络协议中的位置")  
websocket在四层网络协议中的位置

### 怎么建立websocket连接

我们平时刷网页，一般都是在浏览器上刷的，一会刷刷图文，这时候用的是 **HTTP协议** ，一会打开网页游戏，这时候就得切换成我们新介绍的 **websocket协议** 。

为了兼容这些使用场景。浏览器在**TCP三次握手**建立连接之后，都**统一使用HTTP协议**先进行一次通信。

* 如果此时是 **普通的HTTP请求** ，那后续双方就还是老样子继续用普通HTTP协议进行交互，这点没啥疑问。
* 如果这时候是 **想建立websocket连接** ，就会在HTTP请求里带上一些 **特殊的header头** 。

```
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Key: T2a6wZlAwhgQNqruZ2YUyg==\r\n
```

这些header头的意思是，浏览器想 **升级协议（Connection: Upgrade）** ，并且 **想升级成websocket协议（Upgrade: websocket）** 。

同时带上一段 **随机生成的base64码（Sec-WebSocket-Key）** ，发给服务器。

如果服务器正好支持升级成websocket协议。就会走websocket握手流程，同时根据客户端生成的base64码，用某个**公开的**算法变成另一段字符串，放在HTTP响应的 `Sec-WebSocket-Accept`​ 头里，同时带上`101状态码`​，发回给浏览器。

```
HTTP/1.1 101 Switching Protocols\r\n
Sec-WebSocket-Accept: iBJKv/ALIW2DobfoA4dmr3JHBCY=\r\n
Upgrade: websocket\r\n
Connection: Upgrade\r\n
```

> http状态码=200（正常响应）的情况，大家见得多了。101确实不常见，它其实是指 **协议切换** 。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwAibXe8g9qNfjv42WstJ8ibfRmlJDvW8MadaO1w6mFgqoWQsqicdiaWx9ZQ/640?wx_fmt=png "base64转为新的字符串")  
base64转为新的字符串

之后，浏览器也用同样的**公开算法**将`base64码`​转成另一段字符串，如果这段字符串跟服务器传回来的 **字符串一致** ，那验证通过。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUw2NBGkUkia0dzovG1lUaC5BH3U6PhUJrQ7ZAYgdKoSv1SNibictN5REicjg/640?wx_fmt=png "对比客户端和服务端生成的字符串")  
对比客户端和服务端生成的字符串

就这样经历了一来一回两次HTTP握手，websocket就建立完成了，后续双方就可以使用webscoket的数据格式进行通信了。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwVj9q8KHr7iaJaFHmCdniaCKCU7yvicRMYm5G1iaEYaQY1hWgibSR1mC75icA/640?wx_fmt=png "建立websocket连接.drawio")  
建立websocket连接.drawio

### websocket抓包

我们可以用wireshark抓个包，实际看下数据包的情况。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwyvaLdKXa7KQcEWseIfdrDjRN9ey2jkBspAibEQyzf8GUwPO0NU8mCHA/640?wx_fmt=png "客户端请求升级为websocket")  
客户端请求升级为websocket

上面这张图，注意画了红框的第`2445`​行报文，是websocket的 **第一次握手** ，意思是发起了一次带有`特殊Header`​的HTTP请求。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwr19Kt7Eus2axrlOM9tY5hqe20WHnIJ66LzTUJ2ib45Do5oenf6oicic3Q/640?wx_fmt=png "服务器同意升级为websocket协议")  
服务器同意升级为websocket协议

上面这个图里画了红框的`4714`​行报文，就是服务器在得到第一次握手后，响应的 **第二次握手** ，可以看到这也是个HTTP类型的报文，返回的状态码是101。同时可以看到返回的报文header中也带有各种`websocket`​相关的信息，比如`Sec-WebSocket-Accept`​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwzPl2Iq4q0LLiaaddg0jJP07mMhibzxwxfXckicp4p7MPtIsfAzZibyiaFQw/640?wx_fmt=png "两次HTTP请求之后正式使用websocket通信")  
两次HTTP请求之后正式使用websocket通信

上面这张图就是全貌了，从截图上的注释可以看出，websocket和HTTP一样都是基于TCP的协议。经历了三次TCP握手之后，利用HTTP协议升级为websocket协议。

你在网上可能会看到一种说法："websocket是基于HTTP的新协议"， **其实这并不对** ，因为websocket只有在建立连接时才用到了HTTP， **升级完成之后就跟HTTP没有任何关系了** 。

这就好像你喜欢的女生通过你要到了你大学室友的微信，然后他们自己就聊起来了。你能说这个女生是通过你去跟你室友沟通的吗？不能。你跟HTTP一样，都只是个 **工具人** 。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwjDyict3ZiaQftFKYjMXmdMb81I9GRzZWCic9MIH0RDYvIcMs8bRicKpPZg/640?wx_fmt=png "1663378093150")​

这就有点" **借壳生蛋** "的那意思。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwHzh3QQXyaoXg1pWC0hCzIXICicjYWeq5G8PgicQz5maJGuMQSYkVqk3Q/640?wx_fmt=png "HTTP和websocket的关系")  
HTTP和websocket的关系

### websocket的消息格式

上面提到在完成协议升级之后，两端就会用webscoket的数据格式进行通信。

数据包在websocket中被叫做 **帧** 。

我们来看下它的数据格式长什么样子。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwicjM75nZP0CG5176E0IkpnnmnsumpQcSH553RoiaVeJLlX1esxdalicvQ/640?wx_fmt=png "websocket报文格式")  
websocket报文格式

这里面字段很多，但我们只需要关注下面这几个。

 **opcode字段** ：这个是用来标志这是个**什么类型**的数据帧。比如。

* 等于1时是指text类型（`string`​）的数据包
* 等于2是二进制数据类型（`[]byte`​）的数据包
* 等于8是关闭连接的信号

 **payload字段** ：存放的是我们 **真正想要传输的数据的长度** ，单位是 **字节** 。比如你要发送的数据是`字符串"111"`​，那它的长度就是`3`​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwleRNWGS47w0P9USFrNia2ATyz8ic84hiccDlAR37SFnpYM9gqMwNQeAyg/640?wx_fmt=png)​

另外，可以看到，我们存放 **payload长度的字段有好几个** ，我们既可以用最前面的`7bit`​, 也可以用后面的`7+16bit或7+64bit。`​

那么问题就来了。

我们知道，在数据层面，大家都是01二进制流。我怎么知道**什么情况下应该读7bit，什么情况下应该读7+16bit呢？**

websocket会用最开始的7bit做标志位。不管接下来的数据有多大，都 **先读最先的7个bit** ，根据它的取值决定还要不要再读个16bit或64bit。

* 如果`最开始的7bit`​的值是 0~125，那么它就表示了  **payload 全部长度** ，只读最开始的`7个bit`​就完事了。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwIgcujojZ71jfjytl1IKCkZ1CpGw6OlsCVxicDpkqBZ9JL6uPA1kza5Q/640?wx_fmt=png "payload长度在0到125之间")  
payload长度在0到125之间

* 如果是`126（0x7E）`​。那它表示payload的长度范围在 `126~65535`​ 之间，接下来还需要 **再读16bit** 。这16bit会包含payload的真实长度。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUw2QaL8qoJFu7SkfJ3jWdHHvNC8f3sibWzc8OhSibe3fhRxGXqFXMTDKsA/640?wx_fmt=png "payload长度在126到65535之间")  
payload长度在126到65535之间

* 如果是`127（0x7F）`​。那它表示payload的长度范围`&gt;=65536`​，接下来还需要 **再读64bit** 。这64bit会包含payload的长度。这能放2的64次方byte的数据，换算一下好多个TB，肯定够用了。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUwW6pXTmaiaUIHtckfcECzvC9WRiadFA1vqibtXoickS4h5NXun9aSkaNibyQ/640?wx_fmt=png "payload长度大于等于65536的情况")  
payload长度大于等于65536的情况

 **payload data字段** ：这里存放的就是真正要传输的数据，在知道了上面的payload长度后，就可以根据这个值去截取对应的数据。

大家有没有发现一个小细节，websocket的数据格式也是  **数据头（内含payload长度） + payload data** 的形式。

![](https://mmbiz.qpic.cn/mmbiz_jpg/AnAgeMhDIian44JhkLicH6Y000gliazulUwaXBjE0DPSo0BiaaPTZF6wr9FTnWiaCemk3hqhcYicJPwdibfqfibpeo6OVg/640?wx_fmt=jpeg)​

TCP协议本身就是全双工，但直接使用**纯裸TCP**去传输数据，会有**粘包**的"问题"。为了解决这个问题，上层协议一般会用**消息头+消息体**的格式去重新包装要发的数据。

而**消息头**里一般含有 **消息体的长度** ，通过这个长度可以去截取真正的消息体。

HTTP协议和大部分RPC协议，以及我们今天介绍的websocket协议，都是这样设计的。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUw2Q58CXa52ykF5HPiaaUuia5NibIe7P797fpmeAA0Uw4pXqVbkLoV7EXmQ/640?wx_fmt=png "消息边界长度标志")  
消息边界长度标志

### websocket的使用场景

websocket完美继承了TCP协议的**全双工**能力，并且还贴心的提供了解决粘包的方案。它适用于**需要服务器和客户端（浏览器）频繁交互**的大部分场景。比如网页/小程序游戏，网页聊天室，以及一些类似飞书这样的网页协同办公软件。

回到文章开头的问题，在使用websocket协议的网页游戏里，怪物移动以及攻击玩家的行为是**服务器逻辑**产生的，对玩家产生的伤害等数据，都需要由 **服务器主动发送给客户端** ，客户端获得数据后展示对应的效果。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIian44JhkLicH6Y000gliazulUw9pfnTuFly3774icOB4COjQGOEAN7ErF5h7aWGzfgx6eTrRvSpKB2NCQ/640?wx_fmt=png "websocket的使用场景")  
websocket的使用场景

### 总结

* TCP协议本身是**全双工**的，但我们最常用的HTTP1.1，虽然是基于TCP的协议，但它是**半双工**的，对于大部分需要服务器主动推送数据到客户端的场景，都不太友好，因此我们需要使用支持全双工的websocket协议。
* 在HTTP1.1里。只要客户端不问，服务端就不答。基于这样的特点，对于登录页面这样的简单场景，可以使用**定时轮询或者长轮询**的方式实现 **服务器推送** (comet)的效果。
* 对于客户端和服务端之间需要频繁交互的复杂场景，比如网页游戏，都可以考虑使用websocket协议。
* websocket和socket几乎没有任何关系，只是叫法相似。
* 正因为各个浏览器都支持HTTP协议，所以websocket会先利用HTTP协议加上一些特殊的header头进行握手升级操作，升级成功后就跟HTTP没有任何关系了，之后就用websocket的数据格式进行收发数据。

最后，欢迎学编程的朋友们加入我的 [编程知识星球](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247524980&idx=2&sn=9ddcdb6c52aa096ed4c5ad0ced946a7d&chksm=e9c28583deb50c95f3c2665713a8bbc372c68332b3bfb846cf4b23af3f1cc07164832a291335&token=689599617&lang=zh_CN&scene=21#wechat_redirect)（访问 https://yupi.icu 了解详情），带你一起学编程、做项目、向鱼皮 1 对 1 提问、帮你做学习和求职的指导与规划、修改优化简历等。可以加我微信 yupi5927，备注【加入星球】和自己的情况领取优惠加入星球，不备注不通过，非诚勿扰谢谢。

![](https://mmbiz.qpic.cn/mmbiz_jpg/mngWTkJEOYKpAS5nlbOpcYYeuYdno4CiadIBn4iaggJDptQMF3z15nJCJmLUib30cGzHaR7fTEtiaQY7UELb9tqQtw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)​

往期推荐

[我的学习小圈子](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247524980&idx=2&sn=9ddcdb6c52aa096ed4c5ad0ced946a7d&chksm=e9c28583deb50c95f3c2665713a8bbc372c68332b3bfb846cf4b23af3f1cc07164832a291335&scene=21#wechat_redirect)

[我做了一款生成代码+数据的神器！](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527409&idx=1&sn=a9290a4afed64915dbdce772c43317ad&chksm=e9c28c06deb505109befb162ba647c19a895fa7378df931325102bec016d9e2bcd58d055bdf5&scene=21#wechat_redirect)

[学历技术比我好的人都没offer，很有压力！](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527325&idx=1&sn=96b8af9c8504bba2e601c5f787388cbe&chksm=e9c28c6adeb5057c07c4e67c5c01ba0f3a73901886ecc235d9d17156fd85ed3d8d1a534174d7&scene=21#wechat_redirect)

[几个优秀的开源项目，看看你用过几个？](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527371&idx=1&sn=357a8363fc5d6a64da2fba9715b0ab4a&chksm=e9c28c3cdeb5052a98dadcb418f9e758beb7e6f8efd29491a4062e8839f82e50e990fcec6317&scene=21#wechat_redirect)

[这些接口安全方案，大家都在用！](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527310&idx=2&sn=34cbccbdc86bf18dae3aa62fdadfe9b1&chksm=e9c28c79deb5056fb1d29ba02c2504cf0f20d410170f43b619a50d7ec55c0d5ae7343a10e065&scene=21#wechat_redirect)
