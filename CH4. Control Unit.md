# CH4. Control Uint

**제어 유닛의 역할**

- 기계 명령어들을 인출, 해독하고 실행하는 과정이 순차적으로 발생되도록, 제어 신호를 생성하여 해당 하드웨어 모듈로 보낸다.



## 4.1 제어 유닛의 기능

**제어 유닛의 기능**

- (1) 명령어 코드를 해독한다.
- (2) 명령어 실행에 필요한 제어 신호들을 발생시킨다.



**마이크로명령어(microinstruction)**

- **마이크로-연산(microoperation)**: 명령어 사이클의 각 주기에서 실행됨.
- **마이크로명령어**: 각 마이크로-연산을 지정하는 2진 비트 표현. 제어 단어(control word)라고도 한다.
- **마이크로프로그램(microprogram)**: 마이크로명령어들의 집합
- **루틴(routine)**: CPU의 특정 기능을 수행하기 위한 마이크로프로그램



## 4.2 제어 유닛의 구조

<img src=".\Images\4-1.PNG" alt="4-1" style="zoom:30%;" />

**제어 유닛의 구성 요소**

- (1) **명령어 해독기(instruction decoder)**: 명령어 레지스터(IR)로부터 들어오는 <u>명령어의 연산 코드를 해독</u>하여, 해당 연산을 수행하기 위한 <u>루틴의 시작 주소를 결정</u>
- (2) **제어 주소 레지스터(control address register, CAR)**: <u>다음에 실행할 마이크로 명령어의 주소를 임시</u>로 저장.
  - 마이크로 명령어는 제어 기억장치(제어 메모리)에 저장되어 있음.
- (3) **제어 기억장치(control memory)**: <u>마이크로프로그램을 저장</u>하는 내부 기억장치
- (4) **제어 버퍼 레지스터(control buffer register, CBR)**: 제어 기억장치로부터 읽은 <u>마이크로명령어 비트들을 임시로</u> 저장
- (5) **서브루틴 레지스터(subroutine register, SBR)**: 마이크로프로그램에서 <u>서브루틴이 호출되는 경우</u>, <u>현재 CAR의 내용을 일시적으로 저장</u>
- (6) **순서제어 모듈(sequencing module)**: 마이크로명령어의 실행 순서를 결정하는 회로들의 집합



**CPU의 명령어 세트 설계하기**

- (1) 명령어의 종류 및 비트 패턴 정의하기
- (2) 명령어 실행에 필요한 하드웨어 설계하기
- (3) 각 명령어를 위한 실행 사이클 루틴 작성하기(=마이크로프로그래밍)
- (4) 마이크로프로그램코드를 제어 기억장치(ROM)에 저장

마이크로프로그램의 크기에 따라 ROM의 크기가 결정되므로, ROM이 CPU 내 면적을 많이 차지하지 않도록 마이크로프로그램 코드를 간결하게 작성해야한다.



<img src=".\Images\4-2.PNG" alt="4-2" style="zoom:33%;" />

**CPU에 마이크로프로그램 루틴 저장하기**

(예) 마이크로프로그램 루틴을 제어 기억장치에 저장할 때

- 제어 기억장치의 용량 = 128 단어
- 0~63번지(64단어) = 공통 루틴들(인출, 간접, 인터럽트 사이클을 위한 루틴) 저장
- 64~127번지(64단어): 각 명령어의 실행 사이클 루틴 저장



**인출 사이클에서 명령어 해독하기**

<img src=".\Images\4-3.PNG" alt="4-3" style="zoom:33%;" />

- 인출 사이클 동안 IR로 적재된 명령어 비트들 중 연산 코드가 제어 유닛의 명령어 해독기로 들어옴.
- "**명령어를 해독한다**"의 의미: 연산 코드가 지정하는 연산을 위한 <u>실행 사이클 루틴의 시작 주소를 찾는 것</u>.
- **사상(mapping)**을 통해 해독하기: 명령어의 연산 코드와 특정 비트 패턴을 혼합하여 실행 사이클 루틴의 시작 주소를 찾아낸다.
  - (예) 1 X X X X 0 0 : X 자리에 op code를 넣고, 10진수로 바꾸어 사이클의 시작 주소 찾아내기
