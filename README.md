```
#define MAXLength (10000)
#define MAXServer (10)
#define MAX_USER 10000

typedef struct _user{
	int id;
	int axis;
	int mostFarServer;
	int cnt; 
} USER ;

typedef struct _server {
	int axis;
	int capa;

} SERVER;

USER users[MAX_USER];
SERVER servers[MAXServer];

int MY_N, MY_L;
int serverCnt, userCnt;

//heap


int compare(USER a, USER b){
	if (a.mostFarServer != b.mostFarServer) { return a.mostFarServer > b.mostFarServer; }
	return a.id < b.id;
}
int heapPush(USER value)
{
	if (userCnt + 1 > MAX_USER)
	{
		return 0;
	}

	users[userCnt] = value;

	int current = userCnt;
	while (current > 0 && compare(users[current], users[(current - 1) / 2]))
	{
		// users[current] < users[(current - 1) / 2]
		USER temp = users[(current - 1) / 2];
		users[(current - 1) / 2] = users[current];
		users[current] = temp;
		current = (current - 1) / 2;
	}

	userCnt = userCnt + 1;

	return 1;
}

int heapPop(USER *value)
{
	if (userCnt <= 0)
	{
		return -1;
	}

	*value = users[0];
	userCnt = userCnt - 1;

	users[0] = users[userCnt];

	int current = 0;
	while (current * 2 + 1 < userCnt)
	{
		int child;
		if (current * 2 + 2 == userCnt)
		{
			child = current * 2 + 1;
		}
		else
		{
			child = compare(users[current * 2 + 1] , users[current * 2 + 2]) ? current * 2 + 1 : current * 2 + 2;
		}

		if (compare(users[current] , users[child]))
		{
			break;
		}

		USER temp = users[current];
		users[current] = users[child];
		users[child] = temp;

		current = child;
	}
	return 1;
}


void init(int L, int N, int C, int axis[MAXServer])
{
	MY_L = L;
	MY_N = N;
	serverCnt = 0;
	userCnt = 0;

	// set server axis and capa
	for (register int i = 0; i < N; ++i){
		servers[serverCnt].axis = axis[i];
		servers[serverCnt].capa = C;
		++serverCnt;
	}
}

void insertionSort(void)
{
	int temp;
	int i;
	int j;

	for (i = 1; i < num; i++)
	{
		temp = input[i];
		j = i - 1;

		while ((temp < input[j]) && (j >= 0))
		{
			input[j + 1] = input[j];
			j = j - 1;
		}
		input[j + 1] = temp;
	}
}



int getMostFarServer(int axis){
	int tmp[MAXServer];

	for (register int i = 0; i < MY_N; ++i){
		
	}
}

int add_user(int uid, int axis)
{
	USER newUser;
	newUser.id = uid;
	newUser.axis = axis;
	newUser.mostFarServer = getMostFarServer();

	// update user priority
	heapPush(newUser);


	return 0;
}

int remove_user(int uid)
{
	USER popUser;
	heapPop(&popUser);
	return 0;
}

int get_users(int sid)
{
	return 0;
}

```
