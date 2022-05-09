```
출처: https://losslesscash.medium.com/your-intro-into-time-bandit-attacks-on-eth-5a578e3ae6a4

충분히 큰 보상의 tx에 대해서 rewrite를 통해서 tx를 훔쳐오는 공격인데, 이는 MEV를 얻는 전략 중 하나이다.
이러한 시간 도둑 공격을 하기 위해서는 네트워크의 40% 이상의 해시 파워가 있어야 한다. 

Time bandit attack 은 51% attack 과 유사한 점이 있다. 만약에 직전 블록에 매우 큰 수익을 가져다 줄 수 있는 tx가 포함되어 있다면 이더리움의 역사를 다시 쓰고, 이를 mining 함으로써 수익을 가져올 수 있다. 하지만 사실상 40%의 해시파워를 갖고 오는 것은 불가능하다. 대신 다음과 같은 형태의 Time bandit attack은 존재할 수 있다.

Dex 거래를 위한 사용자의 tx들 중에서 buy만 하나의 block 에 넣어둔다, 그리고 그 다음 block에서는 miner 의 tx를 넣어서 의도적으로 arbitrage 수익을 보도록 한다. 
![image](https://user-images.githubusercontent.com/16892436/167397492-0f7c6479-d589-42e6-8d37-c4b3f197ca4e.png)
```
