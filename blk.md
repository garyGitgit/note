```
Windows

Go offline 으로 설치하기 위해서 일단 windows 에서 필요한 go 파일들을 모두 다운로드 받는다
Set https_proxy=http://12.26.204.100:8080 
Https 프록시 설정을 한다.

그리고 clone 받은 go 프로젝트 들어가서
Go get -u ./…  를 수행한다 (이건 안먹히는 것 같음)
Go.mod 가 있는 디렉토리 들어가서 Go mod download 를 한다 (뭔가 동작하는듯?) 

$GOPATH
$GOROOT 
이 두가지 환경 변수가 중요

Go get -v githubxx 를 수행하면 GOPATH 아래에 src 아래에 github 에서 설치한 패키지가 설치된다
Go get -u 를 수행하면 어디에 위치할까? 


윈도우에서 설치하는 법 choco 로
https://geth.ethereum.org/docs/install-and-build/installing-geth
 

bootstrap node: 모든 노드가 join 하는 노드 
모든 노드는 entry point 가 될 수 있다. 하지만 모든 노드가 join 할 수 있는 single node 를 bootstrap 노드라고 한다

New account: 

/user/socat/user/sangwoo.noh/.ethereum/keystore/UTC--2022-03-08T04-50-47.485562540Z--bea9f73d661ada7c8981d430d7b5c749dcd09292

--> keystore 에 있는 값을 mnenoic 으로 decrypt 한다음에 private key 를 추출할 수 있다 


Solidity 를 vscode 에서 지원하기 위해서 vscode extension 을 설치헀음. (vscode 도 최신 버전으로 업데이트 진행)

Bootstrap node 추가하는 방법
$geth --datadir data/ --networkid 15 --nat extip:10.230.74.21 --http.api web3,eth,debug,personal,net,miner --mine --http --http.addr 0.0.0.0 --http.port 12345 --http.vhosts '*' console

* http 를 사용하는 것은 unsecure 하다고 한다. 그러면 어떤 방법이 있지? 
https://ethereum.stackexchange.com/questions/69435/error-account-unlock-with-http-access-is-forbidden-when-unlock-an-account-in-ge

Http 로 접근 가능한 경우에는 http 로 들어오는 어떤 사람들간에 account 에 대한 정보를 알 수 있다. 뿐만 아니라 송금을 한다던지 등 불법적인 활동을 할 수 있다
RPC port 를 오픈했을 때 account 를 unlock 하지 말아라. 그러면 이런 취약점을 찾는 봇들이 바로 account 에 있는 잔고를 가져갈 것이다 
여기서 고민해야할 점이 왜 account unlock 이 필요한가, unlock 하지않을 방법은 없는가 고민을 해봐야한다 


Regular node 를 추가하는 방법
 $geth --datadir data2/ init metadata/genesis.json

 $geth --datadir data2/ --networkid 15 --http.port 30305 --bootnodes enr:-KO4QEgB46rPk6tFY5RkZnVIxiRSq6epWh5cAbdj4hqjNW4XJLZJhinnPq7pvrtNINsEDzFJ7XsWXF3ugfjJtUxZ-MuGAX9pGaNjg2V0aMfGhNyUGjqAgmlkgnY0gmlwhArmShWJc2VjcDI1NmsxoQMSkp1EU07CYTjRaUgMHjlccREb_qmk-C_gjsYP0p4V7IRzbmFwwIN0Y3CCdl-DdWRwgnZf console --mine --http.api web3,eth,debug,personal,net,miner --mine --http --http.addr 0.0.0.0 --http.vhosts '*'

--mine 이라는 옵션은 안 먹는 것 같은데, miner.start() 를 사용하면 시작한다 

Websocket 으로 연결하는 방법
$geth --datadir data3/ --networkid 15 --ws.port 12345 --bootnodes enr:-KO4QEgB46rPk6tFY5RkZnVIxiRSq6epWh5cAbdj4hqjNW4XJLZJhinnPq7pvrtNINsEDzFJ7XsWXF3ugfjJtUxZ-MuGAX9pGaNjg2V0aMfGhNyUGjqAgmlkgnY0gmlwhArmShWJc2VjcDI1NmsxoQMSkp1EU07CYTjRaUgMHjlccREb_qmk-C_gjsYP0p4V7IRzbmFwwIN0Y3CCdl-DdWRwgnZf console --mine --ws.api web3,eth,debug,personal,net,miner --mine --ws --ws.addr 0.0.0.0


Peer 는 추가됐는데 왜 block number 는 다를까? 같은 block number 를 봐야하지않나?
Mine 을 시작하니까 등록되었음 



Node 의 종류
Node 에도 여러가지 종류가 있다: local node vs hosted node 
Local node
	- geth 또는 parity 를 수행했을 때 나에 의해서 컨트롤 되는 노드이기 때문에 이를 local node 라고 부름
	- Local private key는 32 byte 길이이고, tx 나 msg 에 sign 하는 용도로 사용한다.
web3 에서는 send_raw_transaction() 을 수행해야지 local key 랑 같이 보낼 수 있다. Send_trasnaction 은 local key 를 사용하지 않는다
	
Hosted node
	- 다른 사람에 의해서 컨트롤 되는 노드를 hosted node 라고 부른다. 예를 들면, Infura 는 Hosted node 라고 볼 수 있다.
	- Account 를 생성할 때 key 가 같이 return 된다. 따라서 send_transaction 을 그냥 사용하면 된다 
	

Local node 는 host 보다 낮은 신뢰성을 요구한다. Hosted node 는 악성일 수 있기 때문에 높은 신뢰성을 요구한다. 


노드와 네트워크를 선택했으면, 어떻게 연결할 것인가를 결정하면 된다. 다음과 같은 세 가지 방법이 있다
	1) IPC: local file system 을 사용하고, 가장 빠르고 가장 안전함
-> 하나의 머신에서 여러개 노드를 띄울 때 사용 
	2) Web socket: remote 로 동작하고 HTTP 보다 빠름
-> 다른 컴퓨터랑 interface 할 때 사용
	3) HTTP: more node 를 지원
-> web socket 이 지원되지 않는 PC 인 경우 HTTP 를 사용 
	
	
HTTP 와 web socket 의 차이점
	1. HTTP 는 stateless, web socket 은 stateful 한 connection 
	2. Web socket 은 real-time application 개발에 널리 사용됨 (예를 들면, google doc, 증권거래 emd) 
왜 빠를까? web socket 은 header 의 크기가 상당히 작기 때문에 overhead 가 작은 특징이 있다. 또한 서버와 클라이언트가 연결되어 있는 상태이기 때문에 클라이언트 쪽에서 요청하지 않아도 서버 쪽에서 데이터를 제공해줄 수 있다. 
	
Web socket 의 동작 원리
	- 처음에 연결할 떄는 http 프로토콜을 사용하지만, 연결이 정상적으로 이뤄지면 http 연결은 자동으로 끊어진다. 
	

	
```
	

