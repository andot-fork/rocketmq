# RocketMQ Compile Guide

## 设置环境变量

点击Idea 启动类的下拉框，选择相应的启动类，设置环境变量

点击Edit Configurations > Environment variables: 

ROCKETMQ={你的源码目录path}/distribution

## 创建测试类，启动NameSer

直接在这个类【NameServerInstanceTest】中建立也可以，也可以自己创建一个类

创建main方法：

```java
public class NameServerInstanceTest {
    public static void main(String[] args) throws Exception {
        // NamesrvConfig 配置
        final NamesrvConfig namesrvConfig = new NamesrvConfig();
        // NettyServerConfig 配置
        final NettyServerConfig nettyServerConfig = new NettyServerConfig();
        nettyServerConfig.setListenPort(9876); // 设置端口
        // 创建 NamesrvController 对象，并启动
        NamesrvController namesrvController = new NamesrvController(namesrvConfig, nettyServerConfig);
        namesrvController.initialize();
        namesrvController.start();
        // 睡眠
        Thread.sleep(DateUtils.MILLIS_PER_DAY);
    }
}
```
直接可以启动

## 创建测试类，启动Broker

创建类，加入main方法，加入下面代码

```java
public class BrokerControllerTest {
    public static void main(String[] args) throws Exception {
        // 设置版本号
        System.setProperty(RemotingCommand.REMOTING_VERSION_KEY, Integer.toString(MQVersion.CURRENT_VERSION));
        // NettyServerConfig 配置
        final NettyServerConfig nettyServerConfig = new NettyServerConfig();
        nettyServerConfig.setListenPort(10911);
        // BrokerConfig 配置
        final BrokerConfig brokerConfig = new BrokerConfig();
        brokerConfig.setBrokerName("broker-a");
        brokerConfig.setNamesrvAddr("127.0.0.1:9876");
        // MessageStoreConfig 配置
        final MessageStoreConfig messageStoreConfig = new MessageStoreConfig();
        messageStoreConfig.setDeleteWhen("04");
        messageStoreConfig.setFileReservedTime(48);
        messageStoreConfig.setFlushDiskType(FlushDiskType.ASYNC_FLUSH);
        messageStoreConfig.setDuplicationEnable(false);

//        BrokerPathConfigHelper.setBrokerConfigPath("/Users/yunai/百度云同步盘/开发/Javascript/Story/incubator-rocketmq/conf/broker.conf");
        // 创建 BrokerController 对象，并启动
        BrokerController brokerController = new BrokerController(//
                brokerConfig, //
                nettyServerConfig, //
                new NettyClientConfig(), //
                messageStoreConfig);
        brokerController.initialize();
        brokerController.start();
        System.out.println("启动起来了...");
        // 睡眠
        Thread.sleep(DateUtils.MILLIS_PER_DAY);
    }
}

```
直接可以启动

## 找到[example]模块启动生产者、消费者

路径:

package org.apache.rocketmq.example.quickstart;

生产者加入代码：

```
producer.setNamesrvAddr("127.0.0.1:9876");
```

消费者加入代码：

```
consumer.setNamesrvAddr("127.0.0.1:9876");
```

然后启动消费者， 运行生产者，就可以看到控制台的打印了

但是消费者打印的是一个数组，里面body字段的值是byte数组

可以在打印的地方打印下：

```
for (MessageExt messageExt : msgs) {
    System.err.println("接收到的消息："+new String(messageExt.getBody()));
}
```

