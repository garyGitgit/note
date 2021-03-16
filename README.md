```
1) hash -> 도서 이름 (MAX unsigned long long), 도서 타입 (MAX 5, unsigned long long)
2) section (MAX 100)
3) 


unsigned long long s2i (char bookName[]){
    unsigned long long ret = 0;
    for (; *bookName ; bookName++){
        ret = (ret << 6 ) + ret - 'a + 1;
    }
    return ret;
}



typedef struct _type{
    BOOK * types [500+1];
    void add (int typeId, BOOK *book){
        BOOK *head = types [typeId]->next;
        head->prev->next = book;
        book->prev = head-<prev;
        book->next = head;
        head->prev = book;
    }
} TYPE;

TYPE type [500+1];

typedef struct _book{
    unsigned long long bookId;
    int section;
    int mTypes[5];
    int mTypesCnt;
    struct _book *prev, *next;
} BOOK;

BOOK *bookDict [100000];

void addHash(BOOK *newBook){
    unsigned long long hId = s2i(name);
    int hId = hId % 100000;

    while (bookDict[hId] != 0 ){}
    bookDict[hId] = newBook;
}

BOOK *findHash(){
}
typedef struct _bookPool{
    int cnt;
    BOOK bookPool[100000 + 500 + 100];
    BOOK *alloc (){
        bookPool[cnt].section = 0;
        bookPool[cnt].bookId = 0;
        bookPool[cnt].mTypesCnt = 0;
        bookPool[cnt].prev = bookPool[cnt].next = 0;
        return &bookPool[cnt++];
    }
}BPOOL;
BPOOL bookPool;



typedef struct _section{
    BOOK *sections[100 + 1];

    void append(int sId, BOOK *ptr){
        BOOK *head = *sections[sId]->next;
        if (!head){
            head->prev->next = ptr; //dummy->new
            ptr->prev = head->prev; //new->dummy
            ptr->next = head; // new->head
            head->prev = ptr; // head->new      
        }
        else{
            ptr->prev = *sections[sId];
            *sections[sId]->next = ptr;
        }
    }

    void create (int sId, char bookName[], int mTypes[], int mTypesCnt) {
        unsigned long long bookId = s2i (bookName);
        BOOK * newBook = bookPool.alloc();
        newBook->bookId = bookId;
        newBook->section = sId;
        for (RINT i = 0 ; i < mTypesCnt; ++i){
            newBook->mTypes[i] = mTypes[i];
            type.types[mTypes[i]].add(newBook);
        }
        newBook->mTypesCnt = mTypesCnt;

        BOOK *head = *sections[sId]->next;
        if (!head){
            head->prev->next = newBook; //dummy->new
            newBook->prev = head->prev; //new->dummy
            newBook->next = head; // new->head
            head->prev = newBook; // head->new      
        }
        else{
            newBook->prev = *sections[sId];
            *sections[sId]->next = newBook;
        }
    }

    void remove (int sId, BOOK *book){
    }
} SECTION;

SECTION section;


init

bookPook.cnt = 0;
for (RINT i = 1 ; i <= M; ++i){ 
    sections[i] = bookPool.alloc(); //head 
}

for (RINT i = 0; i <= 500; ++i){
    type.types[i] = bookPool.alloc(); // head
}
add: section 에 book 추가 (book 은 mTypes 를 타입으로 갖고 있음) 

int _mTypes [5];
for (RINT i = 0 ; i < mTypeNum ; ++ i){
    _mTypes[i] = s2i (mTypes[i]);
}
section.create(sectionId, char bookName, _mTypes, mTypeNum);

moveType: mType 을 가진 모든 책을 mfrom (section) 에서 mto (section) 로 이동

int typeId = s2i(mType);
BOOK *ptr = type.types[typeId];
while(ptr){
    sections.remove(sId, ptr);
    sections.append(sId, ptr);
    ptr = ptr->next;
}


moveName: mName 을 가진 책을 mSection 으로 이동 
deleteName mName 을 가진 책을 제거
countBook: mSection 에서 mType 을 하나라도 갖고 있으면 그 책들을 counting 해서 반환

```
