# 0. Memory Structure

파일을 실행할 때, 파일이 메모리에 저장되는 영역은 크게 아래와 같이 나뉜다. 
![MEMORY](https://blog.naver.com/wjdrnwk/20187403073)
먼저 메모리의 0번지쪽부터 프로그램을 실행하기 위한 코드, 함수가 저장되는 Code영역, 전역변수가 저장되는 Data, BSS 영역, 동적 할당된 변수가 저장되는 Heap영역과 마지막으로 함수가 실행되면서 함수 내에 선언된 변수가 저장되는 Stack 영역으로 나뉜다.

Data영역은 다시 텍스트, Const 변수등 읽기 전용 데이터가 저장되는 Read-Only Data영역과 Static을 통해 선언된 초기화된 전역변수 (Strong Global Symbol, Static Symbol)가 저장되는 영역으로 나뉜다.

초기화되지 않은은 전역변수(Weak Symbol)는 BSS(Block Started By Symbol)영역에 저장되어 프로그램이 시작할 때 0으로 초기화된다.

Heap영역은 메모리 영역에서 가장 큰 용량을 차지하는 공간으로, 데이터가 Run-time에 동적 할당될 때 변수를 할당하는 공간이다.

마지막으로 Stack영역은 함수가 진행됨에 따라 함수 내에서 변수를 선언할 때 할당하는 공간이다.

Code영역과 Data영역, BSS영역은 파일이 실행되기 전에 메모리에 올라가는 데이터로, 실행파일의 크기를 결정한다.

반면 Heap영역과 Stack영역은 크기가 정해지지 않은 영역으로, Heap영역은 데이터를 동적으로 할당할 때마다 크기가 증가하며 Runtime에 크기가 결정된다.
Stack영역의 경우 코드의 함수가 실행됨에 따라 그 크기가 결정되어 Compile time에 결정된다.

C언어에서 데이터에 할당되는 영역은 다음과 같다
```
#include<stdio.h>

#define STACK_MAX 10                //Preprocessor가 Compile전에 STACK_MAX부분에 10을 대입.

int STACK[STACK_MAX];               //BSS에 저장되어 Preprocessor가 실행되면서 0으로 초기화 해줌

int top = 0;                        //DATA영역에 저장되어 global symbol로 사용된다.

int main(void)                      //CODE영역에 저장. STACK영역에서 함수를 호출하여 Parameter, Return Address 등 저장.
{ int a;                            //함수 내에 할당된 Static이 이난 변수는 STACK 영역에 저장
  int *p = malloc(8*sizeof(int));   //변수 p는 int* type의 변수로 함수 내에 선언되었으므로 STACK영역에 저장! malloc을 통해 데이터를 HEAP영역에 할당하고,
}                                   //이를 가리키기 위해 포인터 p의 값을 heap에 할당된 데이터의 주소값을 지칭하게 한것!
```

