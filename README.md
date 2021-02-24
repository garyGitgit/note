#define MAX_USER      1000
#define MAX_POST      100000
#define MAX_LATEST    10

int MY_N;

typedef struct _follower {
	int id;
	int timestamp;
} FOLLOWER;

typedef struct _post {
	int id;
	int timestamp;
	int like;
} POST;

POST posts[MAX_POST];

typedef struct _user {
	FOLLOWER following[MAX_USER];
	int followerCnt;
	POST latestMsgIDs[MAX_LATEST];
	int msgCnt;

	void follow(int userId, int timestamp) {
		following[followerCnt].id = userId;
		following[followerCnt].timestamp = timestamp;
		++followerCnt;
	}
} USER;

USER users[MAX_USER];
int currentTime = 0;

int compare(POST a, POST b) {
	if (currentTime - a.timestamp <= 1000 && currentTime - b.timestamp <= 1000) {
		if (a.like != b.like) { return a.timestamp < b.timestamp; }
		return (a.like > b.like);
	}
	else if (currentTime - a.timestamp <= 1000 && currentTime - b.timestamp > 1000) {
		return 0;
	}
	else if (currentTime - a.timestamp > 1000 && currentTime - b.timestamp <= 1000) {
		return 1;
	}
	else {
		return (a.timestamp < b.timestamp);
	}
}

// postid index
void insertionSort(int arr[], int cnt)
{
	int temp;
	int i;
	int j;

	for (i = 1; i < cnt; i++)
	{
		temp = arr[i];
		j = i - 1;

		while ((j >= 0) && compare(posts[temp], posts[j]))
		{
			arr[j + 1] = arr[j];
			j = j - 1;
		}
		arr[j + 1] = temp;
	}
}


void post(int userID, int postID, int timestamp) {
	posts[postID].like = 0;
	posts[postID].timestamp = timestamp;

	//handle user
	//if full, discard old one

	if (users[userID].msgCnt == MAX_LATEST) {
		int tmp[MAX_LATEST + 1];
		tmp[0] = postID;
		for (register int i = 1; i <= MAX_LATEST; i++) {
			tmp[i] = users[userID].latestMsgIDs[i - 1].id;
		}

		insertionSort(tmp, MAX_LATEST + 1);

		//copy
		for (register int i = 0; i < MAX_LATEST; i++) {
			users[userID].latestMsgIDs[i] = posts[tmp[i]];
		}
	}
	else {
		users[userID].latestMsgIDs[users[userID].msgCnt].id = postID; // insert to user
		users[userID].latestMsgIDs[users[userID].msgCnt].timestamp = timestamp;
		users[userID].latestMsgIDs[users[userID].msgCnt].like = 0;
		++users[userID].msgCnt;
	}
}


void init(int N)
{
	MY_N = N;
	for (register int i = 0; i < MAX_USER; ++i) {
		users[i].followerCnt = 0;
		users[i].msgCnt = 0;
	}
	currentTime = 0;
	for (register int i = 0; i < MAX_POST; ++i) {
		posts[i].like = 0;
	}
}

void follow(int uID1, int uID2, int timestamp)
{
	users[uID1].follow(uID2, timestamp);
}

void makePost(int uID, int pID, int timestamp)
{
	post(uID, pID, timestamp);
}

void like(int pID, int timestamp)
{
	posts[pID].timestamp = timestamp;
}

void getFeed(int uID, int timestamp, int pIDList[])
{
	currentTime = timestamp;
	int totalMsgs[MAX_USER * MAX_LATEST];
	int cnt = 0;
	//get user's latest msgs
	for (register int i = 0; i < users[uID].msgCnt; ++i) {
		totalMsgs[cnt++] = users[uID].latestMsgIDs[i].id;
	}

	//get followings latest msgs
	for (register int i = 0; i < users[uID].followerCnt; ++i) {
		int followUserID = users[uID].following[i].id;
		for (register int j = 0; j < users[followUserID].msgCnt; ++j) {
			totalMsgs[cnt++] = users[followUserID].latestMsgIDs[j].id;
		}
	}

	// sort all 
	insertionSort(totalMsgs, cnt);

	if (cnt > MAX_LATEST) {
		for (register int i = 0; i < MAX_LATEST; ++i) {
			pIDList[i] = totalMsgs[cnt - i - 1];
		}
	}
	else {
		for (register int i = 0; i < cnt; ++i) {
			pIDList[i] = totalMsgs[cnt - i - 1];
		}
	}
}
