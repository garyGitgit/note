```

#define MAX_USER      1000
#define MAX_TAG       5000
#define MAX_CHAR      26
#define MAX_LATEST    5
#define MAX_TAG_LEN   8

void insertionSort(int arr[], int cnt)
{
	int temp;
	int i;
	int j;

	for (i = 1; i < cnt; i++)
	{
		temp = arr[i];
		j = i - 1;

		while ((temp < arr[j]) && (j >= 0))
		{
			arr[j + 1] = arr[j];
			j = j - 1;
		}
		arr[j + 1] = temp;
	}
}

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

typedef struct _user {
	int following[MAX_USER];
	int followerCnt;
	int latestMsgs[MAX_LATEST];
	int msgCnt;

	void follow(int userId) {
		following[followerCnt++] = userId;
	}
} USER;


TRIE* _malloc() {
	return &mPool.memoryPool[mPool.cursor++];
}

int getTimeInt(char str[]) {
	return 60 * (10 * (str[0] - '0') + (str[1] - '0')) + (10 * (str[3] - '0') + (str[4] - '0'));
}

TRIE* createTrieNode() {
	TRIE *newNode = _malloc();
	for (register int i = 0; i < MAX_CHAR; ++i) {
		newNode->children[i] = 0;
	}
}

TRIE* searchTrieNode(char hashTag[]) {
	char c;
	TRIE* curr = root;
	while (c = *hashTag++) {
		curr = curr->children[c - 'a'];
	}
	return curr;
}

// run when createMessage
TRIE* insertTrie(char str[], int msgID) {
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

void init()
{
	mPool.cursor = 0;
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
	while (msgData) {
		if (*msgData >= 'a' && *msgData <= 'z') {
			hashTag[idx++] = *msgData;
		}
		else if (*msgData == ' ') {
			hashTag[idx] = 0;
			insertTrie(hashTag, msgID);
			idx = 0;
		}
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
		totalMsgs[cnt++] = users[userID].latestMsgs[i];
	}

	//get followings latest msgs
	for (register int i = 0; i < users[userID].followerCnt; ++i){
		int followUserID = users[userID].following[i];
		for (register int j = 0; j < users[followUserID].msgCnt; ++j){
			totalMsgs[cnt++] = users[followUserID].latestMsgs[j];
		}
	}

	// sort all 
	insertionSort(totalMsgs, cnt);

	for (register int i = 0; i < cnt || i < MAX_LATEST; ++i){
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