```
Ganache를 설치한다 -> 간단한 single node network 를 구축해서 smart contract 를 테스트 등 
Geth 를 설치한다 -> Eth 네트워크를 구성한다 (single node, multi node 구축)
Web3.py 를 설치한다 -> Python 으로 네트워크와 통신하기 위한 라이브러리 (send_transaction, contract 와 같은 작업 수행) 
Solc 를 설치해서 bin 과 abi 를 가져올 수 있도록 한다 (remix 가 하는 역할) 
Geth 노드들을 모니터링하는 방법 ?  Private network 를 위한 etherscan? 
	- 딱히 나와있지 않는 것 같다. 또는 너무 오래된 프로젝트들. 아직은 굳이 그정도는 아닐 것 같긴한데, 결국 private network 를 위한 block explorer 가 필요할 듯 한다. 일단 지금은 web3 py API 를 통해서 CLI 로 개발을 하도록 하자 
		○ 어떤 기능이 필요할까?
			§ 특정 address 에 대한 trasnaction history 
			§ 모든 address 에 대한 transaction history
			§ Contract 로 filtering ?
			§ 특정 address 에 대한 balance 확인
			§ 모든 address 에 대한 balance 확인
		



Remix offline 버전을 설치한다 -> offline 으로 smart contract 배포를 간단하게 할 수 있다
Truffle 을 설치한다 -> smart contract 를 개발하기 위한 framework 를 구축 (solc.js 도 같이 설치해주나) -> javascript 용인 것 같음 
Metamask 를 offline 으로 설치한다 -> offline metamask 사용 (근데 그냥 CLI 로 구현해도 될듯, chrome 을 새로운 버전으로 또 깔아야하는데 이게 금방 안되는 것 같기도 하고,,)


이제 Ganache 에 geth 를 연결해서 내가 구축한 private 한 network 에서 잘 동작을 하는지 확인을 해본다 
```

