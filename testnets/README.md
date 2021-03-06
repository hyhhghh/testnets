# IRISnet测试网


 * [IRIShub 简介](#IRIShub-简介)
  * [如何加入fuxi-2000测试网](#如何加入fuxi-1002测试网)
    * [安装IRIShub](#安装IRIShub)
    * [部署一个全节点](#部署一个全节点)
    * [测试IRISHub相关功能](#测试IRISHub相关功能)

## IRIShub 简介

IRIS Hub是在Cosmos生态中的区域性枢纽，提供iService服务

## 如何加入fuxi-2000测试网

### 安装IRIShub

#### 服务器配置要求


首先，你需要配置一台服务器。你的验证人节点应该能够一直运行，使用你可能需要在一台数据中心的服务器。任何像AWS、GCP、DigitalOcean中的云服务器都是适合的。

IRIS Hub是用Go语言编写的。它可以在任何能够编译并运行Go语言程序的平台上工作。然而，我强烈建议在Linux服务器上运行验证人节点。我曾经尝试在Windows上运行验证人节点。我能够顺利的编译但是在运行的时候会有一些问题。接下来的说明和指导都是基于Linux服务器的。
这是我们的服务器的配置：

* CPU核数：2
* 内存容量：2GB
* 磁盘空间：40GB
* 操作系统：Ubuntu 18.04 LTS/16.04 LTS
* 允许的入方向的链接：TCP端口46656和46657


#### 方法1：下载发行版安装

进入下载页: https://github.com/irisnet/irishub/releases/
下载对应版本的可执行文件
解压缩tar -C /usr/local/bin -xzf iris$VERSION.$OS-$ARCH.zip
拷贝到/usr/local/bin/目录下 
执行以下命令,若出现对应的版本号则说明安装成功。
```
$ iris version
v0.3.4
    
$ iriscli version
v0.3.4
```
#### 方法2：源码编译安装

#### 安装Go版本 1.10+ 


系统要求：

Ubuntu LTS 16.04


安装IRISHub需要保证Go的版本在1.10以上，

通过执行以下命令安装1.10版本的Go。

```
    $ sudo add-apt-repository ppa:gophers/archive
    $ sudo apt-get update
    $ sudo apt-get install golang-1.10-go
```

以上命令将安装 golang-1.10-go在 /usr/lib/go-1.10/bin. 需要将它加入到PATH中

```
    echo "export PATH=$PATH:/usr/lib/go-1.10/bin" >> ~/.bash_profile
    source ~/.bash_profile
```

同时，你需要指定相关的 $GOPATH, $GOBIN, 和 $PATH 变量, 例如:

```
    mkdir -p $HOME/go/bin
    echo "export GOPATH=$HOME/go" >> ~/.bash_profile
    source ~/.bash_profile
    echo "export GOBIN=$GOPATH/bin" >> ~/.bash_profile
    source ~/.bash_profile
    echo "export PATH=$PATH:$GOBIN" >> ~/.bash_profile
    source ~/.bash_profile
```

参考链接：

1. https://golang.org/doc/install
2. https://github.com/golang/go/wiki/Ubuntu



#### 下载源码并安装


在完成Go的安装后，通过以下命令下载并安装IRIS hub相关程序.

```
mkdir -p $GOPATH/src/github.com/irisnet
cd $GOPATH/src/github.com/irisnet
git clone https://github.com/irisnet/irishub
cd irishub && git checkout v0.3.4

curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh

make get_vendor_deps && make install
```

以上命令将完成 iris 和 iriscli的安装. 若出现对应的版本号则说明安装成功。

```
    $ iris version
    v0.3.4
    
    $ iriscli version
    v0.3.4
```

### 部署一个全节点

* **配置软件运行的目录**

iris在运行过程中所依赖的配置文件和数据会存放在\$IRISHOME下，所以在运行iris前，需要指定一个目录作为$IRISHOME。若不配置目录，则\$IRISHOME默认为：/\$HOME/.iris。

* **初始化**
  执行以下操作，
  ```
  iris init --home=<iris-home>
  ```

会在$IRISHOME下创建两个文件夹：/config 和 /data。/config终会包含两个重要文件：genesis.json 和config.toml。genesis文件中定义了区块链网络的初始状态，而config.toml指定了iris软件模块的重要组成部分。

* **下载配置文件文件**

如果你想参加到genesis文件的生成流程中，请根据以下[文档](https://github.com/kidinamoto01/testnets-1/blob/master/testnets/docs_CN/Genesis-generation.md)提交相关文件。

下载fuxi-2000的配置文件，并替换原有的/$IRISHOME/config目录下的文件：

    cd $IRISHOME/config/
    rm genesis.json
    rm config.toml
    wget https://raw.githubusercontent.com/irisnet/testnets/master/testnets/fuxi-2000/config/config.toml
    wget https://raw.githubusercontent.com/irisnet/testnets/master/testnets/fuxi-2000/config/genesis.json

* **修改配置文件**

在config.tmol文件中可以配置以下信息：

1. 将moniker字段配置称为自定义的名称，这样便于区分不同的节点
2. seed字段用语设置种子节点，在fuxi-1002中的官方种子节点为：3fb472c641078eaaee4a4acbe32841f18967672c@35.165.232.141:46657
3. 将`external_address` 改为本地IP

* **启动一个全节点**

通过以下命令启动全节点，并将日志输出到文件中：

    iris start --home $IRISHOME > log &

通过执行以下操作确认节点的运行状态：

    iriscli status

示例输出：

    {"node_info":{"id":"3fb472c641078eaaee4a4acbe32841f18967672c","listen_addr":"172.31.0.190:46656","network":"fuxi-1002","version":"0.21.0","channels":"4020212223303800","moniker":"name","other":["amino_version=0.9.9","p2p_version=0.5.0","consensus_version=v1/0.2.2","rpc_version=0.7.0/3","tx_index=on","rpc_addr=tcp://0.0.0.0:46657"]},"sync_info":{"latest_block_hash":"7B1168B2055B19F811773EEE56BB3C9ECB6F3B37","latest_app_hash":"B8F7F8BF18E3F1829CCDE26897DB905A51AF4372","latest_block_height":12567,"latest_block_time":"2018-07-26T02:43:56.757513477Z","syncing":false},"validator_info":{"address":"CAF80DAEC0F4A7036DD2116B56F89B07F43A133E","pub_key":{"type":"AC26791624DE60","value":"Cl6Yq+gqZZY14QxrguOaZqAswPhluv7bDfcyQx2uSRc="},"voting_power":0}}

通过以上命令可以查看状态：

1. “syncing":false表示节点与网络保持同步

2. “syncing":true表示节点正在同步区块

3. "latest_block_height" 表示最新的区块高度

   ​

* **重置一个全节点**

若需要将一个节点重启，则可以通过以下命令让节点再次通过与网络保持同步。

重置IRIShub节点流程如下：

1. 关闭iris进程

    kill -9 <PID>

若Genesis文件有变动，则需要下载新的文件到$IRISHOME/config目录下。

2.  重置iris

iris unsafe_reset_all --home=$IRIShome

3. 重新启动

通过以下命令启动全节点，并将日志输出到文件中：

    iris start --home <path_to_your_home> > log文件地址 &

通过执行以下操作确认节点的运行状态：

    iriscli status

示例输出：

```
{"node_info":{"id":"3fb472c641078eaaee4a4acbe32841f18967672c","listen_addr":"172.31.0.190:46656","network":"fuxi-1002","version":"0.21.0","channels":"4020212223303800","moniker":"name","other":["amino_version=0.9.9","p2p_version=0.5.0","consensus_version=v1/0.2.2","rpc_version=0.7.0/3","tx_index=on","rpc_addr=tcp://0.0.0.0:46657"]},"sync_info":{"latest_block_hash":"7B1168B2055B19F811773EEE56BB3C9ECB6F3B37","latest_app_hash":"B8F7F8BF18E3F1829CCDE26897DB905A51AF4372","latest_block_height":12567,"latest_block_time":"2018-07-26T02:43:56.757513477Z","syncing":false},"validator_info":{"address":"CAF80DAEC0F4A7036DD2116B56F89B07F43A133E","pub_key":{"type":"AC26791624DE60","value":"Cl6Yq+gqZZY14QxrguOaZqAswPhluv7bDfcyQx2uSRc="},"voting_power":100}}
```

通过以上命令可以查看状态：

“syncing":false表示节点与网络保持同步

"latest_block_height":表示最新的区块高度


### 测试IRISHub相关功能

在接下来你可以执行以下操作测试IRISHub的功能：

* Token Transfer：[Link](https://github.com/kidinamoto01/testnets-1/blob/master/testnets/docs_CN/转账交易.md)
* Bond&Delegate： [Link](https://github.com/irisnet/testnets/blob/master/testnets/Basic-Bond:Delegate-Operation.md)
* 部署IRISHub Monitor [Link](https://github.com/irisnet/testnets/blob/master/testnets/docs_CN/如何部署monitor.md)