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
