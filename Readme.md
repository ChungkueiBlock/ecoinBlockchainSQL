# ecoinBlockchainSQL
BlockchainSQL是一个公共服务,将区块链数据写入数据库中(MySQL/Mongodb等),为其他服务提供高效的查询/统计能力.

![BlockchainSQL](/docs/images/blockchainSQL.png)

### Usage:
```
VERSION:
   0.0.1

COMMANDS:
   license  Display license information
   version  Print version numbers
   help     Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --backtrace value    Request a stack trace at a specific logging statement (e.g. "block.go:271")
   --conf value         TOML configuration file
   --debug              Prepends log messages with call-site location (file and line number)
   --nsqd               enable nsqd
   --nsqnslookup value  nsq nsqlookupd host (default: "127.0.0.1:4150")
   --verbosity value    Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail (default: 3)
   --vmodule value      Per-module verbosity: comma-separated list of <pattern>=<level> (e.g. eth/*=5,p2p=4)
   --help, -h           show help
```

### config.toml示例:
```
MysqlDSN="root:123456@tcp(localhost:3306)/blockchain_v1?charset=utf8"

[EchochainSQL]
StartBlk=0 //指定起始块号, 缺省该字段,则从数据库中读取最大块号(会重复写一次该块号)
EndBlk=100 //指定终止块号, 达到该块号时,程序终止;缺省该块号时,会一直监听最新块
BatchInsert=1000 //写入数据库时, 批量插入的bucket大小
BlockGap=12  //只读取到最新块前的第N个块, 防止块回滚而反复擦写数据库
BlockDuration=1 //追逐最新块时,块间隔时间BlockDuration毫秒;监听最新块时,每轮间隔1秒
RpcProvider="ws://172.29.1.169:8846" //节点服务器地址

[ERC20]
Source="/path/to/erc20.txt" // 批量导入erc20代币
```

### 程序运行:
```bash
./build.sh
./bin/blockchainSQL --conf=/path/to/conf.toml --verbosity=4
INFO [07-30|15:11:17] initiate mysql successfully!
INFO [07-30|15:11:17] connected to rpc server                  host=ws://172.29.1.169:8846
INFO [07-30|15:11:17] chain sql daemon start listening         host=ws://172.29.1.169:8846
INFO [07-30|15:11:17] block number in database                 blockNumber=9999
INFO [07-30|15:11:17] echochain rpc server started...
INFO [07-30|15:11:19] handled                                  blk#=10000 txs=0 current highest blk#=3737157 sleeping seconds=1ms
INFO [07-30|15:11:19] handled                                  blk#=10001 txs=0 current highest blk#=3737157 sleeping seconds=1ms
INFO [07-30|15:11:19] handled                                  blk#=10002 txs=0 current highest blk#=3737157 sleeping seconds=1ms
INFO [07-30|15:11:19] handled                                  blk#=10003 txs=0 current highest blk#=3737157 sleeping seconds=1ms
```