- 사상으로 찾아낸 실행 사이클 루틴의 시작 주소를 CAR에 적재한다. 명령어 실행 사이클이 시작되면 해당 위치부터 마이크로명령어를 순차적으로 인출한다.



## 4.3 마이크로명령어의 형식

(예) [ 연산필드 1(3 bit) | 연산필드2(3 bit) | 조건필드(2 bit) | 분기필드(2 bit) | 주소필드(ADF) (7bit) ]

- 연산 피드가 두 개인 경우, 두 개의 마이크로연산을 동시에 수행할 수 있다.
- **조건(CD) 필드**: 분기에 사용될 조건 플래그를 지정한다.
- **분기(BR) 필드**: 분기의 종류와 다음에 실행할 마이크로명령어의 주소를 결정하는 방법을 명시한다.
- **주소 필드(ADF)**: 분기가 발생하는 경우, 목적지 마이크로명령어의 주소를 명시한다.



**[표 4-1] 마이크로연산들의 예시**

(a) 연산필드 1에 위치할 마이크로 연산들

| 코드 | 마이크로-연산들                   | 기호  |
| ---- | --------------------------------- | ----- |
| 000  | $\mathrm{None}$                   | NOP   |
| 001  | $\mathrm{MAR\leftarrow PC}$       | PCTAR |
| 010  | $\mathrm{MAR\leftarrow IR}(addr)$ | IRTAR |
| 011  | $\mathrm{AC\leftarrow AC+MBR}$    | ADD   |
| 100  | $\mathrm{MBR\leftarrow M[MAR]}$   | READ  |
| 101  | $\mathrm{AC\leftarrow MBR}$       | BRTAC |
| 110  | $\mathrm{IR \leftarrow MBR}$      | BRTIR |
| 111  | $\mathrm{M[MAR]\leftarrow MBR}$   | WRITE |



(b) 연산필드 2에 위치할 마이크로 연산들

| 코드 | 마이크로-연산들                  | 기호  |
| ---- | -------------------------------- | ----- |
| 000  | $\mathrm{None}$                  | NOP   |
| 001  | $\mathrm{PC\leftarrow PC+1}$     | INCPC |
| 010  | $\mathrm{MBR\leftarrow AC}$      | ACTBR |
| 011  | $\mathrm{MBR\leftarrow PC}$      | PCTBR |
| 100  | $\mathrm{PC\leftarrow MBR}$      | BRTPC |
| 101  | $\mathrm{MAR\leftarrow SP}$      | SPTAR |
| 110  | $\mathrm{AC\leftarrow AC-MBR}$   | SUB   |
| 111  | $\mathrm{PC\leftarrow IR(addr)}$ | IRTPC |







**[표 4-2] 조건 필드의 코드 지정**

| 코드 | 조건              | 기호 | 설명                                                         |
| ---- | ----------------- | ---- | ------------------------------------------------------------ |
| 00   | 1                 | U    | 현재의 마이크로명령어 실행 완료 후 무조건 분기(unconditional branch) |
| 01   | $\mathrm{I}$ 비트 | I    | $\mathrm{I}$ 비트는 간접 주소지정 방식을 나타내는 비트. $\mathrm{I}$ = 1이면, 간접 사이클 루틴을 호출 |
| 10   | $\mathrm{AC(S)}$  | S    | S 비트는 AC에 저장된 데이터의 부호. S = 1이면 분기           |
| 11   | $\mathrm{AC=0}$   | Z    | AC에 저장된 데이터 = 0(Z=1)이면 분기                         |



**분기 필드의 코드 지정**

