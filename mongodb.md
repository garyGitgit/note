공식 Manual: https://docs.mongodb.com/manual/introduction/

튜토리얼: https://velopert.com/436

적용사례: https://d2.naver.com/helloworld/1016

 

1) Mongo shell 위치
•source /user/socat/AT/utils/env.csh 후 사용



2) 웹 Dashboard 접속 (개발서버 접속 ->  MongoDB-Compass 실행)
•ssh jump01 -l dvlr.jump
•ssh pivy1207 -l vwp.account
•mongodb-compass
•Fill in connection fields individually 클릭
•Hostname 에 10.230.74.24, Port에 27020 입력 후 Connect 버튼 클릭
•at_test_db (Database 이름) -> test_collection (Table 이름) 클릭

3) Python에서 사용하기

 



Example
 
import pymongo
from pymongo import MongoClient
client = MongoClient('mongodb://10.230.74.24:27020/',retryWrites="false")
db = client = client.at_test_db collection = client.test_collection
test_data = {"python_mongo":"true"} collection.insert_one(test_data)
 

 * Pymongo 튜토리얼: https://api.mongodb.com/python/current/tutorial.html

4) Perl에서 사용하기

use lib '/user/antste/mongo/perl_lib' 한 후 사용



Example
 
use lib '/user/antste/mongo/perl_lib';
use MongoDB; # connect to specific host and port 
my $client = MongoDB::MongoClient->new(host => "mongodb://10.230.74.24 :27020/?retryWrites=false" );
my $collection= $client->ns("at_test_db.test_collection");
$collection->insert_one( {"perl_mongo" => "true"});
 

* Perl Mongo 튜토리얼: https://metacpan.org/pod/MongoDB::Tutorial

 

구조

 

서버 역할 - Primary role


이름

IP

Port

역할

설명

pivy1207 10.230.74.21 27018 Shard Server 데이터 저장 
pivy1208 10.230.74.22 27018 Shard Server 데이터 저장 
pivy1209 10.230.74.23 27018 Shard Server 데이터 저장 
pivy1210 10.230.74.24 27019 Config Server 
메타 데이터 저장 (Shard server: data 맵핑).

Replica set name: rs0 (반드시 replica 로 수행되어야 함)
 
pivy1210 10.230.74.24 27020 Route Server Shard sever 로 쿼리 수행 / 쿼리 수행 결과 취합 / Load balancing 

 

예상안 (2021-01-21)






현재 구조  (2021-01-25)



 

 

서버 역할 - Secondary role


역할

 Replica Set Name

IP:Port

Config Server config_set •10.230.74.22:27019
•10.230.74.23:27019
 

 

Setup


Step

To Do

ETC

1 
역할 지정
•Config server 역할을 할 서버 (hostname, port)
•Shard server 역할을 할 서버 (hostname, port)
•Router server 역할을 할 서버 (hostname, port)
 •Config server 는 어느 shard 에 어떤 data가 들어있는지에 대한 meta data 를 가지고 있음.
•Shard server는 실제 data가 저장되는 서버임.
•Router server는 user 와 가장 직접적으로 맞닿는 interface로 user query 를 적합한 shard server 에 저장하고, balancing 하는 역할을 함.
•당연하긴 하지만, a, b, c 서버는 서로 통신 가능해야 함. (firewall check)   
•Official guide에서 권장하는 것은 IP 주소는 바뀔 수 있기 때문에, hostname을 사용해라.
 
2 
Config server 띄우기 - Replica 생성하기 (선택)

 
•Member server 를 0~2 (3개인 경우) 정한다
•각각에 대해서 Configuration file 을 생성한다
•mongod --config file_name 으로 띄운다
•mongo --host --port 로 config 서버 shell 을 띄운다
•shell 안에서 rs.initiate() 를 한다 : initiate 는 replicate set 을 등록하는 작업임. (이 작업을 안 하면 config 가 초기화 되지 않았다는 에러가 나옴)
 •Config server 에서 사용할 configuration file sample 참고 (replica를 사용할 경우. 참고로, config 파일에서 replica 는 필수임)
•그 외 config server 띄우는 전체 과정 예시
 
3 
Shard server 띄우기 - Replica 생성하기 (선택)
•Config server 와 같고, configuration file 은 아래를 참조. replica 는 생성해도 되고, 안해도 된다.
   
4 
Router server 띄우기 (mongos)
•Config server 와 같음.
•Configuration file 에서 configDB 라는 새로운 field 가 생기는데, 여기서 config server 의 hostname와 port 그리고 replica 들의 hostname 과 port 를 적어줌.
   
5 
샤드 추가하기
•mongos 로 들어감
•sh.addShard("replica name/hostname:port") 를 적음.
•Replica 가 없이 standalone 이면 그냥 hostname:port 를 적음.
   
6 
Database 에 sharding 을 적용 
•sh.enableSharding("database")
   
7 
Collection 에 sharding 을 적용
•Hash sharding: 샤드 키로 하나의 field로 hashed index 를 사용함.
•Range sharding: 샤드 키로 multiple field 를 사용. Data를 shard key 에 의해서 결정된 contiguous range 에 넣음.
 
 
 

 

