```


# define MAX_USER 1000
# define MAX_GROUP 100
# define ALPHABET 26
# define MAX_EVENT 5
# define MAX_HASH 40009
# define RINT register int
# define ULL unsigned long long

typedef struct _hash {
	int header;
	ULL name;
	int uIDList[MAX_GROUP][MAX_EVENT];
	int uIDCnt [MAX_GROUP];
} HASH;

HASH hash[MAX_HASH], ini;
int userCnt [MAX_USER];
int addHash (char *str){
	ULL name = 0;
	str = str + 2; // ignore first 2 char for header

	for (; *str; ++str){
		name = (name << 5) + *str - 'a' + 1;
	}
	int idx = name % MAX_HASH;

	while (hash[idx].header != 0){
		idx = (idx + 1) % MAX_HASH;
	}
	
	int header = ((str[0] - 'a' + 1)  << 5) + str[1] - 'a' + 1; // str always longer than 2
	hash[idx].header = header;
	hash[idx].name = name;
	return idx;
}

int findHash (char *str){
	int header = ((str[0] - 'a' + 1)  << 5) + str[1] - 'a' + 1; // str always longer than 2
	ULL name = 0;
	str = str + 2; // ignore first 2 char for header

	for (; *str; ++str){
		name = (name << 5) + *str - 'a' + 1;
	}
	int idx = name % MAX_HASH;
	
	while (hash[idx].header != 0){
		if (hash[idx].header == header && hash[idx].name == name){
			return idx;
		}
		idx = (idx + 1) % MAX_HASH;
	}
	return -1;
}

void init()
{
	for (RINT i = 0 ; i < MAX_HASH ; ++i){
		hash[i] = ini;
	}
	for (RINT i = 0 ; i < MAX_USER; ++i){
		userCnt[i] = 0;
	}
}

void addEvent(int uid, char ename[], int groupid)
{
	int k = addHash(ename);
	hash[k].uIDList[groupid][hash[k].uIDCnt[groupid]++] = uid;
	userCnt[uid]++;
}

int deleteEvent(int uid, char ename[])
{
	int k = findHash(ename);
	int delCnt;
	for (RINT i = 0 ; i < MAX_GROUP ; ++i){
		if (hash[k].uIDCnt[i] > 0){
			for (RINT j = 0 ; j < hash[k].uIDCnt[i]; ++j){
				if (uid == hash[k].uIDList[j]){
					if (j == 0){
						delCnt += hash[k].uIDCnt[i];
						hash[k].uIDCnt = 0;
					}
					else{
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N, int M);
extern void befriend(int uid1, int uid2);
extern void add(int pid, int area, int price);
extern void reserve(int uid, int pid);
extern int  recommend(int uid);

#define INIT			100
#define BEFRIEND		200
#define ADD				300
#define RESERVE			400
#define RECOMMEND		500

static bool run()
{
	int L, N, M;
	bool okay = false;

	int pid, uid;
	int uid1, uid2;
	int area, price;

	int ans;
	int cmd, ret;

	scanf("%d", &L);

	for (int l = 0; l < L; ++l)
	{
		scanf("%d", &cmd);
		switch (cmd)
		{
		case INIT:
			scanf("%d %d", &N, &M);
			init(N, M);
			okay = true;
			break;
		case BEFRIEND:
			scanf("%d %d", &uid1, &uid2);
			befriend(uid1, uid2);
			break;
		case ADD:
			scanf("%d %d %d", &pid, &area, &price);
			add(pid, area, price);
			break;
		case RESERVE:
			scanf("%d %d", &uid, &pid);
			reserve(uid, pid);
			break;
		case RECOMMEND:
			scanf("%d %d", &uid, &ans);
			ret = recommend(uid);
			if (ret != ans)
				okay = false;
			break;
		}
	}

	return okay;
}

int main() {
	int TC, MARK;

	freopen("sample_input.txt", "r", stdin);

	setbuf(stdout, NULL);
	scanf("%d %d", &TC, &MARK);

	for (int testcase = 1; testcase <= TC; ++testcase) {
		int score = run() ? MARK : 0;
		printf("#%d %d\n", testcase, score);
	}

	return 0;
}

```