```
1. private network 구축
	- Genesis.json 파일 필요
	- Bootstrap node 가 필요 
	

출처: https://ethereum.stackexchange.com/questions/8948/difference-between-full-node-and-bootstrap-nodes-in-ethereum# 
일반적인 경우에도 bootstrap node 에서 데이터를 받아올까? 
그렇지 않다. Regular node 들은 bootstrap node 에서 데이터를 받아오는 것이 아님. 다만 bootstrap node 를 통해서 다른 regular node 들의 정보를 갖고 오고 다른 regular node 들을 통해서 데이터를 가져온다

Bootstrap node 는 geth 에서도 하드코딩 되어 있음. (enode: xxx)  그리고 이 노드들은 regular node 에 대한 매우 적은 양의 정보만 갖고 있음. 

만약에 boostrap node 가 잘못된 정보를 갖고 있다면? 
특정 bootstrap node 를 지정할 수 있음. 
내가 특정 peer 를 add 할 수 있기 때문에 잘못된 peer 가 있으면 고칠 수 있는 부분이다 (신뢰를 하지 않아도 된다는건가?) regular node 로부터 peer node 정보를 가져올 수 있음, 

Regular node 와 boostrap node 와의 차이점은?
Regular node 의 역할 중 하나는 peer discovery mechanism 이 있고, 데이터를 다운받게 해주는 역할을 한다. Peer 들과 연결을 하면 그 다음에는 boostrap node 와의 연결을 끊는다


```


```

분산시스템 기반으로 분산하여 저장하고 인터액션한다 (RPC 기능을 활용)
Signature 를 사용할 때는 암호학으로 안전하게 보관
Malicious 한 attacker 가 있는 경우 어떻게 해결할 것인지에 대한 고민 (consensus, game theory  등) 




Layer in blk 
	- Data layer (pointer and linked list + merkle tree) 
	- Network layer (p2p)
	- Consensus layer
	- Application layer (dapp)
		○ Application layer : user 랑 가장 맞닿아있는 부분 (script, API 등) 
		○ Execution layer: smart contract (chain code) 가 동작하는 부분 

Blk : cryptography (public key, private key), game theory (mathematical models of strategic interaction among rational decision-makers), distributed systems (RPC, BFT), economy (tokenomix) 
```


```
hackmd structure

스터디 카테고리

	1. 스터디 내용
		a. Phase 0: 블록체인 개발에 참여하는 방법 - 크게 보면 총 4가지 + a 측면에서 볼 수 있고, 각각이 어떤 역할을 하는지 알아보자 
		b. Phase 1
		c. Phase 2
		d. Phase 3

	1. Defi 
		a. Defi 1.0
			i. Luna - Anchor
		b. Defi 2.0
			i. Olympus Dao 
			ii. Curve
	2. 보안
		a. 이더리움의 보안
			i. 
		b. 솔라나의 보안
			i. 솔라나 보안 이슈 터진 뉴스 공유
	3. 기술
		a. Layer 2 네트워크의 기술적인 이야기
		b. Eth 나 Klay 와 같은 on-chain 상에서 랜덤이 어려운 이유 (간단함. On-chain 에는 seed 값이 나오기 때문에 예측 가능하다. 따라서 off-chain 에서 seed 를 주고 random함수를 가져오는 형태로 수행한다) 
		c. 보안
			i. 이더리움의 보안: 비탈릭 부테린의 보안에 대한 레딧 글 정리
			ii. 솔라나의 보안: 웜홀 이슈 
	4. NFT
		a. 재밌는 NFT 소개
			i. 메타콩즈
			ii. 메타토이 드래곤즈
			iii. 갈라
			iv. 스텝엔
			v. 클레이 엠퍼
	5. 토의사항
		a. 다단계와 NFT 시장: pros and cons
		b. NFT 의 미래: 문제 상황 발생과 문제 발생시 대응 내용
		c. 왜 블록체인일까에 대한 견해
	6. 유용한 사이트들과 그 목적 (언제 쓰면 좋을지) 
	7. 최신 뉴스
		a. Times 에 올라온 
	8. 어쩌구 저쩌구 잡단한 히스토리 기록: 예를 들어 Klaymfer 구입 및 특정 NFT 구입 등 적어도 되고 안 적어도 되고 마음대로~
		
		
기술적으로 구현할 부분
	- Github master branch 에 push 가 되면 (즉, 글이 업데이트가 되면) 이메일이 날아오도록 설정하자
![image](https://user-images.githubusercontent.com/16892436/159154259-5e9c4286-0650-474a-b8b5-6f6522774b58.png)



```

