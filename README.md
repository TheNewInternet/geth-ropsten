# geth-ropsten
A ethereum test network id= 3, ropsten chain, demo.

## 目標
在本篇將示範，操作連進 ropsten，然後開始挖礦，並查詢 eth.accounts ...

## 開啟並進入本機 Ropsten Node

注意這裡只是建立自己電腦上基於 ropsten 規格的節點，
若本節點沒有找到其他同規格節點（Peers）則不會同步區塊資料。

```
$ geth --ropsten console
```


## 如何找到其他 Peers
Geth 會根據 discovery protocol 不斷在網路上尋找其他可連線的節點。
若網路太稀疏，則建議手動 admin.addPeer("enode://pubkey@ip:port")。
```
How Peers Are Found
Geth continuously attempts to connect to other nodes on the network until it has peers. If you have UPnP enabled on your router or run ethereum on an Internet-facing server, it will also accept connections from other nodes.

Geth finds peers through something called the discovery protocol. In the discovery protocol, nodes are gossipping with each other to find out about other nodes on the network. In order to get going initially, geth uses a set of bootstrap nodes whose endpoints are recorded in the source code.

To change the bootnodes on startup, use the --bootnodes option and separate the nodes by commas. For example:
```
也可以透過 --bootnodes 在開機的時候指定有固定的公開 ip 的節點。
```
$ geth --bootnodes enode://pubkey1@ip1:port1,enode://pubkey2@ip2:port2,enode://pubkey3@ip3:port3
```
常見的連線問題，如：防火牆設定等。
```
Common Problems With Connectivity
Sometimes you just can’t get connected. The most common reasons are as follows:

Your local time might be incorrect. An accurate clock is required to participate in the Ethereum network. Check your OS for how to resync your clock (example sudo ntpdate -s time.nist.gov) because even 12 seconds too fast can lead to 0 peers.
Some firewall configurations can prevent UDP traffic from flowing. You can use the static nodes feature or admin.addPeer() on the console to configure connections by hand.
To start geth without the discovery protocol, you can use the --nodiscover parameter. You only want this is you are running a test node or an experimental test network with fixed nodes.
```

## 檢查連線狀態
```
To check how many peers the client is connected to in the interactive console, the net module has two attributes give you info about the number of peers and whether you are a listening node.
```

```
> net.listening
true
> net.peerCount
4
```
執行 admin.peers 則可查看全部以連上的節點
```
To get more information about the connected peers, such as IP address and port number, supported protocols, use the peers() function of the admin object. admin.peers() returns the list of currently connected peers.
```
```
> admin.peers
[{
  ID: 'a4de274d3a159e10c2c9a68c326511236381b84c9ec52e72ad732eb0b2b1a2277938f78593cdbe734e6002bf23114d434a085d260514ab336d4acdc312db671b',
  Name: 'Geth/v0.9.14/linux/go1.4.2',
  Caps: 'eth/60',
  RemoteAddress: '5.9.150.40:30301',
  LocalAddress: '192.168.0.28:39219'
}, {
  ID: 'a979fb575495b8d6db44f750317d0f4622bf4c2aa3365d6af7c284339968eef29b69ad0dce72a4d8db5ebb4968de0e3bec910127f134779fbcb0cb6d3331163c',
  Name: 'Geth/v0.9.15/linux/go1.4.2',
  Caps: 'eth/60',
  RemoteAddress: '52.16.188.185:30303',
  LocalAddress: '192.168.0.28:50995'
}, {
  ID: 'f6ba1f1d9241d48138136ccf5baa6c2c8b008435a1c2bd009ca52fb8edbbc991eba36376beaee9d45f16d5dcbf2ed0bc23006c505d57ffcf70921bd94aa7a172',
  Name: 'pyethapp_dd52/v0.9.13/linux2/py2.7.9',
  Caps: 'eth/60, p2p/3',
  RemoteAddress: '144.76.62.101:30303',
  LocalAddress: '192.168.0.28:40454'
}, {
  ID: 'f4642fa65af50cfdea8fa7414a5def7bb7991478b768e296f5e4a54e8b995de102e0ceae2e826f293c481b5325f89be6d207b003382e18a8ecba66fbaf6416c0',
  Name: '++eth/Zeppelin/Rascal/v0.9.14/Release/Darwin/clang/int',
  Caps: 'eth/60, shh/2',
  RemoteAddress: '129.16.191.64:30303',
  LocalAddress: '192.168.0.28:39705'
} ]
```
查看自己的則是，
```
To check the ports used by geth and also find your enode URI run:
```
```
> admin.nodeInfo
{
  Name: 'Geth/v0.9.14/darwin/go1.4.2',
  NodeUrl: 'enode://3414c01c19aa75a34f2dbd2f8d0898dc79d6b219ad77f8155abf1a287ce2ba60f14998a3a98c0cf14915eabfdacf914a92b27a01769de18fa2d049dbf4c17694@[::]:30303',
  NodeID: '3414c01c19aa75a34f2dbd2f8d0898dc79d6b219ad77f8155abf1a287ce2ba60f14998a3a98c0cf14915eabfdacf914a92b27a01769de18fa2d049dbf4c17694',
  IP: '::',
  DiscPort: 30303,
  TCPPort: 30303,
  Td: '2044952618444',
  ListenAddr: '[::]:30303'
}
```
得到本機接點位址，
```
> admin.nodeInfo.enode
```

