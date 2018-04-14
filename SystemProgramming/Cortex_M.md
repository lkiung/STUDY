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
* Execution PSR: 프로그램 수행에 필요한 정보저장. (ICI/IT;Interruptible, Continuable Instruction / If-Then. LDM, STM과 같은 Multiple Memory Access 명령어 수행중, 인터럽트가 발생했을 시, 중단지점의 레지스터 순서를 저장하는 field.  :xPSP[26:25,15:10], T(Thumb인지 ARM명령어인지 저장. Cortex M은 T!=1이면 Usage Fault발생. : xPSR[24])

