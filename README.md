```
#define MAX_N    100
#define MAX_TOOL 50
#define MAX_STEP 10
#define MAX_MEMORY 100000
#define MAX_Q 100000
int MY_N;

typedef struct _heap {
	int heapSize = 0;
	int empties[MAX_TOOL + 1];
	int total = 0; // init once in setupTools

	int push(int value)
	{
		if (heapSize + 1 > MAX_TOOL + 1)
		{
			return 0;
		}

		empties[heapSize] = value;

		int current = heapSize;
		while (current > 0 && empties[current] < empties[(current - 1) / 2])
		{
			int temp = empties[(current - 1) / 2];
			empties[(current - 1) / 2] = empties[current];
			empties[current] = temp;
			current = (current - 1) / 2;
		}

		heapSize = heapSize + 1;

		return 1;
	}


	int getRunningCnt() {
		return total - heapSize;
	}

	
	int pop(int* value)
	{
		if (heapSize <= 0)
		{
			return -1;
		}

		*value = empties[0];
		heapSize = heapSize - 1;

		empties[0] = empties[heapSize];

		int current = 0;
		while (current * 2 + 1 < heapSize)
		{
			int child;
			if (current * 2 + 2 == heapSize)
			{
				child = current * 2 + 1;
			}
			else
			{
				child = empties[current * 2 + 1] < empties[current * 2 + 2] ? current * 2 + 1 : current * 2 + 2;
			}

			if (empties[current] < empties[child])
			{
				break;
			}

			int temp = empties[current];
			empties[current] = empties[child];
			empties[child] = temp;

			current = child;
		}
		return 1;
	}

} HEAP;

HEAP tools[MAX_STEP + 1]; // empty tools as a heap
int waitQ[MAX_STEP + 1]; // waiting count for each step

typedef struct _job {
	int stepId;
	int toolPt;
	int finshedTime;
} JOB;

typedef struct _memoryPool {
	JOB jobs[MAX_MEMORY];
	int cursor = 0;

	JOB* get(int _stepId, int _toolPt, int _finishedTime) {
		jobs[cursor].stepId = _stepId;
		jobs[cursor].toolPt = _toolPt;
		jobs[cursor].finshedTime = _finishedTime;
		return &jobs[cursor++];
	}
} MEMORY_POOL;

MEMORY_POOL mPool;

typedef struct _jobq {
	JOB *heap[MAX_Q];
	int heapSize;

	int compare(JOB *a, JOB *b) {
		if (a->finshedTime != b->finshedTime) return  a->finshedTime < b->finshedTime;
		if (a->stepId != b->stepId) return a->stepId > b->stepId;
		return a->toolPt < b->toolPt;
	}

	int push(JOB *value)
	{
		if (heapSize + 1 > MAX_Q)
		{
			return 0;
		}

		heap[heapSize] = value;

		int current = heapSize;
		while (current > 0 && compare(heap[current] , heap[(current - 1) / 2]))
		{
			JOB* temp = heap[(current - 1) / 2];
			heap[(current - 1) / 2] = heap[current];
			heap[current] = temp;
			current = (current - 1) / 2;
		}

		heapSize = heapSize + 1;

		return 1;
	}

	int pop(JOB** value)
	{
		if (heapSize <= 0)
		{
			return -1;
		}

		*value = heap[0];
		heapSize = heapSize - 1;

		heap[0] = heap[heapSize];

		int current = 0;
		while (current * 2 + 1 < heapSize)
		{
			int child;
			if (current * 2 + 2 == heapSize)
			{
				child = current * 2 + 1;
			}
			else
			{
				if (compare (heap[current * 2 + 1], heap[current * 2 + 2])) {
					child = current * 2 + 1;
				}
				else {
					child = current * 2 + 2;
				}
			}

			if (compare(heap[current] , heap[child]))
			{
				break;
			}

			JOB* temp = heap[current];
			heap[current] = heap[child];
			heap[child] = temp;

			current = child;
		}
		return 1;
	}
} JOBQ;

JOBQ jobQ; 



int getEmptyToolPt(int stepId) {
	int value;
	int res = tools[stepId].pop(&value); // return -1 when empty
	if (res < 0) { return -1; }
	else { return value; }
}

void run(int stepId, int availToolPt, int timestamp) {
	JOB *job = mPool.get(stepId, availToolPt, timestamp + availToolPt);
	jobQ.push(job);
}

// run if avail else wait
void process(int timestamp, int stepId) {
	// out of steps (출하창고)
	if (stepId == MY_N) {
		++waitQ[stepId];
		return;
	}
	int availToolPt = getEmptyToolPt(stepId);
	// run 
	if (availToolPt > 0) {
		run(stepId, availToolPt, timestamp);
	}
	//wait
	else {
		++waitQ[stepId];
	}
}

void clearWaiting(int stepId, int latestEmptyTime) {
	int availToolPt = getEmptyToolPt(stepId);
	run(stepId, availToolPt, latestEmptyTime);
	--waitQ[stepId];
}

void releaseTool(int stepId, int pt) {
	tools[stepId].push(pt); // release tool
}

void init(int N) {
	MY_N = N;
	// init heap
	for (register int i = 0; i <= MY_N; ++i) {
		tools[i].heapSize = tools[i].total = 0;
		waitQ[i] = 0;
	}
	// init job queue
	jobQ.heapSize = 0;
	mPool.cursor = 0;
}

void setupTool(int T, int stepNo[5000], int procTime[5000]) {
	//create heap
	for (register int i = 0; i < T; ++i) {
		tools[stepNo[i]].push(procTime[i]);
		++tools[stepNo[i]].total;
	}
}

void addLot(int time, int number) {
	while (jobQ.heapSize > 0 && jobQ.heap[0]->finshedTime <= time) {
		JOB* job;
		jobQ.pop(&job);
		releaseTool(job->stepId, job->toolPt);
		if (waitQ[job->stepId] > 0) {
			clearWaiting(job->stepId, job->finshedTime); // process to this step
		}
		process(job->finshedTime, job->stepId + 1); // process to next step
	}

	for (register int i = 0; i < number; ++i) {
		process(time, 0);
	}
}

int simulate(int time, int wip[MAX_N]) {
	//handle event (smaller than time)
	while (jobQ.heapSize > 0 && jobQ.heap[0]->finshedTime <= time) {
		JOB* job;
		jobQ.pop(&job);
		releaseTool(job->stepId, job->toolPt);
		if (waitQ[job->stepId] > 0) {
			clearWaiting(job->stepId, job->finshedTime); // process to this step
		}
		process(job->finshedTime, job->stepId + 1); // process to next step
	}

	for (register int stepId = 0; stepId < MY_N; ++stepId) {
		wip[stepId] = waitQ[stepId] + tools[stepId].getRunningCnt();
	}
	return waitQ[MY_N];
}



```
