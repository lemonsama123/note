---  * [http://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&amp;mid=2247527946&amp;idx=1&amp;sn=4aa278c00af93395ac3e9efaeeb064a7&amp;chksm=e9c289fddeb500ebe6cb3e6d0fe597bda11dce56a8f1966712bb3f77cb14cb57153d9c68824f&amp;mpshare=1&amp;scene=1&amp;srcid=1027zcjFusthsXjXtTDoi9jr&amp;sharer_sharetime=1666858219701&amp;sharer_shareid=11c46305be36f8c23bd557e8c7bec8c9#rd - 微信公众平台](http://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527946&idx=1&sn=4aa278c00af93395ac3e9efaeeb064a7&chksm=e9c289fddeb500ebe6cb3e6d0fe597bda11dce56a8f1966712bb3f77cb14cb57153d9c68824f&mpshare=1&scene=1&srcid=1027zcjFusthsXjXtTDoi9jr&sharer_sharetime=1666858219701&sharer_shareid=11c46305be36f8c23bd557e8c7bec8c9#rd) * 小心不要掉进面试官的陷阱 * 2022-10-27 16:10:32  ---  话说，**UDP比TCP快吗？**

相信就算不是八股文老手，也会下意识的脱口而出："​**是**​"。

这要追问为什么，估计大家也能说出个大概。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtRkkINoDH17SfFbicIfRcPaQianOLmgEzqdGQtKFOZchlrXic2PAOLDGFA/640?wx_fmt=png)​

但这也让人好奇，**用UDP就一定比用TCP快吗？什么情况下用UDP会比用TCP慢？**

我们今天就来聊下这个话题。

### 使用socket进行数据传输

作为一个程序员，假设我们需要在A电脑的进程发一段数据到B电脑的进程，我们一般会在代码里使用socket进行编程。

socket就像是一个​**电话或者邮箱**​（邮政的信箱）。当你想要发送消息的时候，拨通电话或者将信息塞到邮箱里，socket内核会自动完成将数据传给对方的这个过程。

基于socket我们可以选择使用TCP或UDP协议进行通信。

对于TCP这样的可靠性协议，每次消息发出后都能明确知道对方收没收到，就​**像打电话一样**​，只要"喂喂"两下就能知道对方有没有在听。

而UDP就像是​**给邮政的信箱寄信一样**​，你寄出去的信，根本就不知道对方有没有正常收到，丢了也是有可能的。

> 这让我想起了大概17年前，当时还没有现在这么发达的网购，想买一本《掌机迷》杂志，还得往信封里塞钱，然后一等就是一个月，好几次都怀疑信是不是丢了。我至今印象深刻，因为那是我和我哥攒了好久的钱。。。

回到socket编程的话题上。

创建socket的方式就像下面这样。

```
fd = socket(AF_INET, 具体协议,0);
```

注意上面的"​**具体协议**​"，如果传入的是`SOCK_STREAM`​，是指使用**字节流**传输数据，说白了就是​**TCP协议**​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPt7uO5QmjOofm42CWfCRzCuCALSynBjibnFVo0DZ3msiaC8Crrp54d5lPQ/640?wx_fmt=png "TCP是什么")  
TCP是什么

如果传入的是`SOCK_DGRAM`​，是指使用**数据报**传输数据，也就是​**UDP协议**​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtfoyPdym2a3JXsE9qCzjmzL9b27RDiaU7nCvia7sYrNCBrXsPLSicz6qJA/640?wx_fmt=png "UDP是什么")  
UDP是什么

返回的`fd`​是指socket句柄，可以理解为socket的​**身份证号**​。通过这个`fd`​你可以在内核中找到**唯一**的socket结构。

如果想要通过这个socket发消息，只需要操作这个fd就行了，比如执行 `send(fd, msg, ...)`​，内核就会通过这个fd句柄找到socket然后进行发数据的操作。

​**如果一切顺利**​，此时对方执行接收消息的操作，也就是 `recv(fd, msg, ...)`​，就能拿到你发的消息。

