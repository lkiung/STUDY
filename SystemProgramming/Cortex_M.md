# v7 Cortex - M


### 1. v7 Cortex M의 특징

* Thumb2 only (32bit, 16bit 혼용)
* 초저가화
* 일반적으로 XIP구조
* 저전력, 느린 clock속도
* Optionaly MPU.

### 2. Core, Processor, System on Chip

* Core: 연산장치. ALU, Decoder, Control Logic과 Register로 구성되어 있음. Menual: Architecture Reference Manual

* Processor: Core의 연산 성능 개선을 위한 MPU, GPU, FPU, Cache 등 Coprocessor와 SRAM 메모리, NVIC, SysTick, SCB 등 자주 사용되는 주변장치로 구성. Menual: Technical Reference Manual

* SoC: 제조사별 UART, GPIO, DMA, Flash Memory등 주변 장치(Peripheral)을 Processor에 더한 개념.  Manual: 제조사별 제품 reference manual

※ Processor에서 사용되는 것은 Coprocessor로 CP를 통해 관리하고 SoC에서 사용되는 것들은 Peripheral device로 Memory Mapped I/O를 통해 관리된다.

### 3. 기본 Register & AAPCS(Procedure Call Standard for ARM Architecture)

1) General-Purpose Register

R0-R3, R12: Scratch Register 함수 호출시 손상되는 Register. (Argument: R0-R3, Return Value: R0,R1). Callee에서 사용시 백업 불필요.

> C언어 함수로 interworking시 4B 이하의 Argument가 integral promotion되어 앞에서부터 r0, r1, r2, r3 순으로 채워진다. long long, long double의 데이터는 r1:r0, r3:r2가 한쌍이 되어 채워지며, 4B 초과의 구조체, 공용체는 stack을 통해 전달된다. 

R4-R11: Un-Scratch Register 함수내에서 범용적으로 사용하는 Register. Callee에서 사용시 백업 필요. 

SP(R13): Stack Pointer Register. Cortex M에는 Main Stack Pointer와 Process Stack Pointer 두가지가 bank형태로 존재한다.

LR(R14): Link Register. Caller에서 함수 호출시 Callee가 종료되고 복귀될 복귀주소 저장. 

PC(R15): Program Counter. 현재 실행되고 있는 명령의 주소를 지칭. (실질적으로는 Pipeline 때문에 현재 Execute 단계인 명령 + 8값이 PC가 된다)

xPSR: APSR(Application Program Status Register) + IPSR(Interrupt PSR) + EPSR(Execution PSR)

