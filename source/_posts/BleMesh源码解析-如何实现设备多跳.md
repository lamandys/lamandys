---
title: BleMesh源码解析-如何实现设备多跳
date: 2020-02-26 22:21:48
tags: [Android,Bluetooth,BLE,BleMesh]
---

什么是设备多跳？由于设备硬件的限制，一台中心设备所支持连接外围设备的最大连接数为6到7台，根据设备厂商的设计的设备性能不同，这个数量也有所改变。
假如一台设备的最大连接数为7台，那么在一个50人的聊天群中，剩下的设备是如何和这台中心设备进行信息互通的呢？

<!-- more -->

<img src="../BleMesh源码解析-如何实现设备多跳/q.jpg" width="800" height="416" alt="与文无关，侵权必删">

#### 验证成功后做了什么？
我们继续上篇文章最后的流程，上次说到验证成功会回调**directPeerStatusUpdated**这个方法。我们来看看这个方法做了什么：
```
@Override
    public void directPeerStatusUpdated(@NonNull final Peer peer, @NonNull final Transport.ConnectionStatus newStatus, final boolean isHost) {
        GraphMessage mGraphMessage;
        switch (newStatus) {
            case CONNECTED:
                mPeersGraph.newDirectRemote(peer);
                mGraphMessage = GraphMessage.createOutgoing(null, GraphMessage.ACTION_JOIN, GraphMessage.SINGLE_CAST, mPeersGraph);
                sessionManager.sendMessage(mGraphMessage, peer);
                break;

            case DISCONNECTED:
               	...
                break;
        }
```
我们先来看看newStatus==CONNECTED的代码：
1. peer就是验证成功的对象，是对方。
2. mPeersGraph是本地的一个设备表，内部是LinkedHashMap，保存着各个在线的设备信息。
3. mPeersGraph.newDirectRemote(peer)就是把新来的peer对象添加到本地中的设备信息中。
4. 创建一条GraphMessage消息，把本地的mPeersGraph设备信息表结构信息发给新连接成功的peer对象。

**directPeerStatusUpdated**这个方法做的事情就是这么简单，我收到了名叫“小明”的用户连接成功的消息，那么我先把他的信息保存在本地，然后把我本地的信息发给小明。

#### 小明接收后做了什么？
好，上面分析到我把本地的信息都发给了小明，小明什么时候接收到了呢？之前我们有说过，接收信息的方法是**onCharacteristicChanged**,经过一系列的回调，最终会回调到**BleMeshService#messageReceivedFromPeer**方法。
我们看看主要的代码：
```
switch (message.getType()) {
            case GraphMessage.HEADER_TYPE:
                final GraphMessage remoteGraphMessage = (GraphMessage) message;
                if (remoteGraphMessage.getAction() == GraphMessage.ACTION_JOIN) {
                    // 对方传递过来的用户表结构
                    PeersGraph remoteGraph = remoteGraphMessage.getPeersGraph();
                    mPeersGraph.mergeGarph(sender, remoteGraph);
                    remoteGraph.getEdgeMatrix().get(sender.getMacAddress()).put(
                            localPeer.getMacAddress(),
                            mPeersGraph.getEdgeMatrix().get(sender.getMacAddress()).get(localPeer.getMacAddress())
                    );
                    // 如果是 SINGLE_CAST 表示消息是别人仅发给自己的，别人收不到的,此时需要创建一条广播信息通知大家更新设备表
                    if (remoteGraphMessage.getCast() == GraphMessage.SINGLE_CAST) {
                        // 添加消息源发送人
                        Map<String, Object> extra = new HashMap<>();
                        extra.put(GraphMessage.HEADER_ORIGIN_MAC, sender.getMacAddress());
                        // 再通知其他用户，发送该用户结构表，让别人也合并
                        GraphMessage broadcastGraphMessage = GraphMessage.createOutgoing(extra,
                                GraphMessage.ACTION_JOIN,
                                GraphMessage.BROADCAST,
                                remoteGraph);
                        sessionManager.broadcastMessage(broadcastGraphMessage, sender);
                    } else {
                    	...
                    }
                }
                break;
        }
```
我们来分析一下主要的代码：
1. 首先判断消息是不是GraphMessage消息类型再进一步处理
2. getPeersGraph()拿到传递过来的设备信息表对象信息，并merge到本地的信息中
3. 如果消息的cast==GraphMessage.SINGLE_CAST，表示这条消息只是对方发给自己一个人的消息，自己收到后还需要去通知其他人，此时我肯定是新加入的那个人，发信息给我的那个人肯定是首先收到我连接成功的那个人，还有很多其他的人是不知道我进入的，所以需要发信息通知他们。
4. 继续新建GraphMessage消息，此时的cast就是GraphMessage.BROADCAST了，是群通知其他的人。