| 코드 | 기호            | 설명                                                         |
| ---- | --------------- | ------------------------------------------------------------ |
| 00   | $\mathrm{JMP}$  | 조건부 점프(conditional jump) <br />조건 = 1이면, $\mathrm{CAR\leftarrow ADF}$<br />조건 = 0이면, $\mathrm{CAR \leftarrow CAR+1}$<br />조건 필드 값이 00이면 $\mathrm{ADF}$가 가리키는 주소로 무조건 점프(unconditional jump) |
| 01   | $\mathrm{CALL}$ | 조건부 호출(conditional call)<br />조건 = 1이면, $\mathrm{SBR\leftarrow CAR+1, CAR\leftarrow ADF}$<br />조건 = 0이면, $\mathrm{CAR \leftarrow CAR+1}$<br />조건 필드 값이 00이면 무조건 호출(unconditional call) |
| 10   | $\mathrm{RET}$  | $\mathrm{CAR\leftarrow SBR}$ (서브루틴으로부터의 복귀)       |
| 11   | $\mathrm{MAP}$  | $\mathrm{CAR(1)\leftarrow 1, CAR(2-5)\leftarrow IR(op), CAR(6, 7)\leftarrow 0}$<br />== 사상으로 얻어진 주소를 CAR에 적재 |





## 4.4 마이크로프로그래밍

**4.4.1 인출 사이클 루틴**
$$
\begin{align*}
&\mathrm{ORG\ 0} &\mathrm{U\ JMP\ NEXT;\ } &루틴이\ 제어\ 기억장치의\ 0번지부터\ 시작 \\
\mathrm{FETCH:\ } &\mathrm{PCTAR} &\mathrm{U\ JMP\ NEXT;\ } &\mathrm{MAR\leftarrow PC,\ }다음\ 마이크로명령어\ 실행 \\
&\mathrm{READ, INCPC} &\mathrm{U\ JMP\ NEXT;\ } &\mathrm{MBR\leftarrow M[MAR],\ } \\
& & &\mathrm{PC=PC+1} \\
& & &다음\ 마이크로명령어\ 실행 \\
&\mathrm{BRTIR} &\mathrm{U\ MAP;\ } &\mathrm{IR\leftarrow MBR,\ } \\
& & &해당\ 실행 \ 사이클\ 루틴으로\ 분기 \\
\end{align*}
$$
**인출 사이클 루틴의 2진 비트 패턴**

- 주소: 각 마이크로명령어가 저장될 제어 기억장치 주소
- $\mu$-ops: 두 개의 마이크로 연산
- CD: 조건 필드
- BR: 분기 필드
- ADF: 주소 필드

| 주소    | $\mu$-ops | CD   | BR   | ADF                                              |
| ------- | --------- | ---- | ---- | ------------------------------------------------ |
| 0000000 | 001 000   | 00   | 00   | 0000001                                          |
| 0000001 | 100 001   | 00   | 00   | 0000010                                          |
| 0000010 | 110 000   | 00   | 11   | 0000000<br />(map에서는 ADF를 사용하지 않으므로) |



**4.4.2 간접 사이클 루틴**
$$
\begin{align*}
&\mathrm{ORG\ 4} &\mathrm{U\ JMP\ NEXT;\ } &루틴이\ 제어\ 기억장치의\ 4번지부터\ 시작 \\
\mathrm{INDRT:\ } &\mathrm{IRTAR} &\mathrm{U\ JMP\ NEXT;\ } &\mathrm{MAR\leftarrow IR}(addr),\ 다음\ 마이크로명령어\ 실행 \\
&\mathrm{READ} &\mathrm{U\ JMP\ NEXT;\ } &\mathrm{MBR\leftarrow M[MAR],\ }다음\ 마이크로명령어\ 실행 \\
&\mathrm{BRTIR} &\mathrm{U\ RET;\ } &\mathrm{IR}(addr)\mathrm{\leftarrow MBR,\ }실행 \ 사이클\ 루틴으로\ 복귀 \\
\end{align*}
$$