## 同步區塊
理論上執行以下指令，會得到當前區塊數量，
```
> eth.blockNumber
0
```

讀者會發現回傳是 ０，表示我們還沒有開挖任何區塊，但這時執行以下指令卻會發現我們目前的確有區塊！？
```
> eth.syncing
{
  currentBlock: 537445,
  highestBlock: 11205572,
  knownStates: 0,
  pulledStates: 0,
  startingBlock: 0
}
```
getBlock 也確實有回傳，第537445個區塊。
```
> eth.getBlock(537445)
{
  difficulty: 674655039,
  extraData: "0xd983010508846765746887676f312e372e348777696e646f7773",
  gasLimit: 4712388,
  gasUsed: 447232,
  hash: "0x143cf36c2b6b1cbeb87129d2a8d9a066ef7efa835406a5fae7997dc2d83dff95",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000800000000000000000000000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000000000000000080000000000000000000000000000000000000000000000000000000000000800000000",
  miner: "0x58a251b5dc3d5d2fad2fa2684188a10bfc4f5baa",
  mixHash: "0x53984da6f8d43124b1826d145e2d592a2d5efc11a0848072fe795201650b501c",
  nonce: "0x7c41fa11aabd2c22",
  number: 537445,
  parentHash: "0x2718a4e44351b42418ababe10f93875e0cda07d9761967d1fec432b7d382498c",
  receiptsRoot: "0x00837d4234050394ea22af160b1c754dbdcca60d317d5dbdcf19e0ac3b8ce398",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 9266,
  stateRoot: "0xcf5cf7063e5abea6d5189f9a088a7744ffb8672ec6c6d26adb21194eb7232660",
  timestamp: 1487144682,
  totalDifficulty: 175947972818077,
  transactions: ["0xeff5092c53c54f41177f86e6b32e98e95a58b2dcd1b73a156261c8f0bd9e0c06", "0x420d161e0c0a444b1e483d1c36a48442cd887f948873360a861f3b44fcdc7da7", "0xa901f0ce05bd43dfb0d92f4cb747406a507d2b314cc4b87e67829ebfd8d9acc2", "0x82f0ad8dab791fe82fc5a63070b764230d2418d17bcaea002891029e95698b33", "0x9376ae8316c2204f9548e4daf774ed9b8f0fe6bd6d87fbbcc25ec692d1cfbfb2"],
  transactionsRoot: "0xda5090b2f19979f5c1e0a204b8499559ca08091ce1c715e566c92cf293d747fa",
  uncles: []
}
```
原來在 ropsten 網路上 highestBlock 代表最長的鏈，是我們要同步的對象。

由於 Geth fast sync 設計的緣故，使得我們下載了節點，不代表完成全部節點下載（追到 highestBlock ），第一次同步需要不少時間，而且會一直越來越高（長）..

```
Geth 1.6.0 uses fast sync by default. The block states are unavailable for intermediate blocks and are downloaded only for the recent state. As such, up to the point that sync completes, the latest complete block is zero, since all newer blocks are incomplete during sync. When fast sync finishes, it downloads the associated state for the head, so the head block becomes complete and the latest block can be bumped to the current head.
```

## 建立帳號
也就是建立錢包地址
```
> personal.newAccount()
```
結著需要輸入兩次 密碼，最後會回傳 錢包地址（eg., "0x385a16ee0b5940b1fee93189547f00b2f4bb917b"）。
錢包資料存在 ./.ethereum/netXXX/keystore/ 資料夾，會以 時間 ＋ 地址 命名。

