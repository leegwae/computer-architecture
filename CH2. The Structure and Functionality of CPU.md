# CH2. CPU와 구조와 기능

**CPU의 기능**

- 모든 명령어에 대하여 공통적으로 수행

(1) **명령어 인출(instruction fetch)**: 기억장치로부터 명령어를 하나씩 읽어온다.

(2) **명령어 해독(instruction decode)**: 수행할 동작을 결정하기 위하여 명령어를 해독

- 명령어에 따라 필요한 경우에만 수행

(3) **데이터 인출(data fetch)**: 명령어 실행을 위해 데이터가 필요할 경우, 기억장치 혹은 I/O 장치로부터 데이터를 읽어옴

(4) **데이터 처리(data process)**: 데이터에 산술적/논리적 연산 수행

(5) **데이터 저장(data store)**: 연산을 수행한 결과를 저장



**2.1 CPU의 기본 구조**

- **산술논리연산장치(Arithmetic and Logical Unit, ALU)**: 산술/논리 연산을 수행하는 회로들로 이루어진 하드웨어 모듈
- **레지스터 세트(register set)**: 레지스터의 집합
  - **레지스터(register)**: CPU 내부의 일시적인 기억장치. 
    - 액세스 속도가 빠르다. 
    - 적은 수의 레지스터(특수 목적용과 적은 수의 일반 목적용 레지스터)
- **제어 유닛(control unit)**:프로그램 코드(명령어)를 해석하고 실행하기 위한 제어 신호(control signs)를 순차적으로 발생시키는 하드웨어 모듈
- CPU 내부 버스(CPUN internal bus): (1) ALU와 레지스터들 간의 데이터 이동을 위한 데이터/주소 선들, (2)제어 유닛으로부터 발생되는 제어 신호들을 전송하는 선들로 구성
  - CPU 내부의 버퍼 레지스터(MBR, MAR) 혹은 인터페이스 회로를 통해 CPU 외부의 시스템 버스와 접속한다.



**2.2 명령어 실행**

- **명령어 사이클(instruction cycle)**: <u>한 개의 명령어</u>를 실행하는 데 필요한 과정.
  - CPU가 프로그램을 실행한 순간부터 (1) 전원을 끄거나 (2) 회복 불가능한 오류가 발생하여 중단될 때까지 반복
- 두 개의 서브사이클(subcycle)로 구성 
  - (1) **명령어 인출(instruction fetch)** : CPU가 기억장치로부터 명령어를 읽어옴
  - (2) **명령어 실행(instruction execution)**: 일출된 명령어를 실행



**명령어 실행에 필요한 CPU 내부 레지스터**

- **PC(Program Counter, 프로그램 카운터)**: <u>다음에 인출할 명령어</u>의 주소를 가지고 있는 레지스터.
  - 명령어 인출 후에 자동으로 일정 크기가 증가.
  - 분기(branch) 명령어가 실행되는 경우 해당 목적지 주소로 갱신
- **ACC(Accumulator, 누산기)**: 데이터를 일시적으로 저장하는 레지스터.
  - 레지스터의 비트 수: CPU가 한 번에 연산 처리할 수 있는 데이터 비트의 수(단어 길이)와 같음
- **IR(Instruction Register, 명령어 레지스터)**: 기억장치에서 CPU로 가장 최근에 인출된 명령어가 저장된 레지스터
- **MAR(Memory Address Register, 기억장치 주소 레지스터)**: PC에 저장된 명령어 주소가 시스템 주소 버스로 출력되기 전 일시적으로 저장되어있는 주소 레지스터. 
- **MBR(Memory Buffer(Data) Register, 기억장치 버퍼 레지스터)**: 기억장치로부터 읽거나 그것에 쓸 데이터를 일시적으로 저장하는 버퍼 레지스터.



**2.2.1 명령어 인출 사이클**

- **인출 사이클(fetch cycle)**: CPU가 기억장치의 지정된 주소로부터 명령어를 읽어오는 과정
- **마이크로 연산(micro-operation)**: CPU clock의 각 주기 동안 수행동되는 동작

$$
\begin{align*}
t_0&:\mathrm{MAR\leftarrow PC} \\
t_1&:\mathrm{MBR\leftarrow M[MAR], PC\leftarrow PC+1} \\
t_2&:\mathrm{IR\leftarrow MBR}
\end{align*}
$$

- (예 )인출 사이클 소요 시간 계산
  - CPU의 클록 주파수: 1GHz=$10^9$
  - 클록 주기: 1ns=1/1GHz=$10^{-9}$
  - 인출 사이클: 1ns * 3 = 3ns



**2.2.2 명령어 실행 사이클**

