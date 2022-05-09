```
메인 내용
https://medium.com/@m.vanderwijden1/backrunning-in-defi-301f3cade30a

선행 매매 (front running) 에 대해 법적으로 잘 설명해놓음 (front running의 의미, 대규모의 거래를 체결하기 전에 개인 적으로 주식을 사놓는 행위)
https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=songpha69&logNo=220917864768 

선취매랑 약간은 다른 의미같음. (선취매는 미리 주식을 구입해두고, 미디어 등으로 가격 상승을 유도한 다음에 덤핑해버리는 것)

Dex에서의 front running: 다른 사람의 tx 를 보고 더 높은 가격의 gas fee를 지불하고 내 tx가 먼저 처리되도록 조치
Dex에서의 back running: arbitrage에서 주로 사용하는 기법으로 Dex에서 bonding curve (수요-공급선)가 변경 되었을 때, 다른 Dex 와의 시세 차이를 이용하여 이득을 봄. 

Dex에서의 front running 과 back running 이 문제가 되는 이유는 무엇일까?
Front running 은 가스비를 높게 하는 부작용을 가져온다
Back running 은 이런 arbitrage가 많아지면, spam 성 tx가 너무 많아져서 (즉, 진짜 거래를 할 수 있는 arbitrage 가 아니라 다른 arbitrage들이 만든 쓸모없는 tx가 많이 생김) network 에 congestion 이 발생하게 되어 일반 사용자들이 피해를 보게 됨. Dune Analytics 에 따르면 backrunning arbitrage bot 때문에 소비된 gas 만 500 billion 이라고 함.

이러한 문제를 해결하기 위해서  miner들은 geth를 업데이트 해야함. 이 geth에서 업데이트 된 내용은 무엇일까? Xxx


EIP-1559 업데이트로 인한 gas fee 감소 효과 (좋은 글이네)
출처: https://www.bitcoinsuisse.com/research/decrypt/arbitrage-and-frontrunning-in-defi
EIP-1559 에서는 gas fee 를 낮추기 위한 방법으로 base fee + tip 구조로 설계했다. 기존에는 높은 gas fee를 지불한 tx가 먼저 처리되도록 하여 블록에 포함되는 방식이었다. 기존 방법은 경쟁적인 tx들을 만듦으로써 높은 gas fee의 원인이 되었고, 이는 일반 사용자들에게 큰 불편함을 주었다. EIP-1559에서는 이러한 bidding 방식을 개선하고, 사용자가 tx를 제출할 때 소모될 수 있는 base fee를 알 수 있도록 했다 (예측 가능한 gas fee). 그리고 일정 금액의 tip을 채굴자에게 줌으로써 채굴자가 우선적으로 tx를 처리할 수 있도록 업데이트했다. 이 업데이트를 통해서 사용자는 gas fee 를 overpay 하지 않게 하는 효과와 더불어 stuck tx의 문제를 해결할 수 있을 것으로 기대됐다. 또한 base fee는 소각이 될 것이며 이는 현재 4.4%의 인플레이션율을 1% 수준으로 낮춰 이더리움의 디플레이션 모델이 될 것임을 암시했다. 

이러한 조치에 대해서 대부분의 투표자들은 동의했으나, 일부는 tx가 miner의 핵심 revenue 라는 점에서 반대했다. EIP-1559 업데이트에 대한 논의는 miner들이 tx fee와 block reward 외에 얻는 추가적인 수익인 MEV에 대한 논의도 진행되었다. MEV는 miner들이 일반 사용자들보다 우선적인 지위를 갖고 있음으로써 얻는 수익으로서 tx를 reordering 하고, inserting 하고 censoring 함으로써 얻을 수 있다. 

예를 들어 아래 그림을 보자 (샌드위치 공격, figure 3 참고)
사용자가 swap 을 한다고 할 때, 이 swap 하려는 tx 사이에 매수 (값을 올려 놓는 매수), 매도 (기존 가격보다 비싼 값에 매도) 를 통해 사용자의 tx가 slippage 를 발생하도록 유도함. 

이러한 샌드위치 공격은 더 높은 gas fee를 지불하거나 max slippage 를 제공을 함으로써 방지할 수 있다. 이러한 샌드위치 공격을 이용하는 것 뿐만 아니라 profit 을 낼 수 있는 tx를 포착하여 이를 그대로 copy 한 다음에 miner 가 더 높은 gas fee를 지불하고 이익을 취하는 Front running  수법이 있다. (에피소드 1 참고)

그러면 누가 MEV 를 extract 하나?
Miner 들이 보통 그런 짓을 하는 것은 아니고 수많은 MEV extraction bot들이 존재한다. 2021년 글 기준, 이 때까지 88%의 총 MEV 가 MEV extraction bot들에게 가고, 12%가 miner들에게 돌아갔다. Top20 tx fee payer 중에 arbitrage, front running, back running bot 들이 포함되어 있는 것을 보면 bot 들이 이더리움의 gas fee 를 높이는 원인 중 하나라고 볼 수 있다.
```