以我的節點為例，則有 4 個帳號。
```
> eth.accounts
["0x385a16ee0b5940b1fee93189547f00b2f4bb917b", "0x9678a20bd6716a5ae8eb65f7fa350342fd97fe0f", "0x3b66d5392d6ff82f9ba9c5242fbbded9093eb271"]
```
其中 eth.coinbase 等價於 eth.accounts[0]，很直觀地，當你想要挖礦，就需要指定錢包地址。

只要你的 eth.accounts 不為空陣列，應該就會預設 eth.accounts[0] 來存放挖礦的獎勵。

## 交易

在做人任何交易之前，都需要先解鎖帳號，可以附加設定解鎖時間等。
```
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x385a16ee0b5940b1fee93189547f00b2f4bb917b
Passphrase:
true
```
我們來查看第一個帳號有多少 Ether，應為 0。
```
> eth.getBalance(eth.accounts[0])
0
```
進行挖礦時會將挖到的 Ether 給 eth.coinbase 這個帳號，eth.coinbase預設就是eth.accounts[0] ，我們來挖礦吧：
```
> eth.coinbase
"0x385a16ee0b5940b1fee93189547f00b2f4bb917b"
```
1 代表開幾個 Thread 來挖礦。
```
> miner.start(1)
true
```
結束挖礦！
```
> miner.stop()
true
```

我們來看看挖礦結果，不可分割的最小單位是 Wei，好像是 10^18 ..?
```
> eth.blockNumber
7

> eth.getBalance(eth.accounts[0])
21000000000000000000

> eth.getBalance(eth.coinbase)
21000000000000000000

> web3.fromWei(eth.getBalance(eth.accounts[0]), "ether")
21
```

我們看到區塊數變成 7，且帳號裡的 Ether 變成 21 了，我們果然有成功挖到 Ether 了！

## 交易
有了一點錢之後我們就可以進行「 轉帳 」了！
記得建立第二個帳號。

```
> eth.sendTransaction({ from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(5, "ether") })
Error: authentication needed: password or unlock
```

也要記得解鎖..
```
> personal.unlockAccount(eth.accounts[0])
Unlock account 0xe00f575ea205035ca9c530effa728202e5385cb2 Passphrase:
true

> var txHash = eth.sendTransaction({ from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(5, "ether") })

> txHash "0x9f494ebc7b9ad6e6a9abd36798c6d04dcd7e883acf553a99e581b8550ec16178"
```
我們可以成功送出交易了，讓我們看一下交易的詳細資料：
```
> eth.getTransaction(txHash)
{
  blockHash: "...",
  blockNumber: null,
  from: "0x385a16ee0b5940b1fee93189547f00b2f4bb917b",
  gas: 90000,
  gasPrice: 20304857463,
  hash: "0x9f494ebc7b9ad6e6a9abd36798c6d04dcd7e883acf553a99e581b8550ec16178",
  input: "0x",
  nonce: 0,
  r: "...",
  s: "...",
  to: "0x9678a20bd6716a5ae8eb65f7fa350342fd97fe0f",
  transactionIndex: null,
  v: "0x78",
  value: 5000000000000000000
}
```

我們可以看到這個交易的詳細資料中，blockNumber 是 null，這代表交易還未確認，如果我們查看交易收據：

```
> eth.getTransactionReceipt(txHash)
null
```

回傳得到 null，我們看看第 2 個帳號是否有收到 Ether：

```
> eth.getBalance(eth.accounts[1])
0
```

仍然是 0 Ether，所以第 2 的帳號還沒有收到 Ether，我們需要將挖礦打開，這樣新的交易才能在新的挖礦區塊裡被確認：

```
> miner.start(1)
true

// 等待挖礦完成幾個區塊之後

> miner.stop()
true

> eth.getBalance(eth.accounts[1])
5000000000000000000
```

我們可以看到第 2 個帳號拿到 5 Ether 了，而這個交易的 blockNumber 也變成了 8，接下來我們看看交易收據：

```
> eth.getTransactionReceipt(txHash)
{
  blockHash: "...",
  blockNumber: 8,
  contractAddress: null,
  cumulativeGasUsed: 21000,
  from: "0x385a16ee0b5940b1fee93189547f00b2f4bb917b",
  gasUsed: 21000,
  logs: [],
  logsBloom: "...",
  status: "0x1",
  to: "0x9678a20bd6716a5ae8eb65f7fa350342fd97fe0f",
  transactionHash: "...",
  transactionIndex: 0
}
```

我們可以看到交易收據的詳細資料了，其中的 status 代表交易的情況，0x1 代表交易完成，如果是 0x0 就代表交易發生錯誤了。