归纳起来就是小明收到了我发给他个人的消息，把我本地的设备信息合并到他本地后，发送群消息把我的设备信息表群发给大家（为什么是我的？因为我的设备信息表中是最全面的）

#### 其他人收到信息后如何处理？
还是这个方法，因为最终会回调到**BleMeshService#messageReceivedFromPeer**方法，此时判断的消息还是GraphMessage：
```
            case GraphMessage.HEADER_TYPE:
                final GraphMessage remoteGraphMessage = (GraphMessage) message;
                if (remoteGraphMessage.getAction() == GraphMessage.ACTION_JOIN) {
                	...
                    mPeersGraph.mergeGarph(sender, remoteGraph);
                    ...
                    // 如果之前处理过这条消息的话就不再处理啦
                    if (graphMessageIds.contains(remoteGraphMessage.getID())) {
                        return;
                    }
                    if (remoteGraphMessage.getCast() == GraphMessage.SINGLE_CAST) {
                        ...
                    } else {
                        // 添加当前的消息ID，防止下次继续处理相同的东西
                        graphMessageIds.add(message.getID());
                        // 收到了别人的广播信息，继续用当前的消息通知其他人，此时需要去除消息源发件人和此消息发送人
                        boolean isContain = remoteGraphMessage.getExtraHeaders().containsKey(GraphMessage.HEADER_ORIGIN_MAC);
                        String originMessageSender = isContain ? (String) remoteGraphMessage.getExtraHeaders().get(GraphMessage.HEADER_ORIGIN_MAC) : "";
                        sessionManager.broadcastMessage(message, originMessageSender, sender.getMacAddress());
                    }
                }
                break;
```
解读：
1. if之前的处理都跟之前的是一样的，唯一不同的是if判断进入了else部分
2. 假如“小红”是这个网络中的一员，当他收到小明的信息后，也是把携带过去的设备信息合并到他本地中，然后继续把这条消息群通知给在网内的其他成员。

#### 总结：
1. 就这样的流程，一直到最后一个人收到这个信息后，继续发消息通知其他的人，但是其他的人已经处理过了这条消息，则不再处理。
2. 此时所有的网内成员的本地设备信息表中的信息都是一致的，都有其他剩下的人的信息，相当于自己保存了其他所有人的信息。
3. 现在假如A和Z不是相互连接的，即是A和Z不处于同一个范围内，相差的距离已经超出了蓝牙传输的距离，那么他们如何通讯呢？
4. 上面已经分析了本地设备网络表的发送原理，其实是一样的，不同的是，普通的消息传递，是以两个设备连接后的rssi信号的大小来计算两个设备的最短路径
5. 例如A发送信息给Z，A会通过rssi计算它自己连接的设备中，哪个是最优的距离，比如是H，那么A就会发信息给H
6. 当H收到了A发过来的信息，并且知道了目标用户不是他，他就会继续计算最优路径，比如Q，H就会向Q转发该信息
7. 以此类推，直到这条信息传递到Z，才会终止发送，此时就实现了从A到Z的一个过程，这个过程就叫做多跳
8. 最优路径怎么算？这个是算法的问题了，不在此文章的范畴。