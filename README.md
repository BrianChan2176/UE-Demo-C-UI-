# UE5 C++ 联机射击计分 Demo（服务器权威）
这是一个基于 UE5 的玩法 Demo，采用 **蓝图 + C++ 混合开发**，
实现了计分、倒计时、结束流程，并完成了 **服务器权威的联机数据链路**，
用于验证 Gameplay Framework 与 UE 网络同步的工程化写法。

## 演示视频
30秒视频【UE联机服务器数据权威山上随机刷球靶场Demo（蓝图C++混合开发、服务器权威联机、事件驱动UI）】 https://www.bilibili.com/video/BV1oArYB9ELZ/?share_source=copy_web&vd_source=ac628cef45f9e52f29306cbd29b51d36

2分钟视频【权威网络数据链条+GameState网络数据广播+UI订阅】 https://www.bilibili.com/video/BV1RMrYBnESa/?share_source=copy_web&vd_source=ac628cef45f9e52f29306cbd29b51d36

## 玩法说明
联机限时射击靶场玩法：  在山地场景中随机刷球，玩家在限定时间内射击目标获得分数，时间结束后进入结算阶段。



功能列表（Feature Checklist）

✅ C++ 计分/倒计时/结束

✅ Server-authoritative（服务器权威）计分

✅ RPC 请求链路（Client→Server）

✅ RepNotify 同步数据（Score/TimeLeft/Phase）

✅ UI 事件订阅（GS 广播 → UI 刷新）


架构与职责

GameMode：规则判定 / 加分 / 生成销毁

GameState：复制数据（Score/TimeLeft/Phase）+ 事件广播

PlayerController：发送Server RPC请求

Spawner/Ball：在世界里被GameMode可复用生成与命中对象

UI：纯订阅显示（不直接改数据）



网络链路图：

一：Character做射线，检测被hitActor，如果==ball调用PlayerController发送Server RPC请求GameMode加分

二：PlayerController负责发送server RPC请求

三：GameMode负责检测加分条件，加分，摧毁球

四：GameState网络同步客户端得分，广播器广播分数，UI订阅广播器跟着变化



如何运行
UE版本5.6.1
如何启动监听服务器：开始旁边的三个点设置，选择PIE，玩家数量2，网络模式以监听服务器运行



踩过的坑：

一：UI和锚点
从单机到联机双窗口的时候，因为不懂得UI是根据锚点+偏移显示的，导致一直看不到客户端的倒计时UI和得分UI，那时候找错方向一直在找代码的问题，反反复复了很久非常痛苦，结果最后一拉开客户端窗口就看见了UI，然后就想哭。

二：网络PlayerController发ServerRPC，自己PlayerController.cpp执行了ServerRequestXXX_Implementation导致客户端本地执行而不是触发UE RPC机制。正常应该是应该调用.h的ServerRequestXXX，走RPC机制UE网络打包，让服务器端执行_Implementation。

三：打包之后不知道为什么用来很多cpu资源80%多，但是明明编辑器开PIE双窗口的时候都不用这么多资源，后来发现原来打包的默认开了光追，配置关掉就好了


下一步计划：
角色与动画（现在在做）
AI
