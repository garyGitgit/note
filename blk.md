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
