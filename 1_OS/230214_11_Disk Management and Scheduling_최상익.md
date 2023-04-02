## disk Scheduling

- Access time 의 구성
  
  - seek time
    
    - 헤드를 해당 실린더로 움직이는데 걸리는 시간
    
    - 디스크를 접근하는 시간에서 가장 큰 시간 구성 요소
  
  - rotational latency
    
    - 원판이 회전해서 헤더에 도착하는 데 걸리는 회전지연시간
  
  - Transfer time
    
    - 실제 데이터의 전송 시간

- disk bandwidth
  
  - 단위 시간 당 전송된 바이트의 수
  
  - 디스크의 성능

- disk Scheduling
  
  - seek time 을 최소화하는 것이 목표
  
  - seek time = seek distance

    

### disk structure

- logical block
  
  - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
  
  - 주소를 가진 1차원 배열처럼 취급
  
  - 정보를 전송하는 최소 단위

- sector
  
  - logical block이 물리적인 디스크에 매핑된 위치
  
  - sector 0 은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터

    

## disk management

- physical formatting (Low-level formatting)
  
  - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
  
  - 각 섹터는 header + 실제 data(보통 512bytes) + trailer 로 구성
  
  - header 와 trailer 는 sector number, ECC등의 정보가 저장되며 controller 가 직접 접근 및 운영

- Partitioning
  
  - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
  
  - os는 이것을 독립적 disk 로 취급 (logical disk)

- logical formatting
  
  - 파일 시스템을 만드는 것
  
  - FAT,inode, free space 등의 구조 포함

- booting
  
  - rom 에 있는 “small bootstrap loader”의 실행
  
  - sector 0 (boot block)을 load 하여 실행 - 하드디스크에서 0번 섹터에 있는 내용을 메모리에 올리고 실행
  
  - sector 0 은 “full Bootstrap loader program”
  
  - os를 디스크에서 load 하여 실행 - 파일시스템에서 운영체제 커널의 위치를 찾아서 메모리에 올려서 실행

    

## disk scheduling algorithm

### FCFS

- 들어온 순서대로 처리

![](230214_11_Disk%20Management%20and%20Scheduling_최상익_assets/2023-02-14-20-10-34-image.png)

### SSTF

- 요청 중 헤드에서 가장 가까운 요청부터 처리

- 헤드의 이동거리 줄어든다

- starvation 발생(멀리 있는 요청들은 계속 기다림)

![](230214_11_Disk%20Management%20and%20Scheduling_최상익_assets/2023-02-14-20-12-20-image.png)

### SCAN (엘리베이터)

- 가장 간단하면서도 획기적인 방법

- 한쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 들어와 있는 요청을 처리

- 헤드의 이동거리도 짧음, 기아현상도 없음

- 실린더의 위치에 따라 대기시간이 천차만별

### C-SCAN

- 한쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 들어와 있는 요청을 처리

- 끝에 도달하면 출발점으로 다시 이동

- SCAN보다 균일한 대기시간

    

### N-SCAN

- 한 방향으로 움직이기 시작하면 그 이후에 도착한 요청은 돌아올 때 처리

### look, c_look

- 헤드가 진행 중이다가 그 방향에 요청이 없으면 반대로 이동

    

### 알고리즘 결정

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음

- File 의 할당 방법에 따라 디스크 요청이 영향을 받음

- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS 와 별도의 모듈로 작성되는 것이 바람직하다.

    

### Swap-Space Management

- Disk 를 사용하는 두 가지 이유
  
  - memory 의 volatile(휘발성) > 파일 시스템
  
  - 프로그램 실행을 위한 memory 공간 부족 > 물리적인 메모리 한계

- Swap-space
  
  - virtual memory system 에서는 디스크를 memory 의 연장 공간으로 사용
  
  - 파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
    
    - 공간 효율성 보다는 속도 효율성이 우선
    
    - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
    
    -  block의 크기 및 저장 방식이 일반 파일 시스템과 다름

    

### RAID

- 여러 개의 디스크를 묶어서 사용

- 목적
  
  - 디스크 처리 속도 향상
    
    - 여러 디스크에 block 의 내용을 분산 저장
    - 병렬적으로 읽어 옴 (interleaving, striping) 
  
  - 신뢰성 향상
    
    - 동일 정보를 여러 디스크에 중복 저장
    
    - 하나의 디스크가 고장시 다른 디스크에서 읽어옴
    
    - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장해 공간의 효율성을 높임
    
    - parity : 데이터가 유실 또는 손상되었는지 여부를 점검하는 기술
