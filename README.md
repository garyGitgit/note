```
#define MAX_MESSAGE   1000000 + 1
#define MAX_USER      1000
#define MAX_TAG       5000
#define MAX_CHAR      26
#define MAX_LATEST    5
#define MAX_TAG_LEN   8

typedef struct _trie {
	int top5Messages[MAX_LATEST];
	int cnt;
	int terminal;
	struct _trie *children[MAX_CHAR];

} TRIE;

typedef struct _memoryPool {
	TRIE memoryPool[MAX_TAG];
	int cursor;
} MEMORY_POOL;

MEMORY_POOL mPool;
TRIE* root;

typedef struct _msg{
	int time;
	int userID;
} MESSAGE;

typedef struct _user {
	int following[MAX_USER];
	int followerCnt;
	int latestMsgIDs[MAX_LATEST];
	int msgCnt;

	void follow(int userId) {
		following[followerCnt++] = userId;
	}
} USER;

MESSAGE messages[MAX_MESSAGE];



TRIE* _malloc() {
	return &mPool.memoryPool[mPool.cursor++];
}

int getTimeInt(char str[]) {
	return 3600 * (10 * (str[0] - '0') + (str[1] - '0')) + 60 * (10 * (str[3] - '0') + (str[4] - '0')) + (10 * (str[6] - '0') + (str[7] - '0'));
}

TRIE* createTrieNode() {
	TRIE *newNode = _malloc();
	for (register int i = 0; i < MAX_CHAR; ++i) {
		newNode->children[i] = 0;
	}
	return newNode;
}

TRIE* searchTrieNode(char hashTag[]) {
	char c;
	TRIE* curr = root;
	while (c = *hashTag++) {
		if (!curr == 0){ return 0; } //no found

		curr = curr->children[c - 'a'];
	}
	return curr;
}

// run when createMessage
void insertTrie(char str[], int msgID) {
	char c;
	TRIE* curr = root;
	while (c = *str++) {
		int idx = c - 'a';
		if (!curr->children[idx]) {
			curr->children[idx] = createTrieNode();
		}
		curr = curr->children[idx];
	}

	// terminal node
	if (curr->cnt < MAX_LATEST){
		curr->top5Messages[curr->cnt++] = msgID;
		curr->terminal = 1;
	}
	//if full delete the last message and insert a new message
	else{
		for (register int i = MAX_LATEST -1; i >= 0; --i){
			curr->top5Messages[i + 1] = curr->top5Messages[i];
		}
		curr->top5Messages[0] = msgID;
	}
}

USER users[MAX_USER];


void insertionSort(int arr[], int cnt)
{
	int temp;
	int i;
	int j;

	for (i = 1; i < cnt; i++)
	{
		temp = arr[i];
		j = i - 1;

		while (j >= 0 && (messages[temp].time >= messages[arr[j]].time))
		{
			if (messages[temp].time == messages[arr[j]].time && messages[temp].userID > messages[arr[j]].userID){
				break;
			}
			arr[j + 1] = arr[j];
			j = j - 1;
		}
		arr[j + 1] = temp;
	}
}


void init()
{
	mPool.cursor = 0;
	root = createTrieNode();
	for (register int i = 0; i < MAX_CHAR; ++i) {
		root->children[i] = 0;
	}
	for (register int i = 0; i < MAX_USER; ++i) {
		users[i].followerCnt = 0;
	}
}

void createMessage(int msgID, int userID, char msgData[])
{
	int time = getTimeInt(msgData);
	char hashTag[MAX_TAG_LEN + 1];
	int idx = 0;
	users[userID].latestMsgIDs[users[userID].msgCnt++] = msgID; // insert to user
	//insert to messags
	messages[msgID].time = time;
	messages[msgID].userID = userID;

	//insert to time
	msgData = msgData + 10; // skip timestamp
	//insert Trie
	while (1) {
		if (*msgData >= 'a' && *msgData <= 'z') {
			hashTag[idx++] = *msgData;
		}
		else if (*msgData == ' ' || *msgData == 0) {
			hashTag[idx] = 0;
			insertTrie(hashTag, msgID);
			idx = 0;
		}
		if (!*msgData){break;}
		msgData++;
	}	
}

void followUser(int userID1, int userID2)
{
	users[userID1].follow(userID2);
}

int searchByHashtag(char tagName[], int retIDs[])
{
	int res = 0;

	TRIE * node = searchTrieNode(tagName);
	if (!node){ return 0; }

	for (register int i = 0; node->cnt; ++i){
		retIDs[i] = node->top5Messages[i];
	}
	return node->cnt;
}

int getMessages(int userID, int retIDs[])
{
	int totalMsgs[MAX_USER * MAX_LATEST]; 
	int cnt = 0;
	//get user's latest msgs
	for (register int i = 0; i < users[userID].msgCnt; ++i){
		totalMsgs[cnt++] = users[userID].latestMsgIDs[i];
	}

	//get followings latest msgs
	for (register int i = 0; i < users[userID].followerCnt; ++i){
		int followUserID = users[userID].following[i];
		for (register int j = 0; j < users[followUserID].msgCnt; ++j){
			totalMsgs[cnt++] = users[followUserID].latestMsgIDs[j];
		}
	}

	if (cnt == 0){ return 0; }

	// sort all 
	insertionSort(totalMsgs, cnt);

	for (register int i = 0; i < cnt && i < MAX_LATEST; ++i){
		retIDs[i] = totalMsgs[i];
	}
	if (cnt > MAX_LATEST){
		return MAX_LATEST;
	}
	else{
		return cnt;
	}
}

```
