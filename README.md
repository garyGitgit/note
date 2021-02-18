```

#define PATH_MAXLEN 6 + 1
#define NAME_MAXLEN 30

// User defined 
#define MAX_STR 6 + 1
#define MAX_CHILDREN 30
#define MAX_MEMORY 50010

typedef struct _tree {
   int s2i;
   int childCnt;
   struct _tree *parent;
   struct _tree *children[MAX_CHILDREN];
}TREE;


TREE *root;
int N;

typedef struct _memoryPool {
   TREE pool[MAX_MEMORY];
   int cursor;
}  MEMORY_POOL;
MEMORY_POOL mPool;

TREE * _malloc() {
   return &mPool.pool[mPool.cursor++];
}

void _strcpy(char src[], char dest[]) {
   while (*src != 0) {
      *dest = *src;
      src++;
      dest++;
   }
}

int strToInt(char str[]) {
   int s2i = 0;
   while (*str) {
      s2i = (s2i << 5) + *str - 'a' + 1;
      str++;
   }
   return s2i;
}

void initChildren(TREE *children[]) {

   for (register int i = 0; i < MAX_CHILDREN; ++i) {
      children[i] = 0;
   }
}

TREE *createNode(char str[]) {
   TREE *newNode = _malloc();
   newNode->s2i = strToInt(str);
   newNode->parent = 0;
   newNode->childCnt = 0;
   initChildren(newNode->children);
   return newNode;
}

TREE *createNodeInt(int s2i) {
   TREE *newNode = _malloc();
   newNode->s2i = s2i;
   newNode->parent = 0;
   initChildren(newNode->children);
   return newNode;
}

void updateChildCnt(TREE *node, int delta) {
   while (node) {
      node->childCnt += delta;
      node = node->parent;
   }
}

void insertNode(TREE *parent, TREE *newNode) {
   TREE **now = &(parent->children[0]);
   // find children pos
   int cursor = 0;
   while (*now) {
      ++cursor;
      ++now;
   }
   newNode->parent = parent;
   parent->children[cursor] = newNode;
   
}

TREE *getNearestNodeByPath(TREE *parent, char path[], int *offset) {
   char name[MAX_STR];
   //start from the next to first slash
   path = path + *offset + 1;
   register int i = 0;

   //get name
   while (*path != '/' && *path != 0) {
      name[i] = *path;
      ++i;
      ++path;
   }
   name[i] = 0;
   *offset = *offset + i + 1;

   int pathInt = strToInt(name);

   for (i = 0; name[0] && i < MAX_CHILDREN; ++i) {
      if (parent->children[i] != 0) {
         if (parent->children[i]->s2i == pathInt) {
            return parent->children[i];
         }
      }
   }
   return 0;
}

TREE *getNodeByPath(char path[]) {
   TREE *node = root;
   TREE *prev = 0;
   int offset = 0;

   while (node) {
      prev = node;
      node = getNearestNodeByPath(node, path, &offset);
   }
   return prev;
}

//To implement
void init(int n) {
   char empty[2] = "";
   N = n;
   mPool.cursor = 0;
   root = createNode(empty);
}

void cmd_mkdir(char path[PATH_MAXLEN + 1], char name[NAME_MAXLEN + 1]) {
   TREE *newNode = createNode(name);
   TREE *parent = getNodeByPath(path);
   insertNode(parent, newNode);
   updateChildCnt(parent, 1);
}

void cmd_rm(char path[PATH_MAXLEN + 1]) {
   TREE *node = getNodeByPath(path);
   TREE *parent = node->parent;
   for (register int i = 0; i < MAX_CHILDREN; ++i) {
      if (parent->children[i] != 0) {
         if (node->s2i == parent->children[i]->s2i) {
            int delChildCnt = parent->children[i]->childCnt + 1;
            parent->children[i] = 0;
            updateChildCnt(parent, (-1)*delChildCnt);
            break;
         }
      }
   }
}

void copy(TREE *srcNode, TREE *destNode) {
   if (!srcNode) { return; }

   for (int i = 0; srcNode->children[i] != 0; ++i) {
      TREE *newNode = createNodeInt(srcNode->s2i);
      insertNode(destNode, newNode);
      copy(srcNode->children[i], destNode->children[i]);
   }
}
void cmd_cp(char srcPath[PATH_MAXLEN + 1], char dstPath[PATH_MAXLEN + 1]) {
   TREE *srcNode = getNodeByPath(srcPath);
   TREE *destNode = getNodeByPath(dstPath);

   copy(srcNode, destNode);
   updateChildCnt(destNode, srcNode->childCnt);
}

void cmd_mv(char srcPath[PATH_MAXLEN + 1], char dstPath[PATH_MAXLEN + 1]) {
   TREE *srcNode = getNodeByPath(srcPath);
   TREE *destNode = getNodeByPath(dstPath);

   cmd_rm(srcPath);
   insertNode(destNode, srcNode);
   updateChildCnt(destNode, srcNode->childCnt+1);
   
}

int getSubdirCount(TREE *node) {
   if (!node) { return 0; }

   int cnt = 0;

   for (int i = 0; node->children[i] != 0; ++i) {
      cnt += getSubdirCount(node->children[i]);
   }
   return cnt;
}

int cmd_find(char path[PATH_MAXLEN + 1]) {
   TREE *node = getNodeByPath(path);

   return node->childCnt;
}
```