* APSR: 연산 결과를 저장하는 PSR ( Negative: xPSR[31], Zero: xPSR[30], Carry: xPSR[29], oVerflow: xPSR[28], Q(DSP명령의 saturation): xPSR[27]
* IPSR: Exception number를 저장 (xPSR[8:0])
* EPSR: 프로그램 수행에 필요한 정보저장. (ICI/IT;Interruptible, Continuable Instruction / If-Then. LDM, STM과 같은 Multiple Memory Access 명령어 수행중, 인터럽트가 발생했을 시, 중단지점의 레지스터 순서를 저장하는 field. + IT박스 명령어 수행중 인터럽트 발생시, 중단지점의 IT 옵션을 저장하는 field  :xPSP[26:25,15:10], T(Thumb인지 ARM명령어인지 저장. Cortex M은 T!=1이면 Usage Fault발생. : xPSR[24])

### 4. 조건부 명령

Cortex M은 분기명령에만 조건부 옵션 추가 가능. 그외의 명령어에 조건부 옵션을 쓰고 싶으면 IT(If-Then) 명령 활용.
IT명령어에서 T는 Then, E는 Else를 나타내며 I다음은 반드시 T, 이후에는 T또는 E가 최대 3개까지 더 나올 수 있다. ( E다음 T는 나올 수 없음)
아래는 IT블록을 사용한 예이다.

```
	cmp	r0, r1
	ite	gt
	movgt	r2, #-2
	movle	r2, #0
	it	eq
	moveq	r2, #1
```


>ITXXX(IT블록)에서의 T condition과 E condition

| Condition |    Z==1         |     C==1        |    N==1    |     V==1     | (C==1) && (Z==0)  |            N==V           |   (Z==0) && (N==V)     |
|-----------|-----------------|-----------------|------------|--------------|-------------------|---------------------------|------------------------|
|  Then     |  EQ(Equal)      |  CS(Carry Set)  |  MI(Minus) |  VS(V set)   | HI(higher)        | GE(greater than or eqaul) | GT(greater than)       |
|  Else     |  NE(Not Equal)  | CC(Carry Clear) |  PL(Plus)  |  VC(V clear) | LS(lower or same) |    LT(less than)          | LE(less than or equal) |


### 5. CMSIS

Cortex M에서는 peripheral register와 exception vector에 대한 이름과 이에 접근하는 함수를 인터페이스로 정의하여 다양한 미들웨어 vendors에 대해서도 통용되도록 하는 동시에 개발자가 C로도 쉽게 시스템 주변장치에 대한 제어를 할 수 있도록 하였다. 

### 6. Cortex M의 Boot

Cortex M을 Boot하는 방법은 아래 3가지 방법이 있다.

1. Assembly language로 처음부터 다 직접 코딩한다. 이때, 주의해야 할 것은 Cortex A나 R과는 달리 M에서는 code영역의 첫번째 명령어가 stack pointer의 초기값,두번째 명령어가 PC가 시작되는 지점(즉, Reset handler의 위치)로 설정되어야 한다는 것이다. 

2. BSP(Board Support Package)를 활용한다. BSP란 Board를 사용할 수 있도록 제조사에서 제공하는 Software묶음으로, Bootloader와 디바이스 드라이버를 포함한다. BSP를 Board에 porting하면 main()함수부터 프로그램이 시작하게 된다.

3. 컴파일러에게 부트로드, Cstartup 다 맡기고 C로 구현한다. 이때, CMSIS의 SystemInit()함수를 먼저 호출하여 Clock의 동기화를 진행하여야 한다. 이 방식의 치명적인 단점은 exception handler에 대한 처리가 어렵다는 것이다.

### 7. Thumb 2

ARM에서는 명령어를 다양화하여 빠른 처리가 가능하도록하는 32bit 체계의 ARM 명령어와 명령어를 단순화하고 메모리공간의 효율성을 늘리는 16bit 명령 체계인 Thumb 명령어가 있다. 앞서 EPSR에서 설명한 대로 PC에서 가리키는 명령어가 ARM 명령어일 때에는 T bit는 0이, Thumb 명령어일 때에는 1이 된다. 32bit 명령체계는 4B align, 16bit 명령체계에서는 2B align이 되므로 PC[0]은 항상 0이 된다. ARM에서는 이를 활용하여 Thumb에서는 PC[0]을 1로, ARM 명령체계에서는 PC[0]을 0으로 설정한 뒤, PC[0]값을 EPSR의 T bit에 자동으로 대입하는 방식으로 명령어를 해독하였다.

v7 부터는 이를 혼용하여 쓰는 Thumb2 명령체계가 등장한다. Thumb2명령어는 16bit, 32bit를 혼용해서 사용하며 명령어가 32bit 체계인지 16bit 체계인지를 구분하기 위해 상위 2B 값이 11101XXX일 경우 32bit 명령체계로, 아닐경우 16bit 명령체계로 Decode 한다. Thumb2의 T bit는 항상 1이므로 Brunch가 일어나는 경우 항상 홀수번지로 접근하여야 한다. C와의 interworking의 경우 Compiler가 자동으로 처리해주기 때문에 짝수 번지로 코딩하여야 한다. Thumb2는 16bit와 32bit를 혼용해서 쓰기 때문에 PC값을 2B단위로 업데이트한다.


### 8. Exception

* Exception: 일반적인 실행의 흐름을 바꾸는 이벤트. Exception은 잘못된 실행으로 인해 발생하는 오류인 fault와 프로그램이 요구하는 resoucre를 할당하는 interrupt로 크게 구분할 수 있다. 

* Cortex M 에서 동작모드

v7 arch. M에서는 동작모드를 다음과 같이 나눈다

| Mode | Description |   Registor |
|------|---------------|----------|
| Privileged | 프로세서 자원의 접근권한에 제약이 없는 상태. Reset상태에서 프로그램은 최초 Privileged mode이다. | CONTROL[0]==0 (CONTROL.TP==0) |
| Unprivileged | 프로세서 자원의 접근이 일부 제한된 상태. CONTROL 레지스터에 대한 접근이 없어 privileged mode로 접근 불가 | CONTROL[0]==1 |
| MSP  | Stack Pointer로 Main Stack Pointer를 사용한다. | CONROL[1]==0 (CONTROL.SP==0) |
| PSP  | Stack Pointer로 Process Stack Pointer를 사용한다. | CONTROL[1]==1   |

* Thread mode VS Handler Mode

Thread는 일반적인 프로그램이 실행될 때의 모드이다. reset시 기본 모드는 Thread, Privileged, MSP 이다.

Handler는 exception이 발생하여 exception handler가 실행되었을 때의 모드이다. exception이 발생하면 프로그램은 자동으로 Handler, Privileged, MSP로 전환된다.
Cortex M의 경우 A, R과는 달리 context saving을 하드워어적으로 수행하여 exception이 발생할 경우 Context가 자동으로 stack에 push된다. bx lr 명령어를 만나 handler의 동작이 끝나면 자동으로 stack의 값을 push하고, 기존 stack에 저장된 R.A의 위치로 분기한다.(즉, Handler모드에서 LR값은 복귀지점을 의미하지 않는다!!!)

stack에 저장되는 값은 아래와 같다.

| 주소 | SP(MSP) | SP+0x4 | SP+0x8 | SP+0xC | SP+0x10 | SP+0x14 | SP+0x18 | SP+0x1C | ~SP+0x60 |
|------|---------|--------|--------|--------|---------|---------|---------|---------|----------|
| 저장되는 값 | R0 | R1   |    R2  |  R3    |   R12   | LR(R14) | Return Address | xPSR | FPU명령 수행중일 경우 FPU 레지스터. 아닐경우 dummy value로 reserved 된다 |

Exception 발생시 LR의 값은 Exception 발생 당시의 모드와 사용하는 stack에 대한 정보, Stack Frame의 extension 여부(FPU의 경우) 를 저장하게 된다.
 
| LR value | Return to | Return Stack | Frame Type |
|-----------|-----------|---------|---------|
| 0xFFFFFFE1 | Handler | Main | Extended |
| 0xFFFFFFE9 | Thread | Main | Extended |
| 0xFFFFFFED | Thread | Process | Extended |
| 0xFFFFFFF1 | Handler | Main | Basic |
| 0xFFFFFFF9 | Thread | Main | Basic |
| 0xFFFFFFFD | Thread | Process | Basic |

>Handler mode이고 Process Stack을 사용하는 경우는 없다!  

### 9.Fault

v7-M에서는 Fault를 Memory Management Fault, Bus Fault, Usage Fault, Hard Fault 4가지로 구분하고 있다. 

Hard Fault는 특수한 상황에서 발생하는 Fault이고 일반적인 오류는 앞의 3가지로 분류된다.

각 Fault의 발생 허용 여부와 발생상황은 SCB(System Control Block)의 SHCSR(System Handler Cotrol and State Register)에 저장된다.

![SHCSR]()

SHCSR[18:16]은 각각 Usage, Bus, MM fault를 허용할지를 저장하고, 각 bit의 값이 0일 경우 각각의 fault는 hard fault로 인식된다. 

Fault의 각 세분화된 발생원인은 SCB->CFSR(Configurable Fault State Register)에 저장된다. CFSR[31:16]은 Usage Fault, CFSR[15:8]은 Bus Fault, CFSR[7:0]은 MMF를 나타낸다.

#### 9-1. Memory Management Fault

MMF는 프로세스 내에서 MPU에 정의된 접근권한이 위배되거나 제조사가 제한한 XN영역에 Fetch시 발생한다.

CFSR에서 나타나는 MMF는 아래와 같다.

1) IACCVIOL(Instruction Access Violation). CFSR[0]

	: MPU에서 실행금지한(XN) 영역을 Fetch할 때 발생한다. MPU가 없을 때에는 제조사에서 정한 Datasheet에서 XN영역으로 정의된 곳에 fetch할 때 발생한다.

```
0x100	ldx	r0, =0xE0000000		@XN영역
0x104	bx	r0
```

| 접근하려는 메모리주소 | R.A | MMFARVAL |
|-----------------------|-----|----------|
| 0xE0000000  | 0xE0000000 | 0 |

>MMFARVAL은 CFSR[7]로, Data Access시 R.A로 Fault가 발생한 주소값으로 복귀가 가능할 경우 1로 set된다.


2) DACCVIOL(Data Access Violation), CFSR[1]

	:MPU가 존재할 때 load, store 명령으로 읽기금지 혹은 쓰기 금지영역에 접근했을 때 발생한다. MPU가 없다면 발생하지 않는다.