**간접 사이클 루틴의 2진 비트 패턴**

| 주소    | $\mu$-ops | CD   | BR   | ADF     |
| ------- | --------- | ---- | ---- | ------- |
| 0000100 | 010 000   | 00   | 00   | 0000101 |
| 0000101 | 100 000   | 00   | 00   | 0000110 |
| 0000110 | 110 000   | 00   | 10   | 0000000 |

- 명령어가 간접 주소지정 방식을 사용하는 경우 명령어 내 $I$ 비트가 1로 세팅.
- $\mathrm{IR}$에 적재된 명령어의 주소 필드가 가리키는 기억장치 위치로부터 실제 주소를 인출하여, 다시 $\mathrm{IR}$의 주소 필드에 적재한다.
- $\mathrm{RET}$은 $\mathrm{SBR}$에 저장된 원래의 제어 기억장치 주소를 $\mathrm{CAR}$에 적재하여 수행.



**4.3.3 실행 사이클 루틴**

**연산 코드들에 대한 사상의 결과**

| 명령어   | 연산 코드 | 루틴의 시작 주소     |
| -------- | --------- | -------------------- |
| NOP      | 0000      | 1000000 = $64_{10}$  |
| LOAD(I)  | 0001      | 1000100 = $68_{10}$  |
| STORE(I) | 0010      | 1001000 = $72_{10}$  |
| ADD      | 0011      | 1001100 = $76_{10}$  |
| SUB      | 0100      | 1010000 = $80_{10}$  |
| JUMP     | 0101      | 1010100 =  $84_{10}$ |

 

**실행 사이클 루틴들**
$$
\begin{align*}
&\mathrm{ORG\ 64} \\
\mathrm{NOP:\quad} &\mathrm{INCPC} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{FETCH} &&\mathrm{;PC\leftarrow PC+1} \\
\\


&\mathrm{ORG\ 68} \\
\mathrm{LORD:\quad} &\mathrm{NOP} &\mathrm{I} &&\mathrm{CALL} &&\mathrm{INDRT} &&\mathrm{;I=1이면\ 간접\ 사이클\ 루틴\ 호출} \\
&\mathrm{IRTAR} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MAR\leftarrow IR(addr)} \\
&\mathrm{READ} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MBR\leftarrow M[MAR]} \\
&\mathrm{BRTAC} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{FETCH} &&\mathrm{;AC\leftarrow MBR} \\
\end{align*}
$$

$$
\begin{align*}
&\mathrm{ORG\ 72} \\
\mathrm{STORE:\quad} &\mathrm{NOP} &\mathrm{I} &&\mathrm{CALL} &&\mathrm{INDRT} &&\mathrm{;I=1이면\ 간접\ 사이클\ 루틴\ 호출} \\
&\mathrm{IRTAR} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MAR\leftarrow IR(addr)} \\
&\mathrm{ACTBR} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MBR\leftarrow AC} \\
&\mathrm{WRITE} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{FETCH} &&\mathrm{;M[MAR]\leftarrow MBR} \\
\\
&\mathrm{ORG\ 76} \\
\mathrm{ADD:\quad} &\mathrm{IRTAR} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MAR\leftarrow IR(addr)} \\
&\mathrm{READ} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MBR\leftarrow M[MAR]} \\
&\mathrm{ADD} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{FETCH} &&\mathrm{;AC\leftarrow AC+MBR} \\
\\


&\mathrm{ORG\ 80} \\
\mathrm{SUB:\quad} &\mathrm{IRTAR} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MAR\leftarrow IR(addr)} \\
&\mathrm{READ} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{NEXT} &&\mathrm{;MBR\leftarrow M[MAR]} \\
&\mathrm{SUB} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{FETCH} &&\mathrm{;AC\leftarrow AC-MBR} \\
\\


