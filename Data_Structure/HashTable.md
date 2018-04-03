## Hash Table

Hash table은 메모리의 용량을 포기하는 대신 탐색시간을 극소화한 자료구조이다. hash table은 hash function값을 인덱스로 하는 배열로 구성되어 탐색하는데에 별도의 시간이 소요되지 않는다.

hash function이란 임의의 길이의 데이터를 고정된 길이의 데이터로 매핑하는 함수이다. 예를 들어, 임의의 자연수 x에 대해 f(x) = x (mod 10) 이라 하면 임의의 자연수는 0~9까지의 정수로 맵핑된다. 

Hash function에 대한 결과값을 hash key, hash code 혹은 key라고 하며, 이 키값을 통해 데이터의 인덱스를 결정하는 방식을 해시 테이블이라 한다.

해시테이블의 가장 큰 이슈는 collision(충돌) 이다. 해시함수는 다 대 1 함수이기 때문에 비둘기집원리에 의해 항상 충돌이 존재할 수 밖에 없다. 

자료구조에서는 같은 키를 가지는 자료끼리 linked list를 구성하는 chainning 방식과 정해진 규칙에 따라 해시테이블 내에 비어있는 인덱스에 데이터를 삽입하는 open addressing 방식이 있다.

open addressing에서 충돌이 발생할 때에 새로운 인덱스를 부여하는 룰을 잘못정할 경우 중첩되는 인덱스와 관련된 원소에만 데이터가 쌓이는 primary clustering 현상이 발생하게 된다. 



### chainning (close addressing) 구현
```
typedef struct _data 
{
	int id;
	struct _data * next;
}data;					//링크드리스트를 위한 구조체 형식의 hash 함수 정의



#define MAX_ST		10
#define HASH_KEY	10		//chainning에서는 같은 해시값이 나올 경우 링크로 연결하기 때문에  HASH_KEY가 MAX_ST와 같아도 괜찮다.

SCORE Hash_table[HASH_KEY];		//global 변수로의 구현


int Get_Hash_Key(int id)
{
	return id % HASH_KEY;
}


void Init_Hash_Table(void)
{
	int i;

	for(i=0; i<HASH_KEY; i++)
	{
		Hash_table[i].next = (SCORE *)0;
	}
}


int Insert_Data(data * d)
{
	data* p = calloc(1,sizeof(data));
	data* head ;

	if(!p) return -1;			//메모리공간 부족시 -1 반환
	(*p)=(*d);
	p->next = (data*)0;			//맨 마지막에 집어넣을 꺼라서
	head = &Hash_table[Get_Hash_Key(p->id)];
	while(head->next)
		head=head->next;		
	head->next = p;				//next에 링크드리스트로 연결해준다.(다시말해 hash table의 초기값은 다 head일 뿐 데이터는 없다!!!
	return 1;
}

int Delete_Data(int id)
{
	data* head = &Hash_table[Get_Hash_Key(id)];		//해당 키의 첫번째 테이블 원소를 잡은 뒤
	data* temp;
	while(head->next)					//첫번째 값은 다 head이므로 head의 next에 대해 탐색을 시작한다.
	{	
		if(head->next->id == id){			//id가 일치하는 것이 있으면 지워버린다.
			temp = head->next;
			head->next= head->next->next;
			free(temp);
			return 1;
		}
		head= head->next;
	}


	return -1;



}

```
