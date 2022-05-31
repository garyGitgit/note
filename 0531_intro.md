```


이전 글에서 Maximal Extractable Value (MEV) 에 대해서 다뤘다. 간략하게 요약하면 MEV 는 블록체인 상에서 발생하는 추가 세금이라고 불리는데, 사용자가 의도치 않게 지불하는 비용이라고 해서 추가 세금이라고 불린다. 그러면 이 세금은 누구에게 갈까? Miner 또는 Searcher 라는 시장 참여자에게 지불을 하는 것이다. Miner는 블록을 생성할 때 tx를 새로 추가하거나 순서를 바꾸거나 검열할 수 있다 [1]. Tx는 블록에 포함되기 전에 pending tx 형태로 공개되어 있는 mempool에 존재하게 되는데 여기서 수익이 되는 tx를 복사해서 Miner가 수익을 챙겨갈 수 있다. 이 행동은 Miner 뿐만 아니라 Searcher 라고 블록체인 상에서 mempool을 지켜보고 있는 주체가 있다. 이들은 이더리움에서 가스비를 많이 지불한 tx가 먼저 처리된다는 점을 이용하여 mempool 에서 수익이 될 수 있는 tx를 찾아서 복사한 후 더 높은 가스비를 지불하고 먼저 처리되도록 한다. 이러한 이유로 이더리움의 mempool 은 Dark Forest 라고 불리고, 이 Dark Forest에는 우리와 같은 선량한 사용자들의 주머니를 노리는 포식자들이 상주하고 있다. 

Geth mempool 코드 (sort by gas fee)
(이미지) 

이를 해결하기 위한 솔루션 중 하나로, 이전 글에서는 Flash Bot 에 대한 소개를 했는데, Flash Bot 외에도 MEV를 줄이기 위한 다양한 시도들이 있어서 정리를 해보았다. 물론 가장 대중화 되어있는 솔루션은 Flash Bot 이긴 하지만, Dark Forest 를 해결하기 위한 다양한 솔루션을 통해서 블록체인은 더 안전하고 더 건강하게 사용하는 시도들을 엿볼 수 있었다. 솔루션은 다음과 같이 3가지로 알아보았다. 
	1. Private Mining Pool
	2. Flash Bot
	3. Secret Network
	
[1] 
https://medium.com/hackless/secure-fund-migration-explained-mev-and-private-mining-for-safemigrate-fef4c0260dad 

```
