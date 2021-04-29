```cpp
// real time update


struct BookPQ {
	int hn;
	int heap[BLM + 3]; // book id만 기록
	int pos[BLM + 3];  // id 범위만큼 필요

	void swapAll(int x, int y) {
		swap(heap[x], heap[y]);
		swap(pos[heap[x]], pos[heap[y]]);
	}
	void up(int c) {
		for (; c > 1; c /= 2) {
			if (compSeller(heap[c], heap[c / 2])) swapAll(c, c / 2);
			else break;
		}
	}
	void down(int p) {
		for (int c = p * 2; c <= hn; c *= 2) {
			if (c < hn && compSeller(heap[c + 1], heap[c])) c++;
			if (compSeller(heap[c], heap[c / 2])) swapAll(c, c / 2);
			else break;
		}
	}
	void push(int id) {
		heap[++hn] = id;
		pos[id] = hn;
		up(hn);
	}
	int pop() {
		int top = heap[1];
		swapAll(1, hn--);
		down(1);
		return top;
	}
	void update(int id) {
		up(pos[id]), down(pos[id]);
	}
	void erase(int id) {
		int hidx = pos[id];
		swapAll(hidx, hn--);
		if(hidx<=hn) up(hidx), down(hidx);
	}

}bestSeller[23]; // 0:total, 1~20:장르별



```

```cpp
lazy update	

/*
update : lazy propagation, duplicate push
erase : lazy propagation
*/

const int LM = 10000;
const int LN = 100000;
int authorProfit[LM + 3], bestAuthor[4];

template<typename T>
void swap(T&x, T&y) { T z = x; x = y; y = z; }

bool compAuthor(int x, int y) {
	return authorProfit[x] == authorProfit[y] ? x<y : authorProfit[x]>authorProfit[y];
}
struct BookInfo {
	int id, genreID, price, remain, sellCnt; // price 저장시 author가 받는 price를 저장했음 (input price / author num)
	int authorIDs[6];

	// 책이 판매될때마다 author의 profit을 업데이트 해주고 bestAuhor를 최신화한다.
	void updateAuthorProfit(int cnt) {
		for (int i = 0; i < 5 && authorIDs[i]; i++) { // authorIDs[i]==0 이면 끝
			authorProfit[authorIDs[i]] += price * cnt;
			bestAuthor[3] = authorIDs[i];
			int j = 2;
			// 업데이트된 저자가 이미 bestSeller에 있다면 그 위치를 찾는다. j는 찾은 위치 이전으로 이동
			for (int k = 0; k < 2; k++) if (bestAuthor[3] == bestAuthor[k]) j = k - 1;
			// j는 항상 새로 업데이트 된 author의 이전 위치를 가리킨다.
			for (; j >= 0; j--) {
				if (compAuthor(bestAuthor[j + 1], bestAuthor[j])) {
					swap(bestAuthor[j + 1], bestAuthor[j]);
				}
			}
		}
	}
}book[LN + 3];

bool compSeller(BookInfo x, BookInfo y) {
	return x.sellCnt == y.sellCnt ? x.id > y.id : x.sellCnt > y.sellCnt;
}
struct Data { int id, int sellCnt; }
struct BookPQ {
	int hn;
	/*
	 lazy update의 경우에는 그냥 id값으로만 heap을 기록하면 중복된 id들에 대해서 처리가 불가능하다.
	 반드시 id와 update 식별가능한 정보가 같이 기록되어야 한다.
	 1. 해당 정보의 객체를 그대로 사용하는 방법 ex) 현재 코드의 구성
	 2. 새로운 클래스를 생성해서 필요한 정보만 사용하는 방법
	     ex) struct Data { int id, int sellCnt; }
	         Data heap[LN+3];
	 1의 경우 편한데 메모리 overhead가 심하고 힙을 구성해야하는 클래스가 여러개이면 골치아프다 ex)나튜브2
	 2의 경우 여러개의 객체에 대해서 통일시켜서 관리할 수 있으므로 더 범용적이다. ex) 나튜브2
	*/
	BookInfo heap[LN + 3];

	void push(BookInfo book) {
		heap[++hn] = book;
		for (int c = hn; c > 1; c /= 2) {
			if (compSeller(heap[c], heap[c / 2])) swap(heap[c], heap[c / 2]);
			else break;
		}
	}
	BookInfo pop() {
		BookInfo top = heap[1];
		heap[1] = heap[hn--];
		for (int c = 2; c <= hn; c *= 2) {
			if (c < hn && compSeller(heap[c + 1], heap[c])) c++;
			if (compSeller(heap[c], heap[c / 2])) swap(heap[c], heap[c / 2]);
			else break;
		}
		return top;
	}
	BookInfo top() {
		while (hn && book[heap[1].id].sellCnt != heap[1].sellCnt) pop();
		return heap[1];
	}
}bestSeller[23]; // 0:total, 1~20:장르별

void init(int authorNumber, int genreNumber) {
	bestAuthor[0] = 1, bestAuthor[1] = 2, bestAuthor[2] = 3;
	for (int i = 1; i <= authorNumber; i++) authorProfit[i] = 0;
	for (int i = 0; i <= genreNumber; i++) bestSeller[i].hn = 0;
}

void registerBookInfo(int bookID, int genreID, int price, int number, int authorIDs[]) {
	// book 정보등록, price는 작가에게 지급되는 금액으로 등록한다. (price/number) 
	book[bookID] = { bookID, genreID, price / number, 0, 0, 1 };
	for (int i = 0; i < number; i++) book[bookID].authorIDs[i] = authorIDs[i];
	book[bookID].authorIDs[number] = 0; //  author의 마지막을 0으로 표시해서 끝을 알린다.

	bestSeller[0].push(book[bookID]);
	bestSeller[genreID].push(book[bookID]);
}

void inputBook(int bookID, int cnt) {
	book[bookID].remain += cnt;
}

int sellBooks(int bookID, int cnt) {
	if (book[bookID].remain >= cnt) {
		book[bookID].remain -= cnt, book[bookID].sellCnt += cnt;
		book[bookID].updateAuthorProfit(cnt);
		// 항상 0, genreID 를 같이 업데이트 한다.
		// 중복되어도 최신 정보 push
		bestSeller[0].push(book[bookID]);
		bestSeller[book[bookID].genreID].push(book[bookID]);
		return 1;
	}
	return 0;
}

int pickBestSeller(int genreID, int bookIDs[]) {
	int c = 0;
	while (c < 3) {
		BookInfo top = bestSeller[genreID].top();
		if (!bestSeller[genreID].hn) break;
		bookIDs[c++] = top;
	}
	// 유효한 값들만 다시 heap에 넣어준다.
	for (int i = 0; i < c; i++) bestSeller[genreID].push(book[bookIDs[i]]);
	return c;
}

void pickBestAuthors(int authorIDs[]) {
	for (int i = 0; i < 3; i++) authorIDs[i] = bestAuthor[i];
}

void outOfPrint(int bookID) {
	// 절판 표시
	book[bookID].sellCnt = -1e10;
}
```
