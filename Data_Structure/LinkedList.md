---------------------------
## Linked List On Static Region

next 포인터를 포함한 구조체의 배열을 global symbol로 지정하여 static 영역으로 linked list를 선언할 수도 있다. 이 경우 데이터의 양이 프로그램의 메모리에 나타나기 때문에 좀더 안정적인 시스템 구현에 사용된다. 일부 File System 또한 이와 같은 방법으로 구현된다.

### 선언
```
typedef struct _link
{
	int item;
	DATA* next;
}DATA;

define MAX_LIST = 20;

DATA* Head;
DATA example[MAX_LIST];

int Insert(DATA* d);			//heap에서와 달리 배열이 global이기 때문에 어디서든 접근이 가능하여 따로 parameter로 두지 않는다.
DATA * Search(int item);
int Delete(int item);
```

## insert
```
int Insert(DATA * p)
{
	int i;

	for(i=0; i<MAX_LIST; i++)				//데이터의 총량이 한정되어있어 while문보다 for문을 통해 더 쉽게 구현 가능하다.
	{
		if(exam[i].item == 0) 
		{
			exam[i] = (*p);
			return i;
		}

		if(exam[i].item == p->item)		//같은 데이터가 있을 경우 -2반환
		{
			return -2;
		}
	}

	return -1;
}

```

### Search
```
DATA * Search(int item)
{	DATA* temp = head;			//head는 global 변수라 그냥 쓰면 손상되므로 지역변수를 만들어서 사용
	int i;		
	for(i=0;i<MAX_LIST;i++){
		if(temp->next == (DATA*)0)
			break;
		temp=temp->next;		//보통 search와 같이 하지만 for문을 통해 탐색영역 한정.
		if(temp->item == item)
			return temp;
	}
	return (DATA*)0x0;
}
```

### Delete
```
int Delete(int item)
{	
	DATA* temp = head;
	int i;
	for(i=0;i<MAX_LIST;i++){
		if(temp->next==(DATA*)0x0)
			return -1;
		else if (temp->next->item == item){
			temp->next->item = 0;			//free를 해주는 대신 item을 0으로 바꾸는 것으로 데이터를 지운 것으로 한다.
			temp->next = temp->next->next;	
			return 1;
		}
		else if(temp->next->item > item) return -1;
		temp=temp->next;

	}
	return -1;
}
```
