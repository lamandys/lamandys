---
title: BleMesh源码解析-配对后的验证数据
date: 2020-02-17 10:58:23
tags: [Android,Bluetooth,Mesh,BLE]
---

上篇文章[Android蓝牙Mesh知识](http://lsxt10.cn/2020/01/20/Android%E8%93%9D%E7%89%99Mesh%E7%9F%A5%E8%AF%86) 讲述了蓝牙Mesh的基本知识和使用流程，对如何使用Bluetooth进行组建Mesh网络有了一定的了解，下面来看看[BleMesh](https://github.com/wl1244hotmai/BLE-Mesh)这个demo相关的知识，我们来分析它的源码，看看链接成功后是如何进行数据验证的。

<!-- more -->

<img src="../BleMesh源码解析-配对后的验证数据/1.jpg" width="800" height="534" alt="与文无关，侵权必删" alt="center">

#### 如何接收数据？
上次有说到，接收数据会回调 **onCharacteristicWrite** 方法，那么想要接收对方发送过来的数据，首先肯定得让对方发送吧，先来看看对方是如何发送数据的。

#### 什么时候进行数据发送？
说到发送数据，在两个客户端互相连接成功后，首先要做的当然是发送身份验证信息，<!--当前本地已连接设备的**设备网络表**给对方， 为什么呢？原因是对方刚刚和你连接上，有可能是刚刚加入到这个Mesh网络的新成员，它还不知道这个网络中有谁在里面。
或者是它处于某一个偏远的地方，它本身也连接了几个自己这边没有连接的网络，所以需要把我们已经连接的网络表发送给对方。
什么时候发送？ -->来看看代码：
```
@Override
            public void onReadRemoteRssi(BluetoothGatt gatt, int rssi, int status) {
                if (status == BluetoothGatt.GATT_SUCCESS && transportCallback != null) {
                    transportCallback.identifierUpdated(mDeviceType,
                            gatt.getDevice().getAddress(),
                            Transport.ConnectionStatus.CONNECTED,
                            extraInfo);
                }

            }
```
我们知道，当两个设备连接成功后会读取对方的Rssi，即是信号强度，读取成功后，就可以回调本地的通知，发现这台新的设备已经上线，处于CONNECTED的状态。再来进一步看看下面做了什么：
```
@Override
    public synchronized void identifierUpdated(Transport transport,
                                               String identifier,
                                               Transport.ConnectionStatus status,
                                               boolean peerIsHost,
                                               Map<String, Object> extraInfo) {
        switch (status) {
            case CONNECTED:
                ...
                if (peerIsHost && shouldIdentifyPeer(identifier)) {
                    Log.d("SessionManager", "Queuing identity to " + identifier);
                    if (!identifierSenders.containsKey(identifier)) {
                        identifierSenders.put(identifier, new SessionMessageSerializer(localIdentityMessage));
                    } else
                        Log.w("SessionManager", "Outgoing messages already exist for unidentified peer " + identifier);
                }
                ...
                SessionMessageSerializer sender = identifierSenders.get(identifier);

                if (sender != null && sender.getCurrentMessage() != null) {

                    boolean sendingIdentity = sender.getCurrentMessage() instanceof IdentityMessage;

                    // 将要发送的内容转化为byte数组
                    byte[] toSend = sender.getNextChunk(transport.getLongWriteBytes());
                    if (toSend == null) return;

                    if (transport.sendData(toSend, identifier)) {
                        if (sendingIdentity) {
                            Log.d("SessionManager", "Sent identity to " + identifier);
                        }
                    } else
                        Log.w("SessionManager", "Failed to send " + sender.getCurrentMessage().getType() + " message to new peer " + identifier);
                }

                break;

            case DISCONNECTED:
                break;
        }
    }

```
已去除无关部分，我们来看看主要的代码。
1. 通过identifierSenders保存了发送消息的对象，来获取当前的消息，类型是身份验证消息。
2. 把消息转为byte数组进行发送。

#### CurrentMessage是什么？
```
 public @Nullable SessionMessage getCurrentMessage() {
        return messages.peek();
    }
```
messages又是什么？哦，是个队列！
```
private ArrayDeque<SessionMessage> messages;
```
那，这个消息什么时候添加进来的呢？
```
public SessionMessageSerializer(final SessionMessage message) {
        this(new ArrayList<SessionMessage>() {{ add(message); }});
    }

    public SessionMessageSerializer(List<SessionMessage> messages) {
        this.messages = new ArrayDeque<>();
        this.messages.addAll(messages);
        this.completedMessages = new ArrayList<>();
        marker = 0;
        serializeCount = 0;
        ackCount = 0;
    }
```
原来是在构造SessionMessageSerializer的时候，把消息传递进来的，SessionMessage是一个抽象类，IdentityMessage是它的实现类。
```
if (peerIsHost && shouldIdentifyPeer(identifier)) {
                    Log.d("SessionManager", "Queuing identity to " + identifier);
                    if (!identifierSenders.containsKey(identifier)) {
                        identifierSenders.put(identifier, new SessionMessageSerializer(localIdentityMessage));
                    } else
                        Log.w("SessionManager", "Outgoing messages already exist for unidentified peer " + identifier);
                }
```
我们再回头看看identifierUpdated这个方法里面，shouldIdentifyPeer表示当前这个identifier是否需要发送IdentifyMessage?
localIdentityMessage是什么？
```
localIdentityMessage = new IdentityMessage(this.context, this.localPeer);
```
localIdentityMessage就是IdentityMessage，localPeer是本地的蓝牙对象，保存了唯一的macAddress。
```
public IdentityMessage(Context context, Peer peer) {
        super();
        this.peer = peer;
        init();
        serializeAndCacheHeaders();
    }

    private void init() {
        type = "identity";
    }

    protected void serializeAndCacheHeaders() {
        if (serializedHeaders == null) {
            if (headers == null) headers = populateHeaders();
            JSONObject jsonHeaders = new JSONObject(headers);
            serializedHeaders = jsonHeaders.toString().getBytes();

        }
    }

     protected HashMap<String, Object> populateHeaders() {
        HashMap<String, Object> headerMap = new HashMap<>();
        headerMap.put(HEADER_TYPE, type);
        headerMap.put(HEADER_BODY_LENGTH, bodyLengthBytes);
        headerMap.put(HEADER_ID, id);
        headerMap.put(HEADER_MAC_ADDRESS, mac_address);

        return headerMap;
    }

    @Override
    protected HashMap<String, Object> populateHeaders() {
        HashMap<String, Object> headerMap = super.populateHeaders();

        headerMap.put(HEADER_ALIAS, peer.getAlias());

        return headerMap;
    }
```
可以看出，IdentityMessage只是一个Json，里面有type类型，有body的长度信息，有消息的ID，有这个用户的mac地址，上层有alias。至此，CurrentMessage完毕。

#### 数据发送了，对方收到会做什么？
```
@Override
    public void onComplete(SessionMessageDeserializer receiver, SessionMessage message, Exception e) {
        String senderIdentifier = identifierReceivers.inverse().get(receiver);

        if (e == null) {
            if (message instanceof IdentityMessage) {

                //set the rssi for direct remote device.
                Peer peer = ((IdentityMessage) message).getPeer();
                if (identifierRssis.size() > 0 && identifierRssis.get(senderIdentifier) != null) {
                    peer.setRssi(-identifierRssis.get(senderIdentifier)); //change minus rssi to absolute value
                }

                peerIdentifiers.put(peer, senderIdentifier);
                identifiedPeers.put(senderIdentifier, peer);

                ...

                if (newIdentity) {
                    if (!sentIdentityToSender)
                        sendMessage(localIdentityMessage, peer);
                    else if (peerIdentifiers.get(peer).size() == 1)
                        callback.directPeerStatusUpdated(peer, Transport.ConnectionStatus.CONNECTED, hostIdentifiers.contains(senderIdentifier));
                }

            }

        }
    }
```
从上面可以看到，收到IdentityMessage的消息后，把对方的Peer这个对象保存下来，并调用directPeerStatusUpdated方法通知做其他的事情。
另外，如果是第一次收到对方发送过来的IdentityMessage验证信息，则需要也向对方发送一条IdentityMessage消息，在发送成功的时候回调directPeerStatusUpdated方法。
其中，identifiedPeers保存着已经连接的对象列表，后面需要的时候可以通过对方的macAddress来获取，然后可以向对方发送消息。
至此，配对后双方需要验证的信息流程结束。

上面说到的如何发送数据，由于篇幅问题，将再开一篇讲解，谢谢！上面的流程如果只看文章会显得很枯燥，建议下载源码后，再对比分析，对认识流程有很大的帮助。