>WB가 존재하면, store 명령어가 시행된 위치와 fault가 발생한 위치가 달라질 수 있다. 따라서 일반적으로 DACCVIOL은 MMFARVAL이 1이지만 WB가 존재하고 쓰기동작에서 오류가 났을 때, MMFARVAL은 0으로 clear된다.


3) MUNSTKERR(CFSR[3]), MSTKERR(CFSR[4])
	: MPU가 존재할 때, push시 (MSTKERR) 혹은 pop시(MUNSTKERR) SP가 MPU에서 제한한 STACK영역을 벗어나게 될 경우 발생한다.

>WB가 존재하면 Push에서 Fault가 발생할 시 위와 같은 이유로 MMFARVAL이 0이 된다.

### 9-2. Bus Fault

Bus Fault는 Core에서 MPU에서 허용한 메모리영역중 버스 컨트롤러에서 메모리 접근에 오류가 있을 경우 발생한다.

1) I-Bus(Instruction Bus Err). CFSR[8]

	: CPU가 명령어를 fetch할 때, PC가 가리키는 메모리주소가 물리적으로 존재하지 않을 경우

```
0x100	ldx	r0, =0x00010001		@존재하지 않는 주소
0x104	bx	r0
``` 

| 접근하려는 메모리주소 | R.A | BFARVAL |
|-----------------------|-----|----------|
| 0x00010000  | 0x00010000 | 0 |

