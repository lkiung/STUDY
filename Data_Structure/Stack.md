# 2. Stack

Stack은 Last In First Out 구조의 자료구조이다. 먼저 들어온 데이터는 다음 들어온 데이터가 나가야만 나갈 수 있는 구조로 하노이의 탑이 대표적인 Stack 구조의 문제이다.

Stack은 Stack의 맨 마자막으로 들어온 데이터의 위치를 가리키는 Top 변수를 필요로 한다. 

Stack은 크게 데이터를 Stack에 집어넣는 Push함수와 Stack에서 데이터를 꺼내는 Pop 함수로 구현된다.

Stack은 Top의 위치에 따라 Top이 마지막 데이터의 인덱스를 가리키는 Full Stack과 Top이 마지막 데이터의 인덱스 다음값을 가리키는 Empty Stack으로 구분할 수 있다.

또한 데이터가 인덱스가 제일 높은 값에서 시작하여 Push될 때 인덱스가 감소되는 Descending Stack과 인덱스가 0에서 시작하여 Push될 때 마다 인덱스가 증가하는 Ascending Stack으로 나뉜다.

프로그램에서 사용하는 Procedure Stack은 Full Descending Stack 구조를 가진다.

아래는 FD Stack의 구현이다.

### 선언

```

#define MAX_STACK		10
#define STACK_EMPTY	MAX_STACK
#define STACK_FULL		0

int Stack[MAX_STACK];
int Top = STACK_EMPTY;

int Push_Stack(int data);
int Pop_Stack(int *p);
```

### Push
```
int Push_Stack(int data)
{
	if(Top==STACK_FULL)
		return -1;
	else
		Stack[--Top] = data;
	return 1;
}
```

### Pop
```
int Pop_Stack(int *p)
{
	if(Top==STACK_EMPTY)
		return -1;
	else
		(*p)=Stack[Top++];
	return 1;
}
```

