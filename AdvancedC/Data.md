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

