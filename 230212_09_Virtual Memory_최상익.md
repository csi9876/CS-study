#### demand paging

- 실제로 필요할 때 메모리에 올리는 것
  
  - I/O양의 감소
  
  - Memory 사용량 감소
  
  - 빠른 읍답 시간
  
  - 더 많은 사용자 수용

- 유효-무효 비트 사용
  - invalid(무효)의 의미
    - 사용되지 않는 주소 영역인 경우
    - 페이지가 물리적 메모리에 없는 경우
  - 처음에는 모든 page entry가 invaild로 초기화
  - 주소 변환을 하려는데 무효 비트로 set되어 있으면 Page fault

        

#### page falut

-  무효 페이지를 접근하면 MMU가 trap을 발생한다. (page fault trap)

- 커널 모드로 들어가서, page fault handler가 invoke 됨

- 다음과 같은 순서로 page fault 처리
  
  - invalid reference(bad address, protect violation) -> aboort process (프로세스 종료)
  
  - get an empty frame (없으면 뺏어온다)
  
  - 해당 페이지를 disk.에서 메모리로 읽어온다
    
    - disk i/o가 끝나기까지 이 프로세스는 cpu를 preempt 당함
    
    - dist read가 끝나면 page tables entry 기록, valid 상태
    
    - ready queue에 프로세스를 insert > dispatch later
  
  - 이 프로세스가 cpu를 잡고 다시 running
  
  - 아까 중단되었던 instruction을 재개

    

#### 성능

- page fault의 발생 빈도가 성능에 가장 큰 영향

- page fault rate : 0이면 한 번도 일어나지 않음 / 1이면 모든 경우 발생

    

### page replacerment

- 어떤 frame을 빼앗아올지 결정해야함

- 곧바로 사용되지 않을 페이지를 쫓아내는 것이 좋음

- 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음

- repalcement algorithm
  
  - page fault rate를 최소화 하는 것이 목표
  
  - 알고리즘 평가
    
    - 주어진 page refernce string에 대해 page fault를 얼마나 내느지
  
  - refernce string의 예 : 1,2,3,4,1,2,5,1,2,3,4,5

    

#### Optimal Algorithm

- MIN(OPT) : 가장 먼 미래에 참조되는 page를 repalce

- 미래의 참조를 어떻게 아는가 ? offline algorithm

- 다른 알고리즘의 성능에 대한 upper bound를 제공

    

#### FIFO 알고리즘

- 선입선출 : 가장 먼저 올라온 페이지를 내쫓음

- 메모리를 증가하였음에도 페이지  fault가 오히려 늘어나는 현상

- FIFO anomaly : more frame -> less page faults

    

#### LRU

- 가장 오래 전에 참조된 것을 내쫓음

    

#### LFU 알고리즘

- 참조 횟수가 가장 적은 페이지를 내쫓음

- 최저 참조 페이지가 여럿 있는 경우
  
  - 여러 page 중 의의로 선정
  
  - 가장 오래 전에 참조된 페이지를 지우게 구현 가능

- 장단점
  
  - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
  
  - 참조 시점의 최근성을 반영하지 못함
  
  - LRU보다 구현이 복잡함

    

### LRU와 LFU의 구현

- LRU는 졍렬해서 가장 위의 페이지를 쫓아내면 된다

- 시간복잡도 O(1)

- LFU는 페이지를 교체할 때는 가장 위에 있는 페이지를 교체

- 특정 페이지가 참조되었을 때는 자기 자신보다 아래 있는 노드와 비교

- GEAP 자료 구조, 시간복잡도 O(logN)

![](230212_08_Virtual%20Memory_최상익_assets/2023-02-12-20-33-31-image.png)

    

#### 다양한 캐슁 환경

- 캐슁 기법
  
  - 한정된 빠른 공간(=캐시)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐시로부터 직접 서비스하는 방식
  
  - 페이징 시스템 외에도 cache memory, buffer caching, Web caching 등 다양한 분야에서 사용한다.

- 캐시 운영의 시간 제약
  
  - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 거리는 경우 실제 시스템에서 사용할 수 없음
  - buffer caching이나 Web caching의 경우 : O(1)에서 O(log N)까지 허용
  - 페이징 시스템의 경우
    - page fault 경우에만 OS가 관여한다.
    - 페이지가 이미 메모리에 존재하는 경우 참조 시각 등의 정보를 OS가 알 수 없음
    - O(1)인 LRU의 List 조작조차 불가능

    

