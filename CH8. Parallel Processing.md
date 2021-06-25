# CH8. Parallel Processing

## 8.1 병렬처리의 개념 및 필요성

- **병렬처리(parallel processing)**: 다수의 프로세서들이 한 개 이상의 프로그램의 분할된 부분을 분담하여 동시에 처리하는 기술
- 병렬처리를 위한 선결 조건
  - (1) 프로세서들이 하나의 시스템을 구성할 수 있도록 작고 저렴하며, 고속인 프로세서들을 사용할 수 있어야 함: 반도체 기술의 발전과 VLSI 직접도 향상으로 가능해짐
  - (2) 한 프로그램을 여러 개의 작은 부분들로 분할하는 것이 가능해야 하며, 분할된 부분을 병렬로 처리한 결과과 순차적으로 처리한 경우와 동일한 결과를 얻ㅇ르 수 있어야 함
- (2)를 만족하기 위해 해결해야 할 문제
  - (1) **문제 분할(problem partition)**: 병렬 처리를 위해 프로그램을 여러 개로 나누는 것
  - (2) **프로세서간 통신(Inter-processor communication)**: 프로세서 간 데이터 교환을 위한 매커니즘

- 필요한 관련 기술들
  - (1) 병렬 프로그램 언어와 컴파일러 개발
  - (2) 상호 배타 메커니즘(mutual exclusion mechanism) 개발
  - (3) 공유자원들에 대한 경합(contention)을 줄이고 이용률을 극대화하는 운영체제의 개발



## 8.3 병렬 컴퓨터의 분류

### 8.3.1 Flynn의 분류

