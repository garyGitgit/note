블록을 생성하는 채굴자들의 정답 (논스값) 은 블록 해시값이 다르기 때문에 다르다. 
왜냐하면 블록 해시값은 블록 내용으로 결정이 되는데, 예를 들어 하나의 블록을 생성하는 트랜잭션들을 보면 채굴자마다 다 다르다 

만약에 두 채굴자가 동시에 블록을 전파한다면?
블록을 받는 다른 풀노드 (+채굴자) 입장에서는 먼저 들어온 블록을 가장 최신이라고 생각한다. (블록 넘버는 같으니까 그 이후에 들어오는 블록은 무시하려나) 하지만 전체 네트워크 입장에서는 반은 A 블록을 반은 B 블록을 갖고 있을 수 있고, 이는 일시적인 포크를 유발한다. 

둘 중에 어떤 체인이 선택되냐는 분기 이후  가장 긴 체인이 선택된다고 보면 된다. 

비트코인이 기존 시스템과 비교했을 때 나은 점
신용카드는 지불자에 대한 너무 많은 정보를 제공한다. 예를 들면, 카드 뒷자리 3자리나 나의 주소 등을 제공한다. 특히 카드 뒷자리 3자리를 제공하는 것이나 카드 번호를 제공하는 것은 사실상 나의 private key 를 넘기는 것과 같은 행위이다. 

비트코인은 디플레이션 머니이기 떄문에 사람들이 유통하지 않고 보관만 할 것 이다 라는 주장
Saver 들은 spender 와 경쟁하지 않는다? 

51% attack 
만약에 자신이 지불하고 그 돈을 이중지불하기 위해서 엄청난 파워를 쓰는 공격자가 있다면, 차라리 그정도 파워로 비트코인을 채굴하는게 더 이득일 것 이다. 51% attack 한다고 갑자기 부자가 되는 것도 아니고, 고작해야 자신이 지불했던 것에 대한 이중 지불일 뿐이다. 
"The Bitcoin network might acutually reduce spam by diverting zombie farms to generating bitcoins instead"

Pure peer to peer network
	1) Gnutella :  P2P 파일 공유 네트워크를 구성하기 위한 분산 소프트웨어 프로젝트 
	2) Tor: 온라인 상에서 익명을 보장하고 검열을 피할 수 있게하는 자유 소프트웨어 (브라우저) 
	
국가가 재정 적자를 메우는 방법에는 3가지가 있다
	1) 돈을 발행하는 것
	2) 돈을 빌리는 것
	3) 세금을 징수하는 것
	
특정 트랜잭션이 채굴자들에게 무시당한다면 이 트랜잭션은 운에 의존하여 트랜잭션이 블록에 포함될 떄까지 기다려야 하는가? 
그렇다. 노드들은 블록 안에 트랜잭션이 포함될 떄까지 기다려야 한다.

만약에 노드가 두개이상의 체인을 보유하고 있다가 체인 B 에 지불되었던 것이 체인 A 로 이중 지불 되었을 때는 어떻게 되는가? 이런 것을 체크하는 기능이 있는가? 
이런것은 체크할 필요가 없다. 어느쪽 체인에 있던지 간에 그 트랜잭션은 유효하게 되고, 다른 하나의 체인은 무효화된다. 트랜잭션을 받은 쪽의 사람은 적어도 1시간은 기다려야지 확실해진다. 그렇게 이중 지불하는 사람은 즉각적으로 코인을 이중지불 할 수있지만 적어도 어떤 제품을 인도받는데는 시간이 걸린다.

트랜잭션이 유효한지 체크하기 위해서는 얼마나 이전까지 돌아가서 체크해야 되냐
이전 2개의 트랜잭션만 확인하면 충분하다 

트랜잭션은 한 사이클 뒤에나 여러 사이클 뒤에나 문제가 될 건 없다. 결국에는 포함될 것 이다. 
It s not a problem if transactions have to wait one or a few extra cycles to get into a block

이미 블록에 포함되어 있는 트랜잭션은 포함 안 시키고 다른 트랜잭션을 포함시킬 것이다
Any nodes that had the other transaction will stop trying to include it in a block, since it's now invalid according to the accepted chain.

시뇨리지 (seigniorage) 주조 차익이라 하여 중앙은행이나 정부에서 은형권과 경화 등 화폐를 발권함으로서 얻는 수익을 말한다. 화폐 주조 차익, 인플레이션 조세 (inflation tax) 라고도 부르는데 이는 중세 시대의 봉건 영주에서 따온 말이다

예를 들어 1만원짜리 지폐를 발권하고, 2천원의 비용이 들었다면 8천원의 차액만큼 이득을 보게 된다. 

비트코인 지불하는 사람이 오랜 시간동안 기다려야하나
Instantant non-repudiability is not a feature, but it’s still much faster than existing systems. Paper cheque (종이 수표) can bounce up to a week or two leter. Credit card transactions can be contested up to 60 to 180 days later. Bitcoin transactions can be sufficiently irreversible in an hour or two

하나의 채굴 노드가 모든 spends 를 거절한다면 어떡하나
With the transaction fee based incentive system I recently posted, nodes would have an incentive to include all the paying transactions they receive

The proof-of-work chain is a solution to the Byzantine General's Problem. I ll try to rephrase it in that context. 
A number of Byzantine Generals each have a computer and want to attack the King's wi-fi by brute forcing the password, which they ve learned is a certain number of characters in length. (…) They only have enough CPU power to crack it fast enough if a majority of them attack at the same time

만약에 해시 문제를 풀고 있는 동안 다른 트랜잭션이 들어온다면 이들은 transaction pool 에서 대기하게 된다. 

별도의 pending transaction list 가 존재해야 하지 않을까?
When a new block arrives for the best best branch, ConnectBlock removes the block's transactions from the pending tx-pool. If a different branch becomes longer, it calls DisconnectBlock on the main branch down to the fork, returning the block transactions to the pending tx-pool., and calls ConnectBlock on the new branch, sopping back up any transactions that were in both branches. 

만약에 네트워크 상황이 안좋아서 느리게 전파된다면?
Broadcasts will probably be almost completely reliable. TCP transmissions are rarely ever dropped these days and the broadcast protocol has a retry mechanism to get the data from other nodes after a while. If broadcast turn out to be slower in practice than expected, the target time between blocks may have to be increased to avoid waste resources. (…) I m plnning to run an automated test with computers randomly sending payments to each other and randomly dropping packets. 

비트코인은 사회적으로 유용하고 가치가 있고 노드 운영자들은 그들이 선의의 기여를 할 것으로 생각이 든다
In this case, it seems to me that simple altruism can suffice to keep the network running properly. It is very attractive to the libertarian viewpoint if we can explain it properly. I'm better with code than with words though.





 
