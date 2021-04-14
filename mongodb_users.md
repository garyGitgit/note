
Index



 

들어가기 전에

알아야 할 것
◾Python 위치: /user/athome/tools/Anaconda/bin/python

 

SQL 과 용어 비교
◾출처: https://docs.mongodb.com/manual/reference/sql-comparison/

 

* primary key: In MongoDB , the primary key is automatically set to _id (object id) field.

주의해야 할 것
◾Atomicity: Write 은 document 단위로 atomic 하게 수행
◾Denormalized data: RDB 와 다른 개념이기 때문에 각 Collection(Table) 을 관계로 엮으려고 하면 안된다. (lookup 으로 join 과 같은 기능은 있음)
◾Embedded data model:  https://docs.mongodb.com/manual/core/data-model-design/#embedded-data-models


 

 

시작하기

데이터베이스 생성

 

1) MongoDB Compass 열기

> # pivy1207 로 접속
> mongodb-compass
 

 

2) Mongo DB 접속

> # Fill in connection fields individually 클릭
> # Hostname 에 10.230.74.24, Port에 27020 입력
> # Connect 버튼 클릭
 

 



 

3) Database 와 Collection 생성 (= database 와 table 생성하는 작업)

 

> # Fill in connection fields individually 클릭
> # Hostname 에 10.230.74.24, Port에 27020 입력
> # Connect 버튼 클릭
 



 

 

CRUD Operation

0. 준비

 
◾데이터 형식: JSON 
◾Pymongo 튜토리얼: https://api.mongodb.com/python/current/tutorial.html

 



Example
 
import pymongo
from pymongo import MongoClient
client = MongoClient('mongodb://10.230.74.24:27020/',retryWrites="false")
db = client.at_test_db collection
collection = db.test_collection
test_data = {"python_mongo":"true"} 
collection.insert_one(test_data)
 

 

1. Create

 



Insert
 
test_data = {"python_mongo":"true"} 
collection.insert_one(test_data)
 

 

2. Read


Select
 
# select all
collection.find()
 
# select with condition
# select * from table where name = "sangwoo.noh"
condition ["name"] = "sangwoo.noh"
collection.find("$and", condition)
 

 

3. Update



Update
 
my_query = {"python_mongo":true}
new_value = {"$set", {"python_mongo": false}}

 
# update table set python_mongo = false where python_mongo = true
collection.update_many(my_query, new_value)
 

 

4. Delete

 



Delete
 
condition = {"python_mongo":true}

 
# delete table where python_mongo = true
collection.delete_many(condition)
 

 

 

 

Perl
◾TBD

 

Use case

1. DVLR CLI 사용자 로그
◾Daily 수행
◾Document: 커맨드 한 번 수행 시 발생하는 로그
◾Field: timestamp, REQUESTER, TYPE, DEBUG, TYPE, WARNING, SUCCESS, ERROR
◾목적: CLI 사용자 로그를 기록하고 특정 시간대, 특정 사용자 등 특정 조건에 의해서 로그 데이터를 가져올 수 있게하기 위함.



2. DVA job 별 로그 저장  
◾일정 주기 수행
◾Field: itemId, jobId, seqId, curr, info, last, path
◾목적: CLI 에서 사용자에 필터링된 DVA job log 를 보여주기 위함.

 



 

3. Attribute 파일 관리
◾요청 시 수행
◾Document: DB 화 시킬 엑셀 파일
◾Field: project, version, project_map_id, requester, file_path, revision
◾목적: 기존에 Attribute 파일 관리를 위해서 3개의 Table 로 관리하는 어려움이 있어 하나의 Document 로 관리하는 것이 용이할 것으로 판단함.

 

 

        

 

 

 

 

 

 
