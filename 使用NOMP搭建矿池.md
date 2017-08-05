# 前言
本人实验环境为Ubuntu，本文档不保证别的系统可用。

# 安装环境

    su -
    curl –sL https://deb.nodesource.com/setup | bash -
    apt-get install -y nodejs
    npm install -g cnpm --registry=https://registry.npm.taobao.org
    npm install forever -g
    apt-add-repository ppa:chris-lea/redis-server
    apt-get update
    apt-get install vim screen redis-server
# 配置钱包的RPC通信运行钱包

    git clone https://git.ustclug.org/altcoin/thecatcoin
    cp thecatcoin/src/thecatcoin{d,-cli,-tx} /usr/bin
    cp thecatcoin/src/thecatcoin-qt /usr/bin
编辑钱包启动配置文件 ~/.thecatcoin/.thecatcoin.conf

    daemon=1
    rpcuser=thecatcoinrpc
    rpcpassword=securepassword
    rpcport=9710
运行钱包

    screen -S thecatcoin
    ./thecatcoind
    [C-a d]
# 下载代码

    git clone https://github.com/zone117x/node-open-mining-portal.git nomp
    cd nomp
    npm update
# 配置设置

    cp config_example.json config.json
    cp coins/bitcoin.json coins/thecatcoin.json
修改coins/thecatcoin.json内容为

    {
        "name": "Thecatcoin",
        "symbol": "TCC",
        "algorithm": "sha256",
        "peerMagic": "ca7ca766"
    }
进入pool_configs目录，修改litecoin_example.json为thecatcoin.json并进行修改

修改说明直接看 https://git.ustclug.org/altcoin/node-open-mining-portal 的README.md