## 4. Linked List

Queue와 Stack 등 배열을 기반으로한 자료구조는 데이터의 크기를 확장시키지 못하는 단점이 존재한다. List의 경우에는 데이터를 정렬상태로 유지하기 위해서는 데이터를 추가하고 삭제할 때 마다 뒤의 데이터를 이동시켜야 하는 단점이 있다.

Linked List는 이런 단점을 보완하기 위해 고안된 자료구조이다. 데이터마다 다음 나올 데이터의 주소를 저장하는 포인터 변수 next가 존재하는 구조체 형태로 존재한다. 

Linked List는 자료를 탐색하는 Search함수, 자료를 추가하는 Insert함수, 자료를 삭제하는 Delete 함수로 구현된다.

Search함수는 첫번째 자료부터 자료값을 비교하고 next 포인터를 통해 다음자료와 값을 비교하는 형태로 동작한다.

Insert함수는 삽입할 위치 앞의 데이터의 next로 삽입할 데이터의 next로 바꾸고 삽입할 데이터의 앞 데이터의 next를 삽입할 데이터로 지칭하여 구현한다.

Delete함수는 제거할 데이터 앞 데이터의 next를 제거할 데이터의 next로 지칭하여 구현한다.

Linked List는 함수의 동작을 단순하하기 위해 아무 값도 들어있지 않고 다음 포인터만을 지정하는 Head라는 더미데이터를 두기도 한다.

아래는 더미데이터 Head를 추가하여 구현한 Linked List 코드이다.

### 선언
```
typedef struct _link 
{
	int item;
	struct _link  * next; 
}DATA;

DATA* Head;
int Insert(DATA * head, DATA* d);
DATA * Search(DATA * head, int item);
int Delete(DATA * head, int item)
```



### Search
```
DATA * Search_Id_Node(SCORE * head, int item)
{
	while(head=head->next){				//데이터 값이 할당되지 않았을 경우 루프 탈출
		if(head->item == item) 	return head;	//원하는 값을 찾았을 경우 해당 노드의 포인터 반환	
		if(head->item>item)	break;		//원하는 값보다 큰 값이 발견될 경우 루프 중단 (오름차순의 경우)
	}
	return (DATA*)0;				//루프내에서 원하는 데이터를 찾지 못하였으므로 NULL 반환
}

```

### Insert
```
int Insert(DATA * head, DATA * d)
{
	DATA* p=calloc(1,sizeof(DATA));			//linked list는 heap기반이 일반적이므로 heap에 데이터 할당
	if(!p) return -1;				//heap에 데이터를 할당할 공간이 없을 경우 -1 반환

	(*p)=(*d);					//구조체는 대입을 통해 깊은 복사가 가능!
	while(head->next)				//d가 들어갈 위치 탐색. 원하는건 head가 아닌 head의 앞데이터!!
	{
		if(head->next->item == d->item)
		{
			free(p);
			return -2;
		}					//중복된 item값 존재할 경우 삭제(heap에 할당한 데이터 초기화)
		if(head->next->item > d->item)
			break;				//추가할 위치를 찾았을 때 루프 탈출
		head=head->next;			// 이부분을 while condition에 안넣은 이유는 insert에서는 삽입할 데이터의 앞데이터가 필요하기 때문!
	}
	p->next = head->next;
	head->next = p;					//링크 연결해주면 끝!
	return 1;

}
```

### Delete
```
int Delete(DATA * head, int item)			//삭제할 데이터의 포인터가 아닌 그 앞데이터의 포인터가 필요해서 search함수를 쓸수 없다!
{
	while(head->next){				//루프 탐색
		if(head->next->item ==id)
		{
			SCORE * p = head->next;		//heap에 있는 데이터이므로 다른 포인터를 만들어 heap데이터 삭제해 주지 않으면 garbage가 쌓이게 된다!
			head->next = head->next->next;
			free(p);
			return 1;
		}
		head = head->next;
	}
	return -1;
}
```

----------------

## Double Linked List

Data에 link가 하나만 있는 single linked list의 경우 데이터를 역방향으로 출력할 때에는 데이터를 순방향으로 탐색하면서 데이터를 Stack에 넣어준 뒤 다시 출력해야 하는 번거로움이 있다.

또한 앞서 Delete 코드에서 알 수 있듯이 삭제하고픈 노드의 주소를 알아도 삭제할 노드의 앞 데이터가 필요하기 때문에 Delete에서 Search를 다시 구현해야하는 번거로움이 있다.

Double Linked List는 next외에 prev라는 포인터를 구조체 멤버로 가져 앞 데이터에 대한 링크 또한 갖는 자료구조이다.

Double Linked List는 head외에 Tail을 추가변수로 갖고 역방향 데이터 접근을 용이하게 하기도 한다.

아래는 
