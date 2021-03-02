```

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