Example

 

Configuration File Example


Config server (with replica)

Shard server

Router server


sharding:

  clusterRole: configsvr

replication:

  replSetName: "config_set"

net:

  bindIp: 10.230.74.22

  port: 27019

storage:

  dbPath: /user/atdb/mongo/data/db/configsvr/rs2

systemLog:

  destination: file

  path: "/user/atdb/mongo/dblog/configsvr/rs2.log"

processManagement:

  fork: true
 
net:

   bindIp: 10.230.74.22

   port: 27018

storage:

  dbPath: /user/atdb/mongo/data/db/shardsvr1

systemLog:

  destination: file

  path: "/user/atdb/mongo/dblog/shardsvr/shardsvr1.log"

processManagement:

  fork: true

sharding:

  clusterRole: shardsvr
 
net:

  bindIp: 10.230.74.24

  port: 27020

sharding:

  configDB: config_set/10.230.74.24:27019,10.230.74.23:27019,10.230.74.22:27019

systemLog:

  destination: file

  logAppend: false

  path: /user/atdb/mongo/dblog/routersvr/mongoRouter.log

processManagement:

  fork: true
 

 

전체 Process Example

1. Mongo 데몬을 띄운다

config server run
•config server replica 0: mongod --config configsvr_rs0.conf
•config server replica 1: mongod --config configsvr_rs1.conf
•config server replica 2: mongod --config configsvr_rs2.conf

shard server run
•shard server 1:  mongod --config shardsvr1.conf
•shard server 2:  mongod --config shardsvr2.conf
•shard server 3:  mongod --config shardsvr3.conf

router server run
•router server:  mongos --config routersvr.conf

2. config 서버를 등록한다

config server
•mongo --host configsvr_hostname:port

•rs.initiate(   {     _id: "<replSetName>",     configsvr: true,     members: [       { _id : 0, host : "cfg1.example.net:27019" },       { _id : 1, host : "cfg2.example.net:27019" },       { _id : 2, host : "cfg3.example.net:27019" }     ]   } )


3. router 에서 샤드 서버를 등록한다
•mongo --host routersvr_hostname:port
•sh.addShard("hostname:port") // replica 있으면 replica set name 도 적어줌

4. router 에서 Database Shard 와 Collection Shard를 등록
•mongo --host routersvr_hostname:port
•sh.enableSharding('test')
•sh.shardCollection('test.chunk_test',{t:1})
•db.mycollection.ensureIndex({field: "hashed"}) // 또는 range 
•db.adminCommand( { shardCollection: "at_testdb01.test_collection", key: { x: 1 } } )

 

 

 

환경 설정 파일 (.conf) 주의할 점

 
◾clusterRole 은 shardsvr, configsvr, routersvr 중 하나로 지정해야한다.
◾mongo 로 config server 에 들어가보면 primary 와 secondary 인지 확인할 수 있음.
만약에 내가 원하는 서버가 primary 가 아니면,  primary 가 되고 싶은 config server 를 두고 나머지는 다 끈다. 
그러면 남아있는 서버가 config server 가 된다. 추가로 priority 를 줄 수 있는데 reconfig 를 하면 됨. 
순서는 cfg = rs.conf() : config 를 가져옴. cfg.members[id].priority = 1 로 설정하고 나머지는 0.5 로 설정해준다. 
여기서 id 는 내가 primary 로 지정하고 싶은 config server 임. 
(주의) 기본 설정이 Primary 인 경우 (Primary Preference) Primay 가 안 떠있으면 접속 조차 못할 수 있음. 

 

셋업할 때 주의할 점
◾기존에 configdb 에 있는 config 내용이 있으면 rs.initiate() 를 해도 이미 initialize 되었다고 나온다. 만약에 새로운 dbconfig 위치에서 하면 rs.initiate() 를 해야지 initialize가 완료될 수 있음. (기존에 떠있는 daemon 을 kill 로 죽이면 비정상적인 종료여서 다시 셋업할 때 잘 안됨. 새로운 configdb 디렉토리를 생성해서 깔끔하게 시작해야 함.  그렇지 않으면 shard 를 등록해도 config primary 를 찾을 수 없다고 나옴. 
◾config 에서도 PRIMARY, SECONDARY 가 지정이 안되어있으면 > 이렇게만 뜬다. 초기화 (rs.initiate()) 를 해야지 PRIMARY 이렇게 생긴다
◾완전 처음에 rs.initiate() 를 한 후 rs.add (host:port)로 secondary config set을 등록할 수 있다. 각각의 secondary config server 에 들어가면  > 이렇게만 뜨는 것이 secondary 로 뜨는 것을 볼 수 있음. 
◾이렇게 정상적으로 등록되면, shard server 도 hang 없이 정상적으로 등록이 되었다고 메시지가 출력됨. config server 를 못 찾는 경우에는 hang 걸린 상태로 남아있음. 
◾마지막으로 router 에서 sh.add 로 shard server 를 등록할 수 있음. 
◾rs 또는 sh 는 각각 mongo shell 에서 .help() 로 자세한 정보를 볼 수 있음 
 

 

 

  


 

 
