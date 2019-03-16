---
title: Hyperledger-fabric
author: Kairou Zeng
date: 2018/01/29
---

### Hyperledger-fabric

#### Fabric 架构
- 区块链服务(Blockchain)
- 链码服务(Chaincode)
- 成员权限管理(Membership)

#### Fabric 应用构成
- Peer：负责模拟执行交易和记账，主要功能是调用智能合约执行交易和记账。
    - 智能合约：智能合约是运行于区块链上的应用程序，Fabric的智能合约称为`链码`，分为`系统链码`和`用户链码`
        - 系统链码用来实现系统层面的功能，包括系统的配置，用户链码的部署、升级，用户交易的签名和验证策略等。
        - 用户链码实现用户的应用功能。开发者编写链码应用程序并将其部署到网络上。终端用户通过与网络节点交互的客户端应用程序调用链码。
    
     链码被编译成一个独立的应用程序，运行于隔离的Docker容器中，在链码部署的时候会自动生成合约的Docker镜像。

    - 共享账本：共享账本包括区块文件和K-V状态数据，区块文件存储在本地文件系统，由Peer节点负责写入，文件内容是排序后的区块数据，每个文件有大小限制，存储一定数量的区块，区块由交易共识系统产生，包含一条或多条交易。K-V状态数据提供给链码存取使用，采用LevelDB存储。
- Channel：通道，子链，同一Peer可加入不同channel。Chaincode的操作基于Channel进行。同一Channel上的Peer节点同步其上Chaincode执行的结果。流过通道的数据对于加入该通道的结点是共享的。因此，对于加入同一通道的结点来说，就相当于构建了一条子链。这条子链上的内容对于通道外的结点是不可知的。并且，同一个peer结点可以加入不同channel。而Chaincode的执行是基于channel进行的，在一条channel上chaincode执行的结果会被该channel上所有的结点同步到本地Ledger中。
- Endorser：(模拟)执行Chaincode。分离计算任务，减轻consensus节点负担，增加吞吐量。支持endorsement policy，即一个transaction的提交需要哪些endorser进行背书才可通过，更加灵活。
- Orderer：负责交易共识并生成区块。对chaincode执行结果consensus。Orderer结点的工作就是consensus。Chaincode在endorser结点处执行之后，会被发送给orderer进行排序或者说consensus，保证transaction的顺序是一致的。然后，orderer结点会把transaction发送给相应的channel中的所有committer结点。
- Committer：将chaincode执行结果写进ledger


#### [Java SDK for Hyperledger Fabric 1.1](https://github.com/hyperledger/fabric-sdk-java)
The SDK helps facilitate Java applications to manage the lifecycle of Hyperledger channels and user chaincode. The SDK also provides a means to execute user chaincode, query blocks and transactions on the channel, and monitor events on the channel.

该SDK帮助Java应用程序管理Hyperledger通道和用户链码的生命周期。SDK还提供了一种方法，可以在通道上执行用户链码、查询块和事务，并监视通道上的事件。

The SDK acts on behave of a particular User which is defined by the embedding application through the implementation of the SDK's User interface.

SDK针对特定用户的行为进行操作，该用户通过SDK的用户界面的实现来定义嵌入应用程序。

Note, the SDK does not provide a means of persistence for the application defined channels and user artifacts on the client. This is left for the embedding application to best manage. Channels may be serialized via Java serialization in the context of a client. Channels deserialized are not in an initialized state. Applications need to handle migration of serialzed files between versions.

注意，SDK没有为应用程序定义的通道和客户端构件提供持久性的方法。这就留给了嵌入应用程序来更好地管理。在客户机的上下文中，可以通过Java序列化来序列化通道。反序列化的通道不处于初始化状态。应用程序需要处理不同版本之间的序列化文件的迁移。

The SDK also provides a client for Hyperledger's certificate authority. The SDK is however not dependent on this particular implementation of a certificate authority. Other Certificate authority's maybe used by implementing the SDK's Enrollment interface.

该SDK还为Hyperledger的证书颁发机构提供了一个客户端。但是，SDK并不依赖于证书颁发机构的这个特定实现。其他证书颁发机构可能是通过实现SDK的注册接口来使用的。

This provides a summary of steps required to get you started with building and using the Java SDK. Please note that this is not the API documentation or a tutorial for the SDK, this will only help you familiarize to get started with the SDK if you are new in this domain.

这提供了一个步骤的总结，以帮助您从构建和使用Java SDK开始。请注意，这不是API文档或SDK的教程，如果您是这个领域的新手，这只会帮助您熟悉SDK。