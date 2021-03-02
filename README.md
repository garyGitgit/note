```
주제: 그룹별로 일정을 등록하고 삭제하는 문제
그룹별이 핵심임. 
근데 그룹별로 일정을 저장할 필요는 없음.
Trie 를 각각 갖고 있으면 메모리 낭비임. 

핵심 자료구조

일정 이름으로 구성된 TRIE
TRIE 의 말단 노드에 저장될 자료구조: group id (100) x user list (5), count

각 user 가 어떤 group id 에 속하는지 배열 (10000) 의 링크드 리스트

addEvent:
해당 ename 으로 trie 탐색해서 group id 위치에 있는 user list 에 uid 추가 count ++
(0번째 위치는 master 아니면 normal)

deleteEvent:
ename 으로 trie 탐색해서 uid 를 찾음 (uid 가 속한 그룹을 갖는 자료구조도 필요)
uid 가 속한 group 들 리스트를 전부 가져와서 gid 로 user list 탐색
uid 가 발견되면, master 인지 normal 인지 비교해서 모두 삭제할지, 하나만 삭제할지 결정
master 이면 count =0 으로 바꾸고, normal 이면 count -1 하고, 빈칸 하나씩 땡긴다 (그래봤자 5개)

changeEvent:
deleteEvent 를 하고 addEvent 를 하면 됨.
master 인 경우 addEvent 를 그 아래 있던 event 들 수만큼 진행

getCount:
user id 배열에서 count 도 갖고 있어서 event 를 add 하고 delete 할 때마다 count 를 줄이고 늘림


```

```cpp

//void mstrcpy(char dst[], const char src[]) {
//	int c = 0;
//	while ((dst[c] = src[c]) != 0) ++c;
//}

//int mstrcmp(const char str1[], const char str2[]) {
//	int c = 0;
//	while (str1[c] != 0 && str1[c] == str2[c]) ++c;
//	return str1[c] - str2[c];
//}

# define MAX_USER 10000
# define MAX_GROUP 100
# define MAX_CHAR 26
# define MAX_EVENT 5

typedef struct _user{
	int groups[MAX_GROUP];
	int groupCnt;
	int eventCnt;
} USER;

USER users[MAX_USER];

typedef struct _trie{
	int events[MAX_GROUP][MAX_EVENT];
	int eventsCnt[MAX_GROUP];

	struct _trie *children[MAX_CHAR];

	void add(char *str){
		TRIE *ptr = root;
		for (; *str; str++){
			int idx = *str - 'a';
			if (!ptr->children[idx]){
				children[idx] = new TRIE();
			}
			else{
				ptr = children[idx];
			}
		}
	}

	void clear(){
		for (int i = 0; i < MAX_CHAR; ++i){
			if (!children[i]){
				children[i]->clear();
			}
		}
	}
} TRIE;

TRIE *root = 0;

void init()
{
	if (!root){
		root.clear();
	}
	root = new TRIE();
}

void addEvent(int uid, char ename[], int groupid)
{
}

int deleteEvent(int uid, char ename[])
{
	return 0;
}

int changeEvent(int uid, char ename[], char cname[])
{
	return 0;
}

int getCount(int uid)
{
	return 0;
}

```
