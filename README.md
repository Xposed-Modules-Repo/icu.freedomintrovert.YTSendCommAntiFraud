# YouTube发评反诈
## 为何有此模块？
本人之前开发了一个模块，叫“[哔哩发评反诈](https://github.com/freedom-introvert/biliSendCommAntifraud)”，用于反制中国大陆视频网站哔哩哔哩的阿瓦隆控评系统，自动检查评论是否正常还是仅自己可见、秒删。  
但我在使用YouTube的过程中，发现给某视频主的提问一直未得到回复，打开无痕模式，我的评论竟消失在此用于参照的上下两条评论之间！

这熟悉的味道，不就是ShadowBan吗？！哔哩哔哩阿瓦隆一样的作风啊！仅自己可见！！我印象中还有一分钟后直接删除的，就在我回复给某位要我联系方式的时候，发出去后一分钟后自己也看不到评论了。更牛逼的是：即使我用更隐晦的表达方式，如“tg与我此用户名一致”都能识别出来并且在一分钟后删除，最终我再怎么修改评论也无法联系哪位问我问题的人。这真是AI审查了吧，遥遥领先B站！

## 评论症状

- **Shadow Ban（仅自己可见）**
  - 发送提示成功，并正常出现在自己账号下的评论列表里，不出现无账号或其他账号下的评论列表，简称“仅自己可见”
  - 一般会出现在视频发布者的”待审核“评论列表里，视频作者点击“批准”后可正常显示，此处的评论将在 60 天后移除
  - 这个系统牛逼到会Shadow Ban视频作者发布的评论。且不会出现在“待审核”里，然后闹地狱笑话，您只能删除修改重发
  - 视频发布者对视频关闭藏得很深的“可能含有不当内容的评论需经审核才能发布”选项，这样就可以让观众自由说话了……吗？
    - 即使你的评论让视频发布者在待审核列表里通过了，它也只能出现在排序方式为最新的评论列表里，热门评论列表不会出现你的评论！！
    - 若关闭了需经审核才能发布，照以上规则，还只能出现在最新排序的评论列表里，不会出现在热门里！且检查起来更加复杂，开无痕然后按时间排序查找的方法无效，因为会找到评论，不能显示的是热门，你可能要找遍整个热门排序的列表。
- **被系统删除**
  - 发送依旧提示成功，然后正常出现在自己账号下的评论列表里。但你等待1-2分钟后刷新网页，那条评论将会消失
  - 被系统删除后，不刷新网页，点击修改评论，提交会报错：“未能发布评论。”，修改评论请求HTTP状态码：404
  - 不会出现在视频发布者的“待审核”评论列表里，没有办法使其正常显示


## 适配版本
在模块未适配新版本时，请勿更新YouTube，这会导致模块失效！！因为要hook的混淆类名几乎每发布一个版本就会改变  
### 已适配（保证能用）
- 官版&[ReVanced Extended](https://revancedextended.com/)
    - 18.32.36(1539433920)
    - 18.40.33(1540476352)
    - 18.46.43(1541283264)
## 小功能
### 分享URL去跟踪 
对视频按分享所生成的URL，例如`https://youtu.be/1Q-5eIBfBDQ?si=XXxxxxxxxX_xx_xX`真正有用的是`https://youtu.be/1Q-5eIBfBDQ`，后面的`?si=XXxxxxxxxX_xx_xX`为跟踪参数，用于区分谁分享的链接。开启此功能后自动删除后尾的跟踪参数，保护隐私。
### 去除打开链接重定向
你在YouTube点击某蓝色可点击链接，如`https://u.jd.com`打开后跳转浏览器，浏览器地址栏显示的却是`https://www.youtube.com/redirect?event=backstage_event&redir_token=XXX......XXX&q=https://u.jd.com/&html_redirect=1`，然后才跳转目标网址。BYD！这个“redir_token”是干嘛的心里有点逼数，为了收集隐私强加一个重定向链接，还他妈拖慢速度，梯子慢一点直接打不开！开启此功能将把“/redirect”重定向链接转为你本要打开的链接，避免拖满打开速度以及被恶意收集隐私。
## 检查逻辑
### 说明

由于YouTube用上了遥遥领先的AI审查，所以反射弧比哔哩哔哩长上25倍（审查所浪费的电也是b站的10坤倍🙃）！哔哩哔哩审核一条评论1秒左右，而YouTube要25秒左右！！检查过程及其漫长，因此默认的等待时间是15秒。引入了重找机制，如果等待设定时间后未在无账号评论区找到，将等待设定时间后重新查找无账号下的评论区，若找到了则说明评论正常只是审核漫长，若没找到则继续等待一段时间后再查找，重复操作直到设定的上限，若到了上限还未找到，则停止重找循环，评论基本上是被ShadowBan了。不过还有系统审核后删除的情况，因重找循环的漫长时间，系统可能已经把你评论删除了，所以要再次查找一次有账号的评论区，已判断是否被系统删除，没有就是ShadowBan。

当然，检查的时间是非常漫长的，即使评论正常也要等上15秒（默认），遇到ShadowBan的情况那还得多等5*6=30秒（默认）。**若你不关注查找过程，建议在等待的15秒里，点击“后台等待”，会转入通知进行等待。若15秒等待时间已结束，进入重找机制后将不可后台等待！**默认等待120秒，等待期间你可以做别的，等待完成后，将有一条通知，点击通知即可继续进行检查。因为等待时间充足（请设定超过一分钟的等待时间），将不会使用重找机制，若没找到将不会进行重找，直判定为ShadowBan，也不会再重检查是否被系统删除（等待时间够长且没有漫长的重找过程，前一轮的判断是否被系统删除就已是最终的结果）。若你不慎划掉了通知，又或者被杀后台，你可以去“待检查的评论”里找到你的评论进行检查。

**若无特殊说明，查找的评论区都以发布时间排序**

**目前仅支持视频的评论区，暂不支持评论回复的检查，以及帖子的评论区。**


### 流程图（若无法显示请到GitHub查看）

``` mermaid
graph TB
00["Get到评论发送信息"] --等待设定时间--> 10["在有账号状态下查找与发送返回的评论id相同的评论"] 
10 --没找到--> 20["评论被系统删除"]
10 --找到了--> 30["获取此评论下面的一条评论id作为锚点用于定位(如果有)"] --> 45["无账号状态下查找你的评论"]
45 --找到了--> 50["评论正常显示"]
45 --找到了锚点评论----> 60["停止翻页(因为你的评论总是在锚点评论之上)"] --> 85
45 -- "无锚点评论定位，翻到了评论区最后一页没有找到" --> 85
85{"是否到达重找次数上限？"} --"没有"--> 等待设定时间 --> 45
85 --已到上限--> 90["评论被shadowban？"]
90 --> 100["再次在有账号的评论区查找你的评论"]
100 -- 没找到 --> 评论被系统删除
100 -- 找到了 --> 评论被ShadowBan
```
## 翻译

本应用第一语言为简体中文，默认语言为英语，英语部分为机翻，若有误请到crowdin帮我修正！

当然此篇README也需要翻译，有好的翻译结果可以issues跟我说一声:)

### 协助我翻译

https://zh.crowdin.com/project/youtubesendcommentantifraud/


## 宣传
您可以在墙内外任何地方宣传此模块！若在墙内平台宣传，请勿提到我的另一个作品：哔哩发评反诈！
## 关于
GitHub项目主页：https://github.com/freedom-introvert/YouTubeSendCommentAntiFraud  

tg交流群zh(与哔哩发评反诈共用)：https://t.me/biliSendCommAntifraud  

tg交流群en(暂无）