- **실행 사이클(execution cycle)**: CPU가 인출된 명령어 코드를 해독(decode)하고, 그 결과에 따른 연산을 수행함.
- CPU가 수행하는 연산
  - **데이터 이동**: CPU와 기억장치 혹은 I/O 장치 간 데이터 이동
  - **데이터 처리**: 데이터에 대한 산술/논리 연산 실행
  - **데이터 저장**: 연산의 결과 데이터 혹은 입력장치로부터 읽어들인 데이터를 기억장치에 저장
  - **프로그램 제어**: 프로그램의 실행 순서를 결정



- $\mathrm{LOAD\ }addr$: 기억장치의 데이터를 AC로 적재(load)

$$
\begin{align*}
t_0&:\mathrm{MAR\leftarrow IR(addr)}\\
t_1&:\mathrm{MBR\leftarrow M[MAR]} \\
t_2&:\mathrm{AC\leftarrow MBR}
\end{align*}
$$

- $\mathrm{STR}\ addr$: AC의 데이터를 기억장치에 저장

$$
\begin{align*}
t_0&:\mathrm{MAR\leftarrow IR(addr)}\\
t_1&:\mathrm{MBR\leftarrow AC}\\
t_2&:\mathrm{M[MAR]\leftarrow MBR}
\end{align*}
$$

- $\mathrm{Add\ }addr$: 기억장치의 데이터를 AC의 데이터와 더하고, 그 결과를 AC에 저장

$$
\begin{align*}
t_0&:\mathrm{MAR\leftarrow IR(addr)}\\
t_1&:\mathrm{MBR\leftarrow M[MAR]}\\
t_2&:\mathrm{AC\leftarrow AC+MBR}
\end{align*}
$$

- $\mathrm{JUMP\ }addr$: 분기 명령어. PC의 내용을 바꾸어 다른 위치의 명령어로 실행 순서를 바꿈.

$$
t_0:\mathrm{PC\leftarrow IR}(addr)
$$



**2.2.3 인터럽트 사이클**

- **인터럽트(interrupt)**: <u>순차적인 명령어 실행을 중단하고 다른 동작</u>을 수행하도록 하는 시스템 동작
  - CPU와 외부장치들 간의 상호작용을 위해 필요한 기능
- **인터럽트 서비스 루틴(Interrupt Service Routine, ISR)**: (1) 외부로부터 인터럽트 요구가 들어오면, (2) CPU는 원래의 프로그램을 수행을 중단하고(<u>현재 실행하고 있는 명령어는 마저 실행한다</u>) (3) 요구된 인터럽트를 처리하기 위한 프로그램을 수행(= 해당 ISR 수행) (4) 인터럽트에 대한 처리가 끝나면 원래의 프로그램으로 복귀한다.
- **인터럽트 사이클(interrupe cycle)**: (1) 인터럽트 요구 신호 검사 (2) 현재 PC의 내용 스택에 저장 (3) PC에 해당 ISR의 시작 주소를 적재
- 인터럽트 시작 전 인터럽트 가능 상태(interrupt enabled)를 검사한다.
- **스택 포인터(Stack Pointer, SP)**: CPU 내부의 특수 목적용 레지스터. 항상 스택의 최상위 주소를 가리킨다.

$$
\begin{align*}
t_0&:\mathrm{MBR\leftarrow PC}\\
t_1&:\mathrm{MAR\leftarrow SP,\ PC\leftarrow ISR의\ 시작\ 주소}\\
t_2&:\mathrm{M[MAR]\leftarrow MBR,\ SP\leftarrow SP-1}
\end{align*}
$$



**다중 인터럽트(multiple interrupt)**

- 다중 인터럽트: ISR를 수행하는 도중 다른 장치의 ISR이 발생하는 것
- 다중 인터럽트의 처리 방법
  - (1) 다른 ISR 실행하지 않기: interrupt disabled 상태일 때는 CPU가 인터럽트 요구 신호를 검사하지 않도록 함. interrupt enabled 상태가 되면 인식됨.
  - (2) 인터럽트 요구 간 우선순위 정하기: 우선순위가 더 높은 인터럽트 요구가 들어오면 수행하고 있는 ISR 멈추고 새로운 ISR 처리함.



**2.2.4 간접 사이클(indirect cycle)**

- **간접 사이클**: 명령어에 적힌 주소가 [연산에 사용할 데이터의 주소]가 저장된 메모리의 주소를 가리킬 경우, [연산에 사용할 데이터의 주소]를 인출하는 사이클. 인출 사이클과 실행 사이클 사이에 위치한다.

$$
\begin{align*}
t_0&:\mathrm{MAR\leftarrow IR}(addr)\\
t_1&:\mathrm{MBR\leftarrow M[MAR]}\\
t_2&:\mathrm{IR}(addr)\mathrm{\leftarrow MBR}
\end{align*}
$$

- 간접 주소지정 방식(indirect addressing mode)에서 사용