>BFARVAL의 기능은 MMFARVAL의 기능과 같다. r0이 홀수인 이유는 앞서 언급한 대로, thumb2 체제이기 때문. MMF에서 r0를 짝수로 한 이유는 T bit를 확인하기 전에 MPU에서 접근권한을 먼저 확인해 MMF가 먼저 발생하기 때문이다.


2) PRECISE ERR(CFSR[9]), IMPRECISE ERR(CFSR[10])

	: 실행하고 있는 명령어가 데이터 영역에 접근할 때, 접근하려는 데이터 영역이 물리적으로 존재하지 않는 영역일 경우 Bus Fault가 발생한다. 이 중, RA값을 통해 문제가 일어난 명령어의 위치를 파악할 수 있는 경우를 Precise err, 파악할 수 없는 경우를 imprecise err라 한다.

>WB를 사용할 경우, Write동작에서 에러가 발생하면 실제 에러가 발생한 코드의 주소와 store 명령어가 있는 주소는 달라지게 된다. 따라서 WB가 있는데 store 동작을 할 경우 Imprecise err가 발생하며 이때, RA값은 원치 않은 주소값을 갖게 된다. 또한 BFARVAL값은 0이 된다. 이외의 데이터 접근시 Bus Fault는 데이터를 접근한 명령어의 위치를 파악할 수 있으므로 precise err가 되며 RA값이 exception을 발생시킨 메모리 주소가된다.

2-1) Unprivliged Access

	:비특권모드에서 특권모드일 때에만 접근이 가능한 resource를 접근할 시에도 Bus Fault가 발생한다. 일반적으로 특권모드에서 접근하는 영역은 Strongly Ordered라 WB를 사용하지 못하는 영역이므로 precise fault가 주로 발생한다.


3) UNSTKERR(CSFR[11]), STKERR(CFSR[12])

	: exception이 발생하면 v7-M3은 context saving을 위해 push, pop동작을 자동으로 하게 되는데 이때, sp의 값이 존재하지 않는 영역을 가리키게 되면 push시 stkerr, pop시 unstkerr가 발생하게 된다. 

>엄연히 Handler 처리를 다 하고 복구가 된 뒤 fault가 다시 발생하는 것이다!!! 따라서 Hard Fault issue와는 무관하다.


### 9-3. Usage Fault

v7-m3에서는 MMF, BF가 아닌 오류는 모두 Usage Fault로 구분한다.

1) DIVBY0(divided by 0), CFSR[24]

	:설명이 필요할까

2) UNALIGN(unaligned access), CFSR[23]

	:Data에 접근 시 align 되지 않은 데이터 주소로 접근하면 발생한다.

3) NOCP(No CP operation), CFSR[19]

	:존재하지 않은 Coprocessor에 대한 명령어를 수행하면 발생한다.

4) INVPC(Invalid PC), CFSR[18]

	:IRQ수행후 복귀할 때, 처리한 IRQ를 지칭하는 레지스터가 맞지 않으면 발생한다.

5) INVSTAT(Invalid status), CFSR[17]

	:T 비트가 0일때 즉, 홀수가 아닌 PC값에 접근했을 때 발생하는 fault이다.

>I_ACC_VIOL, INVSTAT, I-BUS FAULT중 어떤 것이 먼저 일어날까? v7-m에서는 PC값의 레지스터를 가져오면서 MPU에서 접근권한을 체크한다. 그 후 core 내에서 PC[0] bit을 확인하고 T bit을 업데이트 한다. 명령어가 MPU가 정의한 영역 내에는 없을 때에 BUS에 마지막으로 접근하여 명령어를 읽어온다. 따라서 PC가 fetch하는 과정에서 오류를 체크할때 가장 먼저 체크해야 하는 순서는 1) I_ACC_VIOL 2) INVSTAT 3)IBUS 순서가 된다.

6) UNDEFINST(undefined instruction), CFSR[16]

	:명령어를 decode하는 과정에서 해당 비트에 맞는 명령어가 정의되지 않았을 때에 발생하게 된다.
