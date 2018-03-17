## 3. Queue

Queue는 First In First Out의 특징을 가진 배열형식의 구조체이다. 

Queue는 데이터가 쓰는 곳을 가리키는 포인터 Front와 데이터가 읽을 곳을 가리키는 포인터 Rear가 필요하다. 

Queue는 데이터를 넣는 InQueue(혹은 Put)함수와 데이터를 읽고 삭제하는 OutQueue(혹은 Get) 함수로 작동한다. InQueue에는 Front의 위치에 데이터를 넣고 Front를 1 증가시키고 OutQueue에는Rear 위치의 데이터를 반환하고 Rear를 증가시킨다. Front와 Rear가 같은 값일 때 Queue가 비워져 있으므로 OutQueue에서는 NULL을 반환한다.

Queue는 대표적으로 운영체제가 프로세스가 요청한 리소스를 할당할 때 사용된다.

Linear Queue는 빈 공간을 사용하려면 모든 자료를 꺼내 자료를 이동시켜야 하는 단점이 존재한다. 

따라서 쓴 공간을 효율적으로 접근하기 위해 Queue의 처음과 끝부분을 연결한 Circlar Queue가 등장하게 된다.
(Moduler 연산을 통해 쉽게 구현)

Circular Queue의 경우 데이터가 모두 찼을 때와 모두 비어이있을 때 둘다 Front와 Rear가 같은 값을 가지게 되므로 별도의 Flag를 두거나 마지막 칸을 채우지 않느 ㄴ방식으로 구현한다.

## Linear Queue

---------------------
### 선언

```
#define MAX_Q			20
#define Q_EMPTY			0
#define Q_FULL			MAX_Q

int Queue[MAX_Q];
int Front = Q_EMPTY;
int Rear = Q_EMPTY;

int InQueue(int data);
int OutQueue(int *p);
```

### InQueue
```
int InQueue(int data)
{
	if(Front==MAX_Q)			//Front가 꽉 찼을 때 Rear가 0이 아니면 데이터를 앞으로 옮겨줌
	{
		int num,i,gap;
		if(!(num = Rear-Q_EMPTY))	return -1;
		gap = Front - Rear;
		for(i=0;i<gap;i++)
			Queue[Rear+i-num] = Queue[Rear+i];
		Front -=num;
		Rear -=num;
	}
	Queue[Front++]=data;			//Queue의 기본 동작
	return 1;
}
```

### OutQueue
```
int OutQueue(int *p)
{
	if(Rear==Front)
		return -1;
	(*p)=Queue[Rear++];
	return 1;
}
```
--------------------------------------------


## Circular Queue
---------------------
### 선언

```
#define MAX_Q		8
#define Q_MIN		0
#define Q_MAX		MAX_Q

int Queue[MAX_Q];
int Front = Q_MIN;
int Rear = Q_MIN;

int InQueue(int data);
int OutQueue(int *p);
```

### InQueue
```
int InQueue(int data)
{
	if((Front+1)%MAX_Q==Rear)
		return -1;
	
	Queue[Front] = data;
	Front = (Front+1)%MAX_Q;
	return 1;

}
```

### OutQueue
```
int OutQueue(int *p)
{
	if(Rear==Front)
		return -1;
	(*p)=Queue[Rear];
	Rear = (Rear+1)%MAX_Q;
	return 1;
}
```
---------------------
