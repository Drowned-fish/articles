
# WebRTC

## 简介

> WebRTC (Web Real-Time Communication) 是一个可以用在视频聊天，音频聊天或[P2P](https://baike.baidu.com/item/%E7%82%B9%E5%AF%B9%E7%82%B9%E4%BC%A0%E8%BE%93/8491956?fr=aladdin)文件分享等Web App中的 API。

WebRTC是一项可以在浏览器或移动端中进行音频和视频交流的技术，它还可以进行P2P文件共享，实现在线视频会议等。

WebRTC 主要包括以下几部分:

+ getUserMedia
  获取设备的摄像头与(或)麦克风权限，并为此RTC连接接入设备的摄像头与(或)麦克风的信号。
+ RTCPeerConnection
  客户端之间传输音视频。
+ RTCDataChannel
  客户端之间传输数据。
 
 ## 信令
 
 WebRTC支持端到端通信，但是WebRTC还是需要服务器：
 
  + 对于客户端来说，用于交换元数据来协调通信，这被称为信令
  + 处理网络地址转换器（NetWork Address Translator, 简称NAT）和防火墙
 
 ### 什么是信令
 
 信令是协调通信的过程，为了能使WebRTC应用建立一个通信，客户端需要交换以下信息：
 
 + 会话控制消息用于打开或关闭通信 
 + 错误消息
 + 媒体元数据，如编解码器和编解码器设置，带宽和媒体类型 
 + 密钥数据，用于建立安全的连接 
 + 网络数据，如外界缩减的主机IP地址和端口 
 
 这个信令过程需要一个方式使客户来回地进行消息传递。这个机制不是由WebRTC API来实现的：你需要自己去搭建它。
 
 ### RTCPeerConnection
 
 RTCPeerConnection是WebRTC应用程序用来创建对等端连接并传输音视频的API。 
 
 初始化这个过程RTCPeerConnection有两个工作要做： 
 
 + 确定本地媒体条件，如分辨率和编解码器功能。这是用于提供和应答机制的元数据。 
 + 获取应用程序主机的潜在网络地址，成为候选项
 
 一旦确定了本地数据，就必须通过信令机制与远端对等端进行交换。 
 
  让我们假设一个场景：Alice正在尝试呼叫Eve。下面是完整的提供/应答机制：

         1. Alice创建一个RTCPeerConnection对象。

         2. Alice使用RTCPeerConnection createOffer()方法产生一个提供（一个SDP会话描述）。

         3. Alice用他的提供调用setLocalDescription()。

         4. Alice将提供串联起来，并使用信令机制将其发送给Eve。

         5. Eve用Alice的提供调用setRemoteDescription()，以便她的RTCPeerConnection知道Alice的设置。

         6. Eve调用createAnswer()，以及success callback函数传入本地会话描述：Eve的应答。

         7. Eve通过调用setLocalDescription()将其应答设置为本地描述。

         8. Eve然后使用信令机制将她的字符串化的应答发回给Alice。

         9. Alice使用setRemoteDescription()将Eve的应答设置为远程会话描述。

Alice和Eve也需要交换网络信息。“查找候选项”这个表达是指使用ICE框架查找网络接口和端口的过程。

         1. Alice使用onicecandidate处理器创建一个RTCPeerConnection对象。

         2. 处理器在网络候选变得可用时被调用。

         3. 在处理器中，Alice通过其信令通道将字符串化的候选数据发送给Eve。

         4. 当Eve从Alice那里获得候选消息时，她调用addIceCandidate()，将候选项添加到远端对等描述中。 
         
 ## 设备支持情况
 