- Flynn의 분류(Flynn's classification): 프로세서들이 처리하는 명령어와 데이터의 스트림의 수에 따라 분류
  - **명령어 스트림(instruction stream)**: 프로세서에 의해 실행되기 위해 순서대로 나열된 명령어 코드들의 집합
  - **데이터 스트림(data stream)**: 해당 명령어들을 실행하는 데 필요한 순서대로 나열된 데이터들의 집합
  - SISD: 단일 명령어 스트림 - 단일 데이터 스트림
  - SIMD: 단일 명령어 스트림 - 복수 데이터 스트림 
  - MISD: 복수 명령어 스트림 - 단일 데이터 스트림
  - MIMD: 복수 명령어 스트림 - 복수 데이터 스트림



#### 8.3.1.1 SISD

<img src=".\Images\8-1.PNG" alt="8-1" style="zoom:50%;" />

- 한번에 한 개씩의 명령어와 데이터를 순서대로 처리하는 단일프로세서 시스템
- 파이프라이닝(pipelining), 슈퍼스칼라(superscalar)구조를 이용



#### 8.3.1.2 SIMD

<img src=".\Images\8-2.PNG" alt="8-2" style="zoom:50%;" />

- 배열 프로세서(array processor)
- 여러 개의 프로세싱 유니트(PU)들로 구성되고, PU들의 동작은 모두 하나의 제어 유니트에 의해 통제
- 모든 PU들은 하나의 명령어 스트림을 실행
- 데이터 스트림은 여러 개를 동시에 처리



#### 8.3.1.3 MISD

<img src=".\Images\8-3.PNG" alt="8-3" style="zoom:50%;" />

- N개의 프로세서들이 서로 다른 명령어들을 실행하지만, 처리하는 데이터 스트림은 한 개 -> 비현실적이므로 실제 구현된 경우는 없음



#### 8.3.1.4 MIMD

<img src=".\Images\8-4.PNG" alt="8-4" style="zoom:50%;" />

- N개의 프로세서들이 서로 다른 명령어들과 데이터들을 처리
- 프로세서들간의 상호작용 정도에 따라 두 가지로 분류
  - 밀결합 시스템(tightly-coupled system)
    - 공유-기억장치 구조(shared-memory architecture)
    - 다중프로세서 시스템(multiprocessor system)
  - 소결합 시스템(loosely-coupled system)
    - 지역 기억장치(local memory)를 가진 독립적인 컴퓨터 모듈로 구성
    - 프로세서간 통신은 메시지 전송(message-passing) 방식 이용
    - 다중컴퓨터 시스템(multiple-computer system



### 8.3.2 기억장치 액세스 모델에 따른 분류

- (1) 균일 기억장치 액세스(Uniform Memory Access: UMA) 모델
- (2) 불균일 기억장치 액세스(Non-uniform Memory Access:  NUMA) 모델
- (3) 무-원격 기억장치 액세스(No-Remote Memory Access: NORMA) 모



#### 8.3.2.1 UMA 모델

<img src=".\Images\8-5.PNG" alt="8-5" style="zoom:50%;" />

- 모든 프로세서들이 상호연결망에 의해 접속된 주기억장치를 공유한다.
- 프로세서들은 주기억장치의 어느 영역이든 액세스할 수 있으며,  그에 걸리는 시간이 동일하다.
- (장점) 하드웨어가 간단하고, 프로그래밍이 용이하다.
- (단점) 공유 자원에 대한 경합이 높아 시스템 크기에 한계가 있다.



#### 8.3.2.2 NUMA 모델

<img src=".\Images\8-6.PNG" alt="8-6" style="zoom:50%;" />

- NUMA 모델의 한계를 극복, 더 큰 규모의 시스템을 구성하기 위함.
- 다수의 UMA 모델이 상호 연결망에 의해 접속
- 메모리: 분산 공유-기억장치(distributed shared-memory) 구조
- 기억장치 액세스 시간은 기억장치의 위치에 따라 분류
  - (1) 지역 기억장치 액세스(local memory access)
  - (2) 전역 기억장치 액세스(global memory access)
  - (3) 원격 기억장치 액세스(remote memory access)



#### 8.3.2.3 NORMA 모델

<img src=".\Images\8-7.PNG" alt="8-7" style="zoom:50%;" />

- 프로세서가 원격 기억장치(remote memory)에 직접 액세스할 수 없음.
  - (이전 모델들과 달리) **메시지-전송(message-passing)**을 지원하는 상호연결망으로 접속
- 분산-기억장치 시스템(distributed-memory)으로도 부름
- 주요 상호연결망: 매시(mesh), 하이퍼큐브(hypercube),  토러스(torus) 등



## 8.4 다중프로세서시스템 구조

- MIMD 조직으로서, 여러 개의 프로세서들이 비동기적으로 프로그램을 실행하는 시스템
- 다중 프로세서와 다중 컴퓨터에서 사용되는 상호 연결망에 따른 분류
  - 정적 상호 연결망: 분산-기억장치 시스템 구조에서 사용
  - 동적 상호 연결망: 공유-기억장치 시스템 구조에서 사용



### 8.4.2 정적 상호 연결망

#### 8.4.2.1 선형 배열(liner array) 구조

<img src=".\Images\8-8.PNG" alt="8-8" style="zoom:50%;" />

- 네트워크 지름이 N-1로서, 다른 구조들에 비하여 가장 길다.
- 버스 구조보다 동시성이 더 높다
- 통신 시간이 노드들 간의 거리에 따라 서로 다르며, 노드 의 수가 많아지면(N이 커지면) 통신 시간이 매우 길어진다



#### 8.4.2.2 링 구조

<img src=".\Images\8-9.PNG" alt="8-9" style="zoom:50%;" />

- 네트워크 지름: 각 링크가
  - 양방향성(bi-directional)이면 $\lfloor N/2\rfloor$
  - 단방향성(uni-directional)이면 $N-1$
- 변형 구조: 코달 원형(chordal ring)
  - 링크의 수가 증가될수록 네트워크 지름은 감소
  - $d$(degree) : 각 노드가 가지는 링크의 수



#### 8.4.2.3 트리 구조

<img src=".\Images\8-10.PNG" alt="8-10" style="zoom:30%;" />

- level(층)의 수를 $k$라 하면 $N=(2^k-1)$개의 노드들이 접속
- 네트워크 지름 = $2(k-1)$
- 시스템 요소의 수가 증가함에 따라 성능이 선형적으로 향상



#### 펫 트리(fat tree) 구조

<img src=".\Images\8-11.PNG" alt="8-11" style="zoom:30%;" />

- 상위 층으로 올라갈수록 노드간의 통신 채널 수 증가
- 채널 병목 해결: 트리 구조에서 상위 층으로 올라갈수록 통신 채널(통신량)이 많아짐
- Thinking Machine사의 CM-5시스템에서 실제 사용



#### 8.4.2.4 매시 구조

<img src=".\Images\8-12.PNG" alt="8-12" style="zoom:40%;" />

- Illiac IV, MPP, DAP, CM-2 및 Intel Paragon에서 사용



#### 토러스 네트워크

<img src=".\Images\8-13.PNG" alt="8-13" style="zoom:40%;" />

- 원형 구조와 매쉬 구조가 혼합된 구조로 확장이 용이
- $n×n$ 토러스의 경우에 네트워크 지름은 $2\lfloor N/2\rfloor$



#### 8.4.2.5 하이퍼큐브 네트워크

- 상호연결 함수(interconnection function)

<img src=".\Images\8-14.PNG" alt="8-14" style="zoom:40%;" />



### 8.4.3 동적 상호 연결망

- 상호연결 구조
  - 버스(Bus)
    - 공유-버스(shared-bus)
    - 다중-버스(multiple-bus)
  - 크로스바 스위치(Crossbar Switch)
  - 다단계 상호연결망(Multistage Interconnection Network:  MIN)



#### 8.4.3.1 버스

<img src=".\Images\8-15.PNG" alt="8-15" style="zoom:40%;" />

- 공유-버스 구조
  - 하드웨어가 매우 간단
  - 버스 경합으로 인한 지연 시간 증가
    - 버스의 전송 속도를 높이거나 캐시를 사용-> 성능 저하를 보완

<img src=".\Images\8-16.PNG" alt="8-16" style="zoom:40%;" />

- 다중-버스 구조
  - 버스 경합을 줄이기 위하여 버스의 수를 증가
  - 계층버스 구조(hierarchical bus structure) : 용도가 다른 여러 계층의 버스들을 사용



#### 8.4.3.2 크로스바 스위치

<img src=".\Images\8-17.PNG" alt="8-17" style="zoom:40%;" />

- 프로세서들과 기억장치들 사이의 완전 연결성(full connectivity)
- 비용이 많이 들고 하드웨어가 복잡($O(N^2)$)



#### 8.4.3.3 다단계 상호연결망

- 크로스바 스위치와 버스 중간 정도의 하드웨어 복잡성을 가진 상호연결망

- 라우팅 알고리즘: 스위치를 어떻게 연결할 것인가, 원하는 곳에서 목적지까지 어떻게 보낼 것인가 결정하는 알고리즘

- 예) 오메가 네트워크

  <img src=".\Images\8-18.PNG" alt="8-18" style="zoom:40%;" />

  - 입력단과 출력단이 각각 $N$개인 경우

    - 필요한 단계(stage)의 수 : $s = log_2N$
    - 각 단계의 스위칭 소자(switching element)들의 수: $m=N/2$

  - 스위칭 소자의 접속 방식(connection mode)

    - 직진(straight): 같은 위치의 입출력 단자들이 서로 접속

    - 교차(swap): 서로 다른 위치의 입출력 단자들이 접속
    - 하위 방송(lower broadcast): 하단의 입력 단자가 모든 출력단자들로 접속
    - 상위 방송(upper broadcast): 상단의 입력 단자가 모든 출력단자들로 접속

