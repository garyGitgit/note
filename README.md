```
#define NAME_MAXLEN 6
#define PATH_MAXLEN 1999

// The below commented functions are for your reference. If you want 
// to use it, uncomment these functions.
/*
int mstrcmp(const char *a, const char *b)
{
int i;
for (i = 0; a[i] != '\0'; i++)
{
if (a[i] != b[i])
return a[i] - b[i];
}
return a[i] - b[i];
}

int mstrncmp(const char *a, const char *b, int len)
{
for (int i = 0; i < len; i++)
{
if (a[i] != b[i])
return a[i] - b[i];
}
return 0;
}

int mstrlen(const char *a)
{
int len = 0;

while (a[len] != '\0')
len++;

return len;
}

void mstrcpy(char *dest, const char *src)
{
int i = 0;
while (src[i] != '\0')
{
dest[i] = src[i];
i++;
}
dest[i] = src[i];
}

void mstrncpy(char *dest, const char *src, int len)
{
for (int i = 0; i<len; i++)
{
dest[i] = src[i];
}
dest[len] = '\0';
}
*/

// User defined 
#define MAX_STR 6 + 1
#define MAX_CHILDREN 30
#define MAX_MEMORY 50010

typedef struct _tree{
	int s2i;
	struct _tree *parent;
	struct _tree *children[MAX_CHILDREN];
}TREE;

typedef struct _memoryPool{
	TREE pool[MAX_MEMORY];
	int cursor;
}  MEMORY_POOL;
MEMORY_POOL mPool;

TREE * _malloc(){
	return &mPool.pool[mPool.cursor++];
}

void _strcpy(char src[], char dest[]){
	while (*src != 0){
		*dest = *src;
		src++;
		dest++;
	}
}

int strToInt(char str[]){
	int s2i = 0;
	while (*str){
		s2i = (s2i << 5) + *str - 'a' + 1;
		str++;
	}
	return s2i;
}

void initChildren(TREE *children[]){
	for (register int i = 0; i < MAX_CHILDREN; ++i){
		children[i] = 0;
	}
}

TREE *createNode(char str[]){
	TREE *newNode = _malloc();
	newNode->s2i = strToInt(str);
	newNode->parent = 0;
	initChildren(newNode->children);
	return newNode;
}

void insertNode(TREE *parent, TREE *newNode){
	TREE **now = &parent->children[0];
	// find children pos
	int cursor = 0;
	while (*now){
		++cursor;
		++now;
	}
	newNode->parent = parent;
	parent->children[cursor] = newNode;
}

void init(){
	mPool.cursor = 0;
}


TREE *searchChildNodeByPath(TREE *parent, char path []){
	char name[MAX_STR];
	//start from the next to first slash
	++path;
	register int i = 0;

	//get name
	while (*path != '/' && *path != 0){
		name[i] = *path;
		++i;
		++path;
	}
	name[i] = 0;

	int pathInt = strToInt(name);

	for (i = 0; i < MAX_CHILDREN && parent->children[i] != 0; ++i){
		if (parent->children[i]->s2i == pathInt){
			return parent->children[i];
		}
	}
	return 0;
}


TREE *root;
int N;
//To implment

void init(int n) {
	N = n;
	mPool.cursor = 0;
	root = createNode("");
}

void cmd_mkdir(char path[PATH_MAXLEN + 1], char name[NAME_MAXLEN + 1]) {

}

void cmd_rm(char path[PATH_MAXLEN + 1]) {

}

void cmd_cp(char srcPath[PATH_MAXLEN + 1], char dstPath[PATH_MAXLEN + 1]) {

}

void cmd_mv(char srcPath[PATH_MAXLEN + 1], char dstPath[PATH_MAXLEN + 1]) {

}



int getSubdirCount(TREE *node){
	if (!node) { return 0; }

	int cnt = 0;

	for (int i = 0; node->children[i] != 0; ++i){
		cnt += getSubdirCount(node->children[i]);
	}
	return cnt;
}

int cmd_find(char path[PATH_MAXLEN + 1]) {
	TREE * prev = 0;
	TREE *node = root;
	// find node position
	while (node){
		prev = node;
		node = searchChildNodeByPath(node, path);
	}

	// found
	if (path == 0 && prev != 0){
		return getSubdirCount(prev);
	}
	return 0;
}

int main(){
	init(0);
	TREE *childNode = createNode("aaa");
	insertNode(root, childNode);

	TREE *childNode2 = createNode("bbb");
	insertNode(childNode, childNode2);

	childNode = createNode("bbb");
	insertNode(root, childNode);

	childNode = createNode("ccc");
	insertNode(root, childNode);

	int cnt = cmd_find ("/aaa");
	return 0;
}

```
