```

// User defined 
#define MAX_STR 6 + 1
#define MAX_CHILDREN 30
#define MAX_MEMORY 50010

typedef struct _tree{
	int s2i;
    int childCnt;
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
    newNode->childCnt = 0;
	initChildren(newNode->children);
	return newNode;
}

TREE *createNodeInt(int s2i){
	TREE *newNode = _malloc();
	newNode->s2i = s2i;
	newNode->parent = 0;
	initChildren(newNode->children);
	return newNode;
}

void updateChildCnt(TREE *node, int delta){
    while (node){
        node->childCnt += delta;
        node = node->parent;
    }
}

void insertNode(TREE *parent, TREE *newNode){
	TREE **now = &(parent->children[0]);
	// find children pos
	int cursor = 0;
	while (*now){
		++cursor;
		++now;
	}
	newNode->parent = parent;
	parent->children[cursor] = newNode;
    ++parent->childCnt;
    updateChildCnt(parent, 1);
}

TREE *getNearestNodeByPath(TREE *parent, char path [], int *offset){
	char name[MAX_STR];
	//start from the next to first slash
    if (offset){
        path = path + *offset + 1;
    }
	register int i = 0;

	//get name
	while (*path != '/' && *path != 0){
		name[i] = *path;
		++i;
		++path;
	}
	name[i] = 0;
    if (offset){
        *offset = *offset + i + 1;
    }

	int pathInt = strToInt(name);

	for (i = 0; i < MAX_CHILDREN ; ++i){
        if (parent->children[i] != 0){
		    if (parent->children[i]->s2i == pathInt){
			    return parent->children[i];
		    }
        }
	}
	return 0;
}

TREE *getNodeByPath (char path[]){
    TREE *node = root;
    TREE *prev = 0;
    int offset = 0;

	while (node){
		prev = node;
		node = getNearestNodeByPath(node, path, &offset);
	}
}

TREE *root;
int N;
//To implement
void init(int n) {
	N = n;
	mPool.cursor = 0;
	root = createNode("");
}

void cmd_mkdir(char path[PATH_MAXLEN + 1], char name[NAME_MAXLEN + 1]) {
    TREE *newNode = createNode(name);
    TREE *parent = getNearestNodeByPath(root, path, 0);
    insertNode(parent, newNode);
}

void cmd_rm(char path[PATH_MAXLEN + 1]) {
    TREE *node = getNearestNodeByPath(root, path, 0);
    TREE *parent = node->parent;
    for (register int i  = 0; i < MAX_CHILDREN ; ++i){
        if (parent->children[i] != 0){
            if (node->s2i == parent->children[i]->s2i){
                parent->children[i] = 0;
                int delChildCnt = parent->children[i]->childCnt + 1;
                updateChildCnt (parent, (-1)*delChildCnt);
                break;
            }
        }
    }
}

void copy(TREE *srcNode, TREE *destNode){
	if (!srcNode) { return ; }

	for (int i = 0; srcNode->children[i] != 0; ++i){
        destNode = createNodeInt(srcNode->s2i);
		copy(srcNode->children[i], destNode->children[i]);
	}
}
void cmd_cp(char srcPath[PATH_MAXLEN + 1], char dstPath[PATH_MAXLEN + 1]) {
    TREE *srcNode = getNearestNodeByPath(root, srcPath, 0);
    TREE *destNode = getNearestNodeByPath(root, dstPath, 0);

    destNode->childCnt += srcNode->childCnt;
    copy(srcNode, destNode);
}

void cmd_mv(char srcPath[PATH_MAXLEN + 1], char dstPath[PATH_MAXLEN + 1]) {
    //cmd_cp(srcPath, dstPath);
    TREE *srcNode = getNearestNodeByPath(root, srcPath, 0);
    TREE *destNode = getNearestNodeByPath(root, dstPath, 0);

    insertNode(destNode, srcNode);
    cmd_rm(srcPath);
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
    node = getNodeByPath(path);

	return node->cnt;
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
