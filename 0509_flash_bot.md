```
Bitmex Research에서 이야기 하는 Flashbot의 구조
https://twitter.com/BitMEXResearch/status/1522593288502616065?cxt=HHwWgoC9qeyhq6EqAAAA

좀 더 자세히
https://blog.bitmex.com/flashbots/ 

Searcher: MEV 로 인한 이득을 보기 위한 시장 참여자 (mempool 보고 있다가 수익이 되는 tx가 발견되면 front running 수행).

MEV 중 가장 많은 revenue를 낸 것이 arbitrage임. 
MEV를 하기 위한 조건
	1. Defi activity 를 모니터링하고 있어야 함
	2. 기회를 포착하기 위한 상당한 자본이 있어야 함
	
Searcher 들의 gas fee bidding 경쟁때문에 open 된 mempool을 거치지 않고 private한 mining pool을 통해서 수행함으로써 MEV를 searcher 와 miner가 나눠가지는 방법을 사용했다. 하지만, 이 방법은 mining centralization 문제를 발생시켰다. 특정 mining pool이 tx를 검열할 수 있다는 점에서 이더리움의 핵심 가치를 훼손하는 일이 발생하는 것이다. 

이를 해결하기 위해서 Flash bot이 나왔다. Flash bot은 searcher 와 miner 를 이어주는 역할을 중간 매개체로, private 한 contract와 arrangement 를 사용하는 것이 아니라 open participation system 을 갖추고 있다. 구조는 기존과 같이 searcher 가 mempool을 검토하고, 수익이 될만한 MEV 기회를 포착하면 Flashbot 서버에 tx를 제출한다. 이 tx에는 miner에게 지급하는 비용도 포함이 되어있는데, 이렇게 flash bot으로 넘기면 mempool로 MEV tx를 broadcasting 하는 문제는 신경쓰지 않아도 된다. 

Flash bot 서버는 이 요청을 받아서 tx 의 bundle을 miner에게 보내고 이것은 public mempool은 거치지 않는다. 충돌하는 tx는 bidding 방식으로 높은 비용을 지불하는 searcher 의 tx를 우선적으로 처리한다. 이런 bidding 방식을 통해서 MEV의 수익 중 63%는 searcher에게, 37%는 miner에게 보내진다. 

Miner 는 seracher 가 보낸 tx 와 관계 없는 non-flashbot tx 도 블록에 추가할 수 있으며 이 방법은 public mempool과도 연동할 수 있다는 장점이 있다. 

Flashbot은 centralized system으로 만약에 악의적인 행동을 하게 되면 다른 searcher 보다 높은 입찰가를 써서 앞설 수 있다. (?) 
If Flashbots acted nefariously, it could outbid and front run other searchers themselves. (이건 무슨 말일까) 그러므로 Flashbot은 trusted 하고 centralized 한 시스템이다. 

MEV miner 는 block들을 보고 MEV tx 가 있는지 없는지를 확인할 수 있으며, reward 가 높은 block 을 채택해서 생성할 수 있다. 단, Flashbot에 의해서 bundle 로 되어 있는 tx들은 reordering 등을 할 수 없기 때문에 tx의 순서가 바뀌거나 새로운 tx가 추가되거나 하는 등의 작업은 할 수 없다. 

결국 Flash bot은 중앙화된 시스템인데 어떤 의미가 있냐라고 할 수 있는데, 중앙화된 시스템이긴 하지만 open participation으로 인해서 작은 규모의 miner 들이 경쟁을 할 수 있는 환경을 만들어 놓았다고 볼 수 있다. 만약 Flash bot 이 없었다면 one large miner, one larger searcher 와 같이 private arrangement 를 하는 독점현상이 발생할 수 있었지만 Flash bot 은 이를 방지했다고 볼 수 있다. 

그리고 Flashbot 은 DAO로 운영되고 Lido 가 한 것처럼 탈중앙화의 방향으로 가고 있다. (Lido)
```
