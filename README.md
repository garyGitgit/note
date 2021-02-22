```
#define MAX_USER      1000
#define MAX_TAG       5000
#define MAX_CHAR      26
#define TOP5          5
#define MAX_TAG_LEN   8

// TRIE

typedef struct _trie {
	int mID;
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
	int cnt;

	void follow(int userId) {
		following[cnt++] = userId;
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
TRIE* createTrie(char str[], int mID) {
	char c;
	TRIE* curr = root;
	while (c = *str++) {
		int idx = c - 'a';
		if (!curr->children[idx]) {
			curr->children[idx] = createTrieNode();
		}
		curr = curr->children[idx];
	}

	curr->mID = mID;
}

USER users[MAX_USER];
void init()
{
	mPool.cursor = 0;
	for (register int i = 0; i < MAX_CHAR; ++i) {
		root->children[i] = 0;
	}
	for (register int i = 0; i < MAX_USER; ++i) {
		users[i].cnt = 0;
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
			createTrie(hashTag, msgID);
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
	return 0;
}

int getMessages(int userID, int retIDs[])
{
	return 0;
}


```