```
20220323 스터디 주제

1. Pi Network 에 대한 이야기. Pi Network 는 Legit 인가 Scam 인가에 대한 의견
기사 읽어보기

2. Hackmd 에 정리한 크립토 관련 내용 및 스터디 내용 공유
* 참고로 지금 스터디 내용을 시간 순으로 정리하면 나중에 찾아보기 힘들테니 분류를 해서 저장을 하자 
* 루나와 앵커에 대한 이야기가 없다. 여기는 빈 페이지로 두거나 약간 찾아봐서 채우고 나서 다른 스터디원도 컨트리뷰션하도록 하자.
* 올림푸스 다오에 대한 내용도 마침 논스클래식에서 나왔으니까 읽어보자
* 유용한 사이트 소개와 그 이유에 대해서도 적어보자

3. 개발 과정
* GUI / CLI 로 개발한 크립토 스터디 클럽의 node 런치 시스템 공개 (vwp 내에서 사용가능)

4. 파생 상품 (전체 TVL 이 증가하는 것은 의미가 없을 수 있다. 마치 서브프라임 모기지 사태의 파생상품 처럼) 과 다단계 경제의 함정 (올림푸스 다오를 통한 고민) 

기타 궁금한 내용
	- 스터디 이름 (가칭. DCC / 픽셀 아트로 우리를 대표할 수 있는 이미지가 있으면 재밌겠다)
	- 스텝엔 진행 상황
	- Klaymfer 참여 이야기 (2개 매수)
	- 브레이브 콩즈 이야기, 메타토이드래곤즈에서 런칭한 두 번째 프로젝트 (메타토이게이머즈 - 게임 쪽으로 나가겠구나) 
![image](https://user-images.githubusercontent.com/16892436/159156389-bdab1779-e48e-43c1-a4b8-c2ceac5fb255.png)

```
```
one klay bookstore
One Klay Bookstore (OKB) 

목적: 
누구나 책을 발행하고 누구나 잘 정리된 고급 정보를 얻고 그리고 후원하고 소득을 분배받는다, 그리고 거버넌스에 참여한다

책을 무단으로 복사하거나 캡처하거나 하는 등의 작업은 못하도록 방지한다
오히려 출처를 명시함으로써 거기에 대한 보상을 주는 방식으로 확실한 출처 표기를 하도록 한다 


Smart contract 종류
	1) Mint governance NFT
		a. Send token from buyer to developer (send token)
		b. Send NFT from developer to buyer (send nft ->실제로 넘어가는 것은 string 일까? Nft url --> 그러면 실제 이미지는 어디에 저장되나 ? IPFS?, 아직 JPG 를 저장하는 기술은 안나왔을라나? 그리고 그걸 저장하는게 또 어떤 의미가 있으려나?) 
			i. NFT 의 구성 요소 (https://docs.ipfs.io/how-to/mint-nfts-with-ipfs/#a-short-introduction-to-nfts)
				1) Unique id (<-> fungible token, ex. ETH) 
				2) Smart contract 외부에 있는 데이터 (off-chain) 와 연결할 수 있음. 즉, 거대한 용량의 데이터를 smart contract 에 저장하는 것은 엄청나게 비싼 비용을 수반함
			ii. IPFS 가 하는 역할
				1) 기존의 HTTP 가 갖고 있는 기술적인 한계
					a) 서버에서 이미지를 갖고 오는 경우, 그 이미지가 민팅했던 그 이미지였다고 하는 보장이 없음. 한 예로, 민팅한 후 이미지를 바꿔버린 러그풀 사례가 있음
				2) 이를 해결하기 위해서 HTTP 기반이 아니라 content addressing 을 사용함. Content addressing 이란 CID (Content ID) 를 부여하고, 이 ID 에 기반하여 link를 전달하게 된다. 링크가 변경되었다는 것은 content 가 변경되었다는 것이랑 같은 의미이기 때문에 바로 데이터의 진위여부를 파악할 수 있다는 장점이 있다. 
				3) 네트워크 상 
	2) Publish books (round 1, round 2 …, round K)
		a. Send token from author to treasury pool
		b. Send books from develop to author
		c. Distribute treasury pool (N %) to governance users (monthly)
	3) Buy a book 
		a. Send token from buyer to author
		b. Send book from author to buyer
	4) Publish author book NFT
		a. Send token from author to developer
		b. Send NFT from author to developer
	5) Mint Author / Book NFT governance token
		a. Send token from buyer to author
		b. Send NFT from author to buyer
		c. 

```

