### deadlock (교착상태)

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

- 자원
  
  - 하드웨어, 소프트웨어 등을 포함하는 개념
  
  - I/O device, cpu cycle, memory space, semaphore
  
  - 프로세스가 자원을 사용하는 절차(request요청, allocate획득, use사용, release반납)



#### deadlock 발생의 4가지 조건

- mutual exclusion (상호 배제) : 매 순간 하나의 프로세스만이 자원을 사용할 수 있음

- no preemption(비선점) : 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

- hold and wait(보유대기) : 자원을 가진 프로세스가  다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음

- Circular wair (순환대기) : 자원을 기다리는 프로세스간에 사이클이 형성되어야 함

    

##### 자원할당 그래프

- ![](77_assets/2023-02-07-20-38-35-image.png)

- 그래프에 cycle이 없으면 deadlock이 아니다

- 그래프에 cycle이 있으면 자원 당 instance가 하나씩 밖에 없으면 deadlock

- 자원이 여러 개 있으면 데드락일 수도 있고 아닐 수도 있다.

    

    

### deadlock의 처리방법

- deadlock prevention
  
  - 자원 할당 시 deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

- deadlock avoidance
  
  - 자원 요쳉에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
  
  - 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당

- deadlock detection and recovery
  
  - deadlock발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover

- deadlock ignorance
  
  - deadlock을 시스템이 책임지지 않음
  
  - 대부분의 OS가 채택 > 사람이 처리

    

#### deadlock prevention

- mutual exclusion (상호 배제) : 공유해서는 안되는 자원의 경우 반드시 성립해야함

- hold and wait(보유대기)
  
  - 프로세스가 자원으 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다
  
  - 1.프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법
  
  - 2.자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청

- no preemption(비선점)
  
  - 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
  
  - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다
  
  - state를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용(cpu, 메모리)

- Circular wair (순환대기) : 자원을 기다리는 프로세스간에 사이클이 형성되어야 함
  
  - 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
  
  - 순서가 3인 자원r을 보유중인 프로세스가 순서가 1인 자원ri를 할당받기 위해서는 우선 r을 release해야 한다
  
  -> utilization 저하, throughput 감소, starvation 문제

    

### deadlock avoidance

- 자원요청에 대한 부가정보를 이요해서 자원 할당이 deadlock으로부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당

- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법임

- safe state : 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태

- safe sequence
  
  - 프로세스의 sequence가 safe하려면 pi의 자원요청이 가용자원 + 모든 pj의 보유 자원에 의해 충족되어야만 함
  
  - 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
  
  - pi의 자원요청이 즉시 충족될 수 없으면 모든 pj가 종료될 때까지 기다린다
  
  - pi-1이 종료되면 pi의 자원요청을 만족시켜 수행한다

- 시스템이 safe state에 있으면 no deadlock

- 시스템이 unsafe state에 있으면 deadlock 가능성

- avoidance는 시스템이 unsafe에 들어가지 않는 것을 보장

- 두 가지 알고리즘

    

##### resource allocation graph algorithm (single instance)

- claim edge Pi -> Rj
  
  - 프로세스 Pi가 자원 Rj를 미래에 요청할 수 있음을 뜻함 (점선으로 표시)
  
  - 프로세스가 해당 자원 요청 시  request edge로 바뀜(실선)
  
  - Rj가 release되면 assignment edge는 다시 claim edge로 바뀐다

- request edge의 assignment edge 변경시 cycle이 생기지 않는 경우에만 요청 자원을 할당한다

- cycle 생성 여부 조사시 프로세스의 수가 n일 때 O(N²) 시간이 걸린다

    

    

##### banker's algorithm  (multiple instances)

![](77_assets/2023-02-07-21-25-17-image.png)

- 현재 할당량, 최대 사용량, 필요량 계산

- 프로세스의 요청이 가용자원 이하여도, 프로세스의 need가 가용자원 초과이면 거부

- 



    

#### deadlock detection

- resource type 당 single instance인 경우
  
  - 자원할당 그래프에서의 cycle이 곧 deadlock을 의미

- resource type 당 multiple instance인 경우
  
  - banker's algorithm과 유사한 방법 활용

- wair for graph 알고리즘
  
  - resource type 당 single instance인 경우
  
  - wiat for graph
    
    - 자원할당 그래프의 변형
    
    - 프로세스만으로 node 구성
    
    - pj가 가지고 있는 자원을 pk가 기다리는 경우 pk -> pj
  
  - algorithm
    
    - wait for 에 사이클이 존재하는지를 주기적으로 조사
    
    - O(n²)
  
  ![](77_assets/2023-02-07-21-38-15-image.png)
  
  - resource type 당 single instance인 경우
  
  ![](77_assets/2023-02-07-21-43-46-image.png)

- recovery
  
  - process termination
    
    - deadlock에 연루된 모든 프로세스를 한번에 종료하는 것
  
  - resource preemption
    
    - 비용을 최소화할 희생자 선정, 프로세스를 하나씩 종료
    
    - safe state로 rollback하여 프로세스를 restart
    
    - starvation 문제
      
      - 동일한 프로세스가 계속해서 희생자 선정되는 경우
      
      - cost factor에 rollback 횟수도 같이 고려

    

    

#### deadlock ignorance

- deadlock이 드물게 발생하므로 예방 자체가 더 큰 overhead일 수 있음

- 사람이 느낀 후 직접 process를 죽이는 방법