### 페이징 시스템에서 LRU, LFU가 가능한가

-  주소 변환은 모두 하드웨어가 맡고, OS는 관여X

- 프로세스가 요청한 페이지가 메모리에 이미 올라와 있는 경우에는 CPU가 OS로 넘어가지 않음

- 페이지 부재가 발생할 때만 페이지에 접근하는 정보를 알 수 있으므로 LRU, LFU 알고리즘은 페이징 시스템에서 사용불가 

- buffer caching이나 Web caching 가능

    

#### clock 알고리즘

- LRU의 근사 알고리즘

- second chance , NUR, NRU

- reference bit을 사용해서 교체 대상 페이지  선정

- reference bit가 0인 것을 찾을 떄까지 포인터를 하나씩 앞으로 이동

- 포인터 이동 중 reference bit 1은 모두 0으로 바꿈

- reference bit가 0인 것을 찾으면 그 페이지를 교체

- 한바퀴 되돌아와서도 0이면 그 떄는 repalce 당함

- 자주 사용되는 페이지라면 second chance가 올때 1

- 개선
  
  - reference bit와 modified bit 을 함께 사용
  
  - reference bit = 1 : 최근에 참조된 페이지
  
  - modified bit = 1 : 최근에 변경된 페이지

    

#### 페이지 프레임의 할당

- allocation 문제 : 각 프로세세에 얼만큼의 페이지 프레임을 할당할 것인가

- 필요성
  
  - 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조
  
  - 명령어 수행을 위해 최소한 할당되어야 하는 프레임의 수가 있음
  
  - loop를 구성하는 페이지들은 한꺼번에 프로세스에 할당되는 것이 유리
  
  - 최소한의 할당이 없으면 매 반복마다 페이지 fault가 발생

- equal 할당 : 모든 프로세스에 똑같은 갯수 할당

- proportional : 프로세스 크기에 비례하여 할당

- priority : 프로세스의 우선순위에 따라 다르게 할당

    

#### global 교체

- 교체 시 다른 프로세스에 할당된 프레임을 빼앗아 올 수 있다

- 프로세스별 할당량을 조절하는 또 다른 방법

- FIFO, LRU, LFU 등을 글로벌로 시행할 시 해당

- working set, PFF 알고리즘 사용

### Local 교체

- 자신에게 할당된 프레임 내에서만 교체

- FIFO, LRU, LFU 등 알고리즘을 프로세스 별로 운영시

    

#### Thrashing

- 원활한 수행에 필요한 최소한 페이지 프레임 수를 할당 받지 못한 경우

- page fault rate가 높아짐

- cpu utilization이 낮아짐

- Os는 MPD를 높여야 한다고 판단

- 또 다른 프로세스가 시스템에 추가됨

- 프로세스 당 할당된 프레임의 수가 더욱 감소

- 프로세스는 페이지의 swap in / swap out으로 매우 바쁨

- 대부분의 시간에 cpu는 한가함

- low thrroughput

    

### Working-Set 알고리즘

- locality of refernce
  
  - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조
  
  - 집중적으로 참조되는 해당 페이지들의 집합을 locality set이라 함

- 워킹셋 모델
  
  - locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 페이지의 집합을 워킹셋이라고 정의함
  - 워킹셋 모델에서는 프로세스의 워킹셋 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 프레임들을 반납 한 후 swap out
  - thrashing을 방지
  - 멀티프로그래밍 degree를 결정

- 워킹셋의 결정
  - 워킹셋 window를 통해 알아냄

![](230212_08_Virtual%20Memory_최상익_assets/2023-02-12-21-01-07-image.png)

    

#### pff 알고리즘

- PAGE FAULT RETE의 상한선과  하한선을 둔다
  
  - 상한선을 넘으면 프레임을 더 할당
  
  - 하한선 이하이면 할당 프레임 수를 줄인다

- 빈 프레임이 없으면 일부 프로세스를 swap out

    

#### 페이지 사이즈의 결정

- 페이지 사이즈를 감소시키면
  
  - 페이지 수 증가
  
  - 페이지 테이블 크기 증가
  
  - 내부 단편화 감소
  
  - Disk transfer의 효율성 감소
  
  - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
    
    - locality의 활용 측면에서는 좋지 않음

- trend : larger page size