```
논스 클래식 강좌 내용

Curve

전체 TVL 수치는 폭발적으로 증가하는데 그 수치를 믿을 수 있느냐에 대한 이야기
예를 들어 A <- B <- C <- D <- F 이렇게 추정을 한다고 하면, 각각 TVL 을 하는데 실제가치는 1인 반면 5의 가치를 넘어버릴 수 있다고 함. 
![image](https://user-images.githubusercontent.com/16892436/159156422-e4dc9592-8a03-4c1f-9f9b-f332cb36d6bd.png)


Olympus Dao
올림푸스 다오를 통한 Defi 2.0 에 대해서 (논스 클래식) : 경제 공학적인 메커니즘 (논스클래식) 

거래소 프로토콜과 외부 유동성에 따라서 유동성 제공
외부가 아닌 프로젝트 유동성을 스스로 공급
	- 옴 토큰을 스테이킹을 하고 스테이킹하면 큰 혜택을 받을 수 있도록 구조를 설계 
	
채권을 발행
	- 올림푸스 다오에서 채권은, 옴 토큰 보다 더 저렴한 가격으로 매수 할 수 있도록 함 (선물같은 개념) 
	
옴토큰이 하는 일
	- 거래하는 용도는 아니고, 기존의 여러 자산을 묶어서 가치를 기반으로 교환이 되는 새로운 자산
	- 스테이블 코인의 경우 화폐의 가치가 변하면 그대로 따라간다는 단점이 있음.
	- 달러페깅은 연준의 양적완화 정책에 따라서 가치가 떨어지지 않나? 이러한 문제점에 기반해서 옴 토큰이 발생하게 됨.
	- 왜 그러면 채권? 연준처럼 채권 발행 (미국이라는 건실한 신용을 기반으로 발행하는 것) --> 올림푸스다오도 채권을 비슷한 방식으로 발행한 것. 그렇다면 올림푸스의 신용은 무엇일까? 
	- 올림푸스 다오는 채권을 판매해서 얻은 수익으로 옴을 더 발행한다. 즉 1 옴을 100DAI 에 판매를 했다고 했을 때, 1달러 = 1옴이라는 지지가격이 있을 때, 지지가격이 지켜지기 때문에 100 옴을 발행한다. 새로 발행한 옴을 스테이킹하고 있는 사용자에게 약 90% 이상을 수익으로 돌려준다. 
	- 진짜 돈은 treasury 에 넣고 (금), 추가 발행한 토큰을 수익을 배분한다 (이것이 APY 가 압도적으로 높은 이유
	- 자동으로 스테이킹을 함 (지갑에 다시 넣어주기) 
	
(3,3) 한 때 유행했던 용어 - 죄수의 딜레마

	
큰 특성
	- Treasury
	- 채권
	- Rebase (복리효과 이자)
	- Game theory (3.3)
	

![image](https://user-images.githubusercontent.com/16892436/159156428-759c731b-2b07-4504-b9bc-afa43ba13cae.png)

```

```
유용한 사이트 (정보 얻을만한 사이트)


정보를 얻을만한 사이트

국내

논스클래식 - 코인 & 최근 nft 소식
코인버펫 - 최근 nft 소식
메타 nft ? - 최근 nft 소식
김단테 - steve lee
알고란 - ? 



해외
Crypto univ? 

![image](https://user-images.githubusercontent.com/16892436/159156503-73127a8a-ac4b-4104-b905-22d7149b1b99.png)

```
