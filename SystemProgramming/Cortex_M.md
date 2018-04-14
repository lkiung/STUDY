# Cortex - M


### 1. Cortex M의 특징

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

### 3. Register

1) General-Purpose Register

R0-R3, R12: Scratch Register 함수 호출시 손상되는 Register. (Parameter: R0-R3, Return Value: R0,R1). Callee에서 사용시 백업 불필요.

* C언어 함수로 interworking시 Parameter가 앞에서부터 r0부

R4-R11: Un-Scratch Register 함수내에서 범용적으로 사용하는 Register. Callee에서 사용시 백업 필요. 

SP(R13): Stack Pointer Register. Cortex M에는 Main Stack Pointer와 Process Stack Pointer 두가지가 bank형태로 존재한다.

LR(R14): Link Register. Caller에서 함수 호출시 Callee가 종료되고 복귀될 복귀주소 저장. 

PC(R15): Program Counter. 현재 실행되고 있는 명령의 주소를 지칭. (실질적으로는 Pipeline 때문에 현재 Execute 단계인 명령 + 8값이 PC가 된다)

xPSR: APSR(Application Program Status Register) + IPSR(Interrupt PSR) + EPSR(Execution PSR)

* APSR: 연산 결과를 저장하는 PSR ( Negative: xPSR[31], Zero: xPSR[30], Carry: xPSR[29], oVerflow: xPSR[28], Q(DSP명령의 saturation): xPSR[27]
* IPSR: Exception number를 저장 (xPSR[8:0])
* Execution PSR: 프로그램 수행에 필요한 정보저장. (ICI/IT;Interruptible, Continuable Instruction / If-Then. LDM, STM과 같은 Multiple Memory Access 명령어 수행중, 인터럽트가 발생했을 시, 중단지점의 레지스터 순서를 저장하는 field. + IT박스 명령어 수행중 인터럽트 발생시, 중단지점의 IT 옵션을 저장하는 field  :xPSP[26:25,15:10], T(Thumb인지 ARM명령어인지 저장. Cortex M은 T!=1이면 Usage Fault발생. : xPSR[24])

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

3. 컴파일러에게 부트로드, Cstartup 다 맡기고 C로 구현한다. 이때, CMSIS의 SystemInit()함수를 먼저 호출하여 Clock의 동기화를 진행하여야 한다.