![](https://mmbiz.qpic.cn/mmbiz_gif/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtC4ZAP7M7C8MfCy5DxV04r2W0pGpQWfQFek0DcH9Q48ugX1icib8ib4Piag/640?wx_fmt=gif "udp发送接收过程")  
udp发送接收过程

### 对于异常情况的处理

**但如果不顺利呢？**

比如消息发到一半，丢包了呢?

> 丢包的原因有很多，之前写过的《用了TCP协议，就一定不会丢包吗？》有详细聊到过，这里就不再展开。

那UDP和TCP的态度就不太一样了。

UDP表示，"哦，是吗？然后呢？关我x事"

TCP态度就截然相反了，"啊？那可不行，是不是我发太快了呢？是不是链路太堵被别人影响到了呢？不过你放心，我肯定给你补发"

TCP老实人石锤了。我们来看下这个老实人在背后都默默做了哪些事情。

#### 重传机制

对于TCP，它会给发出的消息打上一个​**编号（sequence）**​，接收方收到后回一个​**确认(ack)**​。发送方可以通过`ack`​的数值知道接收方收到了哪些`sequence`​的包。

如果长时间等不到对方的确认，TCP就会重新发一次消息，这就是所谓的​**重传机制**​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtgURmoCtr5XrdIPSP2pdKEicM1lGZtFNG51BicQ8MibEpYKf4X2ZxKM8eg/640?wx_fmt=png "TCP重传")  
TCP重传

#### 流量控制机制

但重传这件事本身对性能影响是比较严重的，所以是​**下下策**​。

于是TCP就需要思考有没有办法可以尽量​**避免重传**​。

因为数据发送方和接收方处理数据能力可能不同，因此如果可以根据双方的能力去调整发送的数据量就好了，于是就有了​**发送和接收窗口**​，基本上从名字就能看出它的作用，比如**接收窗口的大小**就是指，接收方当前​**能接收的数据量大小**​，**发送窗口的大小**就指发送方当前能发的数据量大小。TCP根据窗口的大小去控制自己发送的数据量，这样就能大大减少丢包的概率。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtic4YibzVFgGGPTonRcuQLjxD4NgzCty89AmhyWycTtFuDdZR9tQpJExg/640?wx_fmt=png "流量控制机制")  
流量控制机制

#### 滑动窗口机制

接收方的接收到数据之后，会不断处理，​**处理能力也不是一成不变的**​，有时候处理的快些，那就可以收多点数据，处理的慢点那就希望对方能少发点数据。毕竟发多了就有可能处理不过来导致丢包，丢包会导致重传，这可是下下策。因此我们需要动态的去调节这个接收窗口的大小，于是就有了​**滑动窗口机制**​。

看到这里大家可能就有点迷了，**流量控制和滑动窗口机制貌似很像，它们之间是啥关系？**我总结一下。其实现在TCP是​**通过滑动窗口机制来实现流量控制机制的**​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtBGPmpXMuXFVOkM1JmpGCYxIfSg2FRXVEnU1lkLhmhicHlSPYPCROHKQ/640?wx_fmt=png "滑动窗口机制")  
滑动窗口机制

#### 拥塞控制机制

但这还不够，有时候发生丢包，​**并不是因为发送方和接收方的处理能力问题导致的**​。而是跟**网络环境**有关，大家可以将网络想象为一条公路。马路上可能堵满了别人家的车，只留下一辆车的空间。那就算你家有5辆车，目的地也正好有5个停车位，你也没办法同时全部一起上路。于是TCP希望能感知到外部的网络环境，根据网络环境及时调整自己的发包数量，比如马路只够两辆车跑，那我就只发两辆车。但外部环境这么复杂，TCP是怎么感知到的呢？

TCP会先慢慢试探的发数据，不断加码数据量，越发越多，先发一个，再发2个，4个…。直到出现丢包，这样TCP就知道现在当前网络大概吃得消几个包了，这既是所谓的​**拥塞控制机制**​。

不少人会疑惑流量控制和拥塞控制的关系。我这里小小的总结下。**流量控制**针对的是**单个连接**数据处理能力的控制，**拥塞控制**针对的是**整个网络环境**数据处理能力的控制。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtPD1aSJkicqxTXxLOtxKrFictOHDNpA6moTCZVHG9MmORsfqxWsv68iaLw/640?wx_fmt=png "1663598420295")  
1663598420295

#### 分段机制

但上面提到的都是怎么**降低**重传的概率，似乎重传这个事情就是无法避免的，**那如果确实发生了，有没有办法降低它带来的影响呢？**

有。当我们需要发送一个超大的数据包时，如果这个数据包丢了，那就得重传同样大的数据包。但如果我能将其分成一小段一小段，那就算真丢了，那我也就只需要重传那一小段就好了，大大减小了重传的压力，这就是TCP的​**分段机制**​。

