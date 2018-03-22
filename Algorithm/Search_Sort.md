## Binary Search

이진탐색은 값의 위, 아래로의 bound를 잡고, 가운데로 잘라가며 탐색하여 원하는 값을 찾는 알고리즘이다.

이진탐색은 주로 주어진 조건을 만족하는 최소, 최댓값을 찾기 위해 사용된다.

이진탐색의 구현은 아래와 같다.

###Binary Search
```
int Binarysearch(int min, int max, int id){		//재귀 이용
	int m = (min+max)/2;
	if(min>=max)
		return -1;
	if(arr[m]==id)
		return m;
	if(arr[m]<id)
		return Binarysearch(m+1,max,id);
	else
		return Binarysearch(min,m,id);

}
int Binarysearch2(int min, int max, int id){		//loop이용
	int m;
	while(min<=max){		//탐색 종료 시점
		m = (min+max)/2;		//중간값을 기준으로 잡고
		if(arr[m]==id)
			return m;		//탐색 완료의 경우
		else if(arr[m]<id)
			min = m+1;		//중간값 위에 값이 있을 경우 탐색범위를 중간값 위로
		else				//중간값 아래 값이 있을 경우 탐색범위를 중간값 아래로
			max = m-1;

	}	
	return -1;

}
```


binary search의 특징은 정렬된 데이터에만 사용될 수 있다는 점이다. 

따라서 별도의 sort 알고리즘이 필요한 경우가 많다.

아래는 대표적인 nlogn의 time complexity를 가진 Quick Sort와 Merge Sort의 구현이다.


```

#define SWAP(x, y, t) ( (t) = (x), (x) = (y), (y) = (t) )
int app[30010];
int temparr[30010];
void Quicksort(int min,int max)		//Quick sort의 기본 논리는 pivot값을 기준으로 작은것은 아래, 큰것은 위로 옮긴 뒤 이를 재귀적인 용법을 통해 정렬하는 것이다. 따라서 재귀구문이 뒤에 온다.
{
	int temp,l,t,p = max;
	if(min>=max) return;		//sorting 종료의 경우.
	for(t = l = min;l<p;l++)		//t는 바꿀 위치의 데이터, l은 pivot과 비교할 데이터의 위치를 가리킨다.
		if(app[l]<app[p]){		//l이 pivot보다 작을 경우 t와 위치을 바꾸고 t를 1 증가시킨다.
			SWAP(app[l],app[t],temp);
			t++;
		}
	if(p!=t)				//모든 비교가 끝날 경우 pivot과 t의 위치를 교환한다.
		SWAP(app[t],app[p],temp);
	Quicksort(min,t-1);
	Quicksort(t+1,max);

	return;
}

void Mergesort(int min, int max){
	int idx1,idx2,idxtemp,m = (min+max)/2;
	if(min>=max) return;
	Mergesort(min,m);
	Mergesort(m+1,max);
	idx1 = min,idx2 = m+1,idxtemp=min;
	while((idx1<m+1)||((idx2<max+1))){
		if((idx1>m) || ((idx2<max+1)&&(app[idx2]<app[idx1])))
			temparr[idxtemp++] = app[idx2++];
		else
			temparr[idxtemp++] = app[idx1++];
	}
	for(idxtemp=min;idxtemp<max+1;idxtemp++) app[idxtemp] = temparr[idxtemp];
}
```

ex1) 문제 2 : 암소 야구 [Brian Dean, 2013]

N마리 암소 (3 <= N <= 1000)가 연속적으로 서 있으며, 각각은 숫자에 해당하는 좌표에 위치에 있습니다.

그들은 주변 농장의 소들과 중요한 경기를 준비하고 있습니다. 농부가 3 개의 소 (X, Y, Z) 그룹이 두 번의 던지기를 성공하는 것을 관찰합니다. 

소 X가 공을 오른쪽 Y에게  던지고 암소 Y가 공을 오른쪽 Z에게 던집니다. 농부는 두 번째 던지기가 최소 첫 번째 던진 거리만큼, 최대 첫 번째 던진 거리의 두배 2 배 이상 움직이지 않는다고 말합니다. 농부가 볼 수 있었던 소 (X, Y, Z)의 가능한 트리플 수를 세어보십시오.


```
int leaf[1000+10];
int temparr[1000+10];

void Mergesort(int min, int max){
	int idx1,idx2,idxtemp,m = (min+max)/2;
	if(min>=max) return;
	Mergesort(min,m);
	Mergesort(m+1,max);
	idx1 = min,idx2 = m+1,idxtemp=min;
	while((idx1<m+1)||((idx2<max+1))){
		if((idx1>m) || ((idx2<max+1)&&(leaf[idx2]<leaf[idx1])))
			temparr[idxtemp++] = leaf[idx2++];
		else
			temparr[idxtemp++] = leaf[idx1++];
	}
	for(idxtemp=min;idxtemp<max+1;idxtemp++) leaf[idxtemp] = temparr[idxtemp];
}


int BinarysearchUpper(int min, int max, int id){
	int m;
	int sol = min;
	while(min<=max){
		m = (min+max)/2;
		
		if(leaf[m]<id)
			sol = min = m+1;		//leaf[m]==d인 경우는 어차피 boundary 구하는거라 노상관
		else
			max = m-1;

	}	
	return sol;
}

int BinarysearchLower(int min, int max, int id){
	int m;
	int sol = max;
	while(min<=max){
		m = (min+max)/2;
		if(leaf[m]<=id)
			min = m+1;
		else
			sol = max = m-1;

	}	
	return sol;

}

int main(){
	int n,i,j;
	int ub,lb;
	int up,lo;
	int sum = 0;
	scanf("%d",&n);
	for(i=0;i<n;i++)
		scanf("%d",&leaf[i]);
	Mergesort(0,n-1);					//데이터가 임의로 들어오기 때문에 sort 필요
	for(i=0;i<n-2;i++)
		for(j=i+1;j<n-1;j++){
			lb = leaf[j]+ (leaf[j]-leaf[i]);
			ub = leaf[j]+ 2*(leaf[j]-leaf[i]);
			up = BinarysearchLower(j,n-1,ub);
			lo = BinarysearchUpper(j,n-1,lb);
			if(up==-1 || lo==-1)
				continue;
			if(up>=lo)
				sum+= (up-lo+1);
		}
	printf("%d",sum);
}

```
