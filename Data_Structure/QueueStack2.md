## Stack On Heap

기존 Stack은 배열의 크기가 고정된다는 단점이 있다. 이전의 Linked List를 이용하면 Heap에 Stack을 구성할 수 있다.

기존 가장 최신에 들어온 데이터의 인덱스를 지칭하던 Top 변수를 DATA의 pointer로 선언한 뒤, 가장 마지막에 들어온 노드를 지칭하게 한다.

처음 데이터 노드의 next는 Null을 가리키게 하고 새로들어온 노드의 next는 이전 노드의 주소를 가리키도록 구현한다. 또한 pop될 시 Top를 Top의 next에 위치시키고, push시 새로운 데이터의 next가 Top를 가리키게 하고, Top를 새로 들어온 노드로 하여  Stack을 구현한다.

### 선언
```
typedef struct _stk
{
	int num;
	struct _stk * next; 
}STACK;

STACK * Top = (STACK *)0;

int Push_Stack(STACK * data);
int Pop_Stack(STACK * p);
```

### Push
```
int Push_Stack(STACK *data)
{
	STACK* p = calloc(1,sizeof(STACK));
	if(!p) return -1;
	*p = *data;
	if(!Top){
		Top = p;
		p->next = (STACK*) 0x0;
	}
	else{
		p->next = Top;
		Top = p;
	}
	return 1;
}
```

### Pop
```
int Pop_Stack(STACK *p)
{	
	STACK* temp;
	if(!Top)
		return -1;
	*p = *Top;
	temp = Top;
	Top = Top->next;
	free(temp);			//동적할당으로 받은 데이터이기 때문에 free 필요!
	return 1;

}
```

### Queue On Heap

Queue 또한 Linked List를 통해 구현할 수 있다. 배열로 Queue를 구현할 경우와 달리 구조체 크기에 제한이 없기 때문에 데이터를 옮겨야 하는 리스크가 사라진다.

Queue도 Stack과 마찬가지로 처음과 끝을 가리키는 Front, Rear가 구조체를 가리키는 포인터가 된다.
초기에 데이터가 있지 않을 때에는 Front와 Rear둘다 NULL값을 가지며 1번 데이터가 들어올 때 1번 데이터의 next는 null, Front와 Rear는 1번 데이터를 지칭하게 된다. 이후 데이터가 추가될 때 마다 Front가 지칭하는 데이터의 next를 새로운 데이터로 하고 Front를 새 데이터로 옮긴다.

데이터가 나갈 때에는 Rear를 반환하고 Rear의 next가 지칭하는 노드로 Rear를 이동한다. Rear가 null이 될 때가 데이터가 모두 사라진 때이다.

자세한 구현은 아래와 같다.

### 선언
```
typedef struct _que
{
	int num;
	struct _que * next; 
}QUEUE;

QUEUE * Front = (QUEUE *)0;;
QUEUE * Rear = (QUEUE *)0;;

int InQueue(QUEUE * data);
int OutQueue(QUEUE * p);
```

### InQueue
```
int InQueue(QUEUE * data)
{
	QUEUE* p = calloc(1,sizeof(QUEUE));
	if(!p)
		return -1;
	*p = *data;
	p->next = (QUEUE*)0x0;
	if(!Rear)					//Rear와 Front 둘다 NULL이지만 REAR만 확인해도 데이터가 비었는지 확인 가능
	{
		Front=p;
		Rear=p;
	}
	else
	{
		Front->next = p;
		Front = p;
	}
	return 1;


}
```

### OutQueue
```
int Out_Queue(QUEUE * p)
{920
	QUEUE* temp;
	if(!Rear)
		return -1;
	temp = Rear;
	(*p) = (*temp);
	Rear = Rear->next;
	free(temp);
	return 1;
}
```

---------------------
Queue든 Stack이든 Heap에 쌓이는 데이터는 데이터의 크기가 일정하지 않고 임의의 영역에 데이터가 할당되기 때문에 Real Time System 등 시스템의 동작이 완벽히 파악되어야 하는 분야에는 적합하지 않다. 또한 Link를 통해 데이터를 접근하기 때문에 빠른 속도가 중요한 시스템에서 또한 적합하지 않다. Heap영역은 Runtime에서 용량이 결정되기 때문에 임베디드에서도 잘 안쓰인다고 합니다.