而这个所谓的一小段的长度，在传输层叫​**MSS**​（​**Maximum Segment Size**​），数据包长度大于MSS则会分成N个小于等于MSS的包。

![](https://mmbiz.qpic.cn/mmbiz_gif/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtZr5Llwk6yHJybDHSe3oXC6N9HuUibhRhqCypQiamOuibMyyZkbUfb0GbA/640?wx_fmt=gif "MSS分包")  
MSS分包

而在网络层，如果数据包还大于​**MTU（Maximum Transmit Unit）**​，那还会继续分包。

![](https://mmbiz.qpic.cn/mmbiz_gif/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtmBbcwib8JKrk6ButEVJtibiapvuJDMcpuGtqc7eXPa0d7fibeB9HicBYUFA/640?wx_fmt=gif "MTU分包")  
MTU分包

一般情况下，`MSS=MTU-40Byte`​，所以​**TCP分段后，到了IP层大概率就不会再分片了**​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtJ21Bgcic5AHQCCGzy2XDIjWOKutbG2rPgYxc3l1SM7t3OLnQM3WCngA/640?wx_fmt=png "MSS和MTU的区别")  
MSS和MTU的区别

#### 乱序重排机制

既然数据包会被分段，链路又这么复杂还会丢包，那数据包乱序也就显得不奇怪了。比如发数据包1,2,3。1号数据包走了其他网络路径，2和3数据包先到，1数据包后到，于是数据包顺序就成了2,3,1。这一点TCP也考虑到了，依靠数据包的`sequence`​，接收方就能知道数据包的先后顺序。

后发的数据包先到是吧，那就先放到专门的**乱序队列**中，等数据都到齐后，重新整理好乱序队列的数据包顺序后再给到用户，这就是​**乱序重排机制**​。

![](https://mmbiz.qpic.cn/mmbiz_png/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtZkUCglibzYCSzpZUrt520W1vcUPmxGq1PwkP5C46PKTyXh3kiaPc0jYA/640?wx_fmt=png "乱序队列等待数据包的到来")  
乱序队列等待数据包的到来

#### 连接机制

前面提到，UDP是无连接的，而TCP是面向连接的。

这里提到的**连接**到底是啥？

TCP通过上面提到的各种机制实现了数据的可靠性。这些机制背后是通过一个个数据结构来实现的逻辑。而为了实现这套逻辑，操作系统内核需要在两端代码里维护一套复杂的状态机（三次握手，四次挥手，RST，closing等异常处理机制），​**这套状态机其实就是所谓的&quot;连接&quot;**​。这其实就是TCP的​**连接机制**​，而UDP用不上这套状态机，因此它是"无连接"的。

网络环境链路很长，还复杂，数据丢包是很常见的。

我们平常用TCP做各种数据传输，完全对这些事情无感知。

**哪有什么岁月静好，是TCP替你负重前行。**

这就是TCP三大特性"面向连接、可靠的、基于字节流"中"​**可靠**​"的含义。

不信你改用UDP试试，丢包那就是真丢了，丢到你怀疑人生。

### 用UDP就一定比用TCP快吗？

这时候UDP就不服了："​**正因为没有这些复杂的TCP可靠性机制，所以我很快啊**​"

嗯，这也是大部分人认为UDP比TCP快的原因。

**实际上大部分情况下也确实是这样的。这话没毛病。**

那问题就来了。

**有没有用了UDP但却比TCP慢的情况呢？**

其实也有。

在回答这个问题前，我需要先说下​**UDP的用途**​。

实际上，**大部分**人也不会尝试**直接拿裸udp**放到生产环境中去做项目。

那UDP的价值在哪？

在我看来，UDP的存在，本质是内核提供的一个​**最小网络传输功能**​。

很多时候，大家虽然号称自己用了UDP，但实际上都很**忌惮**它的丢包问题，所以大部分情况下都会在UDP的基础上做各种不同程度的**应用层**可靠性保证。比如王者农药用的`KCP`​，以及最近很火的`QUIC（HTTP3.0）`​，其实都​**在UDP的基础上做了重传逻辑**​，实现了一套**类似**TCP那样的可靠性机制。

教科书上最爱提UDP适合用于​**音视频传输**​，因为这些场景允许丢包。但其实也不是什么包都能丢的，比如重要的关键帧啥的，该重传还得重传。除此之外，还有一些​**乱序处理机制**​。举个例子吧。

打音视频电话的时候，你可能遇到过丢失中间某部分信息的情况，但应该从来没遇到过乱序的情况吧。

比如对方打网络电话给你，说了："​**我好想给 xx 来个点赞在看！**​"

这时候网络信号不好，你可能会听到"我….点赞在看"。

但却从来没遇到过"在看 xx 好想赞"这样的**乱序**场景吧？

所以说，​**虽然选择了使用UDP，但一般还是会在应用层上做一些重传机制的**​。

于是问题就来了，​**如果现在我需要传一个特别大的数据包**​。

在`TCP`​里，它内部会根据`MSS`​的大小​**分段**​，这时候进入到IP层之后，每个包大小都不会超过`MTU`​，因此IP层一般不会再进行分片。这时候发生丢包了，只需要**重传每个MSS分段**就够了。

![](https://mmbiz.qpic.cn/mmbiz_gif/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPtO1Ej7QaX5MkYaedW4hQQLp02e2m6B7S5gIiarlX26C8skfyMVVUa7lA/640?wx_fmt=gif "TCP分段")  
TCP分段

但对于`UDP`​，其本身并不会分段，如果数据过大，到了IP层，就会进行分片。此时发生丢包的话，再次重传，就会​**重传整个大数据包**​。

![](https://mmbiz.qpic.cn/mmbiz_gif/AnAgeMhDIianyhYJN5jcGIWlicWE0BlibPt08iaCLicTmZNZ8T4VxUwhpsFoGXhOoLVdiaOFcYJqXKdPhnChbSibBfUwA/640?wx_fmt=gif "UDP不分段")  
UDP不分段

对于上面这种情况，​**使用UDP就比TCP要慢**​。

当然，解决起来也不复杂。这里的关键点在于是否实现了数据分段机制，​**使用UDP的应用层如果也实现了分段机制的话，那就不会出现上述的问题了**​。

### 总结

* TCP为了实现可靠性，引入了重传机制、流量控制、滑动窗口、拥塞控制、分段以及乱序重排机制。而UDP则没有实现，因此一般来说TCP比UDP慢。
* TCP是面向连接的协议，而UDP是无连接的协议。这里的"​**连接**​"其实是，操作系统内核在两端代码里维护的一套复杂状态机。
* 大部分项目，会在基于UDP的基础上，模仿TCP，实现不同程度的可靠性机制。比如王者农药用的KCP其实就在基于UDP在应用层里实现了一套**重传**机制。
* 对于UDP+重传的场景，如果要传​**超大数据包**​，并且没有实现**分段机制**的话，那数据就会在IP层分片，一旦丢包，那就需要重传整个超大数据包。而TCP则不需要考虑这个，内部会自动分段，丢包重传分段就行了。这种场景下，其实TCP更快。

往期推荐

[我的学习小圈子](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247524980&idx=2&sn=9ddcdb6c52aa096ed4c5ad0ced946a7d&chksm=e9c28583deb50c95f3c2665713a8bbc372c68332b3bfb846cf4b23af3f1cc07164832a291335&scene=21#wechat_redirect)

[我滴项目又完成啦！！！](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527878&idx=1&sn=00ef3e8862cec2570eeb2e74a232c700&chksm=e9c28a31deb5032784071939437fba3fd6fdb810f0f849aa90536259d8d8bc25453328b86c12&scene=21#wechat_redirect)

[一些已经淘汰的 Java 技术，别再学了！](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527922&idx=1&sn=b9f1eba389be3804b3f8ba7f5f445ed6&chksm=e9c28a05deb5031352faa8fdc34436bbb3b022d3bb06db384c9eeba89d8f7752a2c4dd1495b4&scene=21#wechat_redirect)

[我做了一款生成代码+数据的神器！](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527409&idx=1&sn=a9290a4afed64915dbdce772c43317ad&chksm=e9c28c06deb505109befb162ba647c19a895fa7378df931325102bec016d9e2bcd58d055bdf5&scene=21#wechat_redirect)

[2022 年，前端都在用什么开发？](https://mp.weixin.qq.com/s?__biz=MzI1NDczNTAwMA==&mid=2247527742&idx=2&sn=813a3b8418a521c3ae123868691685f8&chksm=e9c28ac9deb503df099fd932049f7b0aade46b4b1d1cba4faba2f4d7a24236b887efc1a777e0&scene=21#wechat_redirect)

######
