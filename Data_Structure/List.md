# 1.

## 1-1 List

List는 C에서 기본적으로 제공하는 1차원 배열의 자료구조이다. List는 Array의 index로 데이터를 접근한다.

데이터의 위치를 지정하는 특별한 변수도 필요하지 않고, 데이터를 저쟝할 만큼의 메모리 용량만을 요구한다.

하지만 데이터를 삭제하거나 삽입하기 위해서는 뒤의 데이터를 모두 옮겨야 하는 단점이 있다.

일반적으로 Sort알고리즘을 통해 데이터를 정렬하고, Binary Search등을 이용해 필요한 데이터를 탐색한다.


### 선언

```
typedef struct _list

{

	int item;

}LIST;

#define LIST_MAX 20

LIST example[LIST_MAX];	
```

### Binary Search

업다운 게임처럼 데이터의 가운데값인 example[MAX/2]를 기준으로 탐색범위를 1/2씩 줄여가면서 원하는 데이터를 찾는 알고리즘
```
int BinarySearch(int left, int right, int value){
	int mean = (left+right)/2;
	if(example[mean].item<value){
		if(left<right) return BinarySearch(left, mean, value);
		else return -1; 
	}
	else if(example[mean].item>value{
		if(left<right) return BinarySearch(mean,right,value);
		else return -1;
	}
	else
		return example[mean].item;

}
```

### Sort

귀찮......

### Insert

```
int Insert_Data(LIST * p)
{
	int i,j,k;
	int max = 0;
	if(example[max].item==0){
		example[max]=*p;
		return max;
	}
	while(example[++max].item);

	if(max>=LIST_MAX)
		return -2;
	for(i = 0; i<max;i++)
		if(p->item == example[i].item) return -2;
		else if(p->item<example[i].item){
			for(j=max;j>i;j--)
				example[j]=example[j-1];
			example[i] = *p;
			return i;
		}
	example[max] = *p;
	return max;

}
```

### Delete

```
int Delete_Data(int item)
{
	int i = 0;
	int max = 0;
	int idx;
	while(example[++max].item);
 
	idx = BinarySearch(0,max,item);
	if(idx == -1 )
		return -1;
	for(i=idx;j<max-1;i++){
		if(exam[i+1].item==0)
			{
				exam[i].item=0;
				return i;
			}
		exam[i] = exam[i+1];
	}
	exam[i].item = 0;
	return i;
		
}
```