&\mathrm{ORG\ 84} \\
\mathrm{JUMP:\quad} &\mathrm{IRTAR} &\mathrm{U} &&\mathrm{JMP} &&\mathrm{FETCH} &&\mathrm{;PC\leftarrow IR(addr)} \\
\end{align*}
$$



## 4.5 마이크로프로그래밍의 순서 제어

- **순서제어(sequencing)**: 다음에 실행할 마이크로명령어의 주소를 결정하는 것
- 순서제어를 하는 회로의 요소
  - CAR: 마이크로명령어의 제어 기억장치 주소를 가지고 있음. 초기값으로 인출 사이클 루틴의 첫번째 마이크로명령어의 주소를 가진다.
  - MUX2: 제어 기억장치의 CD 필드들을 받아, <u>네 개의 조건 필드 중 하나를 선택</u>([표 4-2])
  - MUX1: 주소 선택 회로의 선택(select) 신호를 받아, 네 개 중 하나를 선택(다음에 실행할 마이크로명령어의 주소 선택)



<img src=".\Images\4-4.PNG" alt="4-4" style="zoom:30%;" />



**[표 4-5] 주소 선택 회로의 입력 및 출력 신호들**

<img src=".\Images\4-6.PNG" alt="4-6" style="zoom:30%;" />



**주소 선택 방법**

- $\mathrm{BR=00\ (JUMP)}$ 혹은 $\mathrm{01(CALL)}$
  - $\mathrm{C=0}$ (조건이 만족되지 않았으면), $\mathrm{CAR}$ 내용 1증가, 다음에 위치한 마이크로명령어 선택
  - $\mathrm{C=1}$ (조건이 만족되었다면), $\mathrm{ADF}$의 내용을 $\mathrm{CAR}$로 적재하여, JUMP 혹은 호출(CALL)
    - 단, 호출 시 $\mathrm{CAR}$ 내용을 $\mathrm{SBR}$로 적재. 제어 신호 $L$에 의해 이루어진다.
- $\mathrm{BR=10\ (RET)}$ : $\mathrm{SBR}$ 내용을 $\mathrm{CAR}$로 적재하여 복귀
- $\mathrm{BR=11\ (MAP)}$ : 사상 결과를 $\mathrm{CAR}$를 적재하여 해당 실행 사이클 루틴으로 분기



**제어 신호의 생성**

- 제어 기억장치로부터 인출된 마이크로명령어의 연산 필드 비트들($\mu$-ops)은 제어 유니트의 외부로 출력되어 제어신호가 됨.



**마이크로명령어 제어의 종류**

- (1) 수직적 마이크로프로그래밍 : 해독기로 제어 비트를 제어 신호들로 확장시킴. (3비트 - 3X8 해독기- 8비트)
- (2) 수평적 마이크로프로그래밍 : 해독기가 필요하지 않음 (8비트)



**(1) 수직적 마이크로프로그래밍(vertical microprogramming)**

- (1) 마이크로명령어의 연산 핀들에 적은 수의 코드화된 비트(encoded bits)를 포함시킴.
- (2) 해독기(decoder)를 이용하여 비트들을 필요한 수 만큼의 제어 신호들로 확장함.
- (장점) 마이크로명령어의 길이(비트 수) 최소화(제어 기억장치 용량 감소)
- (단점) 해독 동작에 걸리는 만큼의 지연 시간 발생

<img src=".\Images\4-5.PNG" alt="4-5" style="zoom:30%;" />

**(2) 수평적 마이크로프로그래밍(horizontal microprogramming)**

- (1) 연산 필드의 각 비트와 제어 신호가 일대일대응
- (2) 필요한 제어 신호 수만큼의 비트들로 이루어진 연산필드 비트들이 마이크로 명령어에 포함
- (장점) 하드웨어가 간단하고 해독에 따른 지연 시간이 없음.
- (단점) 마이크로명령어의 비트 수가 길어짐(제어 기억장치 용량 증가)

