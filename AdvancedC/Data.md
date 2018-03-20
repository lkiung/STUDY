## Data Type

Integer 계열

| Dtype      | Cap                    | expression     |
|------------|------------------------|----------------|
| char       | 1B                     |'10' (쉼표로 표시) |
| short      | 2B                     | 10s               |
| int (기본형)| Compiler마다 용량 다름 | 10 (그냥 쓰면 되고 진법은 prefix로 0x10과 같이) |
| long (int) | 4B                     | 10L (signed unsignd도 surfix. 10ul) |
| long long (int) | 8B                | 10LL (surfix는 소문자로 적어도 상관 없음) |


| Dtype      | standard   |
|------------|------------|
| char       | 0~127 |
| short      | -2^15+1 ~ 2^15 -1 |
| int (기본형)| -2^31 +1 ~ 2^31 -1 |
| long (int) | -2^31 + 1 ~ 2^31 -1 (옛날에는 int가 4B가 아니여서 만들어짐) |
| long long (int) | -2^63 +1 ~ 2^63-1  |
>siged가 default이지만 char는 ASCII Code를 표현하기 위해 만들어진 변수로 0~127이 표준이기 때문에 default가 unsigned, signed 둘중 임의 사용.



Double 계열

| Dtype    | Cap       | expression    |
|----------|-----------|---------------|
| float    | 4B        | 10.7f (실수는 signed unsigned가 없다 )  |
| double   | 8B        | 11.3 (소수는 기본이 double, 정수를 소수로 표현할때는 뒤에 점하나 10.) |
| long double | Compiler마다 다름 | 10.3L (실수에 L 붙이면 끝} |


## Memory Address 


Word: CPU가 한번에 읽어들이는 데이터의 크기. 32bit 체계 컴퓨터에서는 32bit가 1word가 된다.

원래는 이 word에 의해 int의 크기가 결정되지만 64bit 운영체제에서도 32bit 컴퓨터와의 호환성의 문제로 int의 크기는 32bit로 하였다.

메모리는 데이터를 32bit = 4Byte 단위로 유지하여 데이터를 관리하여 CPU의 데이터 접근을 용이하게 한다. 

앞서 dtype에서 용량의 최소 크기는 1B. 따라서 메모리는 주소를 1B마다 할당하여 데이터를 관리한다. 보통 4B단위로 데이터를 접근하기 때문에 일반적으로 메모리 구조를 그릴 때에는 4xK의 배열로 표현한다.



## Big Endian vs Little Endian

0x12345678을 대입하기 위해서

| Dtype  |   0x1003 | 0x1002 | 0x1001 | 0x1000 |
|--------|----------|--------|--------|--------|
| BigEndian | 78    | 56     | 34     | 12     |
| LittleEndian | 12 | 34     | 56     |  78    |


* Big Endian: 작은 bit를 큰 주소값에!

MSB부터 전송되기 때문에 데이터의 부호가 중요한 통신쪽에 주로 사용된다.

* Little Endian: 작은 bit를 작은 주소값에!

데이터를 접근할 때 type에 따른 별도의 캐스팅 작업 없이 불러들이기만 하면 된다는 장점이 있다.

인텔 CPU의 CISC 명령체계의 경우 8bit CPU부터 64bit CPU까지 모두 호환되게 하기 위해 메모리 양 별 데이터에 접근하는 명령어를 다르게 해두어서 Little Endian이 유리하다.



# 정수형 데이터의 표현

음수의 데이터를 표현하는 방법에는 3가지가 존재한다.


1) signed magnitude: MSB는 부호를 표시하고 나머지는 크기를 표현하는 방식

ex) -12 in 8 bit =  10001100

장점: 이해하기 쉽다.

단점: 음수와 양수의 덧셈연산이 어렵다. 0이 2개라 표현할 수 있는 수가 효율적이지 않다.


2) 1's compliment: 양수로 표현된 데이터를 완전 보수 취하여 표현

ex) -12 in 8 bit = 11110011

장점: 계산과정이 나름 복잡하지 않다. 음수를 취하는것도 쉽고 하지만 0이 얘도 두개라 별로

> n진법에서의 n-1의 보수는 합이 n-1이 되도록 자릿수를 다 바꾸는 것을 의미한다.


3) 2's compliment: 1's compliment 에 1을 더해 표현

장점: 음수와 양수의 덧셈연산에 추가적 구현 없이 그냥 더하면 연산된다. 거기다 0도 한개!

많은 cpu가 2's compliment를 음수표현으로 채택.
>n진법에서의 n의 보수는 자릿수의 합이 carry까지 계산해서 n이 되도록 하는것!


* C에서는 셋다 포함할 수있도록 데이터 타입의 범위를 정하였다.


# 실수의 데이터 표현

C에서 실수는 IEEE 754의 Floatig Point를 따른다. 

Floating Point는 중학교 과학시간에 배운 실수의 표기방법으로 1.xxx*10^y 과 같이 표현하는 방식을 말한다.

컴퓨터는 이진체계니까 우리는 (-1)^s * 1.xxx*2^y으로 수를 표현한다.

이때 부호를 나타내는 s를 sign bit, 유효숫자(가수부)를 나타내는 xxxx를 mantisa bits(or Fragment bits), 지수를 나타내는 y를 exponent bits라 한다.

각 type별 bit 할당량은 아래와 같다

| Type | sign bit | exponent bits | mantissa bits  |
|------|----------|---------------|----------------|
| float|    1     |       8       |     23         |
|double|    1     |      11       |      52        |


* Normalization vs Denormalization

floating point의 mantissa bit은 2진법으로 표현되므로 유효숫자가 항상 1로 시작하게 된다. (아니면 exponent를 바꾸면 됨)

따라서 맨앞의 1을 제외한 나머지 범위 즉, 1.xxxxxxxx 의 x부분만 mantissa bit으로 표현하는데 이를 normalization이라 한다.

반대로 exponent bits를 최소화한 값 보다 작은 값의 경우 0.000xxxx * 2^(exponents 최솟값) 으로 표현해도 유효숫자가 0으로 시작할 수 있다.

이런 경우는 mantissa bits은 정수부를 포함하여 표현하게 되는데 이를 denormalization라 한다.


* exponents bits의 범위

float을 예로 들면 exponents bits는 8bit 으로 총 0~255까지 표현할 수 있게 된다. IEEE754에서는 음수범위의 지수도 표현하기 위해 bias를 127로 두고 빼는 방식을 택했다. 

따라서 실제 2진법의 표현은

(-1)^s * 1.mmmmmmmmmmmmmmmmmmmmmmm * 2^(eeeeeeee-127) (normalized의 경우) 가 된다.

denormalized의 경우 exponents bit이 전부 0이 된다. IEEE에서는 denormalized와 normalized의 최소값에서 데이터 간격에 의한 손실을 최소화하기 위해 exponent bits이 모두 0일 경우 exponent를 -126으로 정의하였다.

exponent bit과 mantissa bit에 따른 값을 아래와 같다.


|exponent bits| mantissa bits |     의미      |
|-------------|---------------|---------------|
| 0000.....00 | 000000....000 |  0(sign bit에 따라 +0, -0 구분) |
| 1111.....11 | 1xxxxx....xxx | Q_NAN(값을 정할 수 없음) |
| 1111.....11 | 000000....000 | sign bit에 따라 +-Inf |
| 1111.....11 | 0xxxxx....xxx | S_NAN(연산 오류/ 최소 1개bit은 1이다) |
