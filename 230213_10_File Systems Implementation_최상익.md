## 파일과 파일 시스템

- 파일
  
  - A named collection of related information
  
  - 일반적으로 비휘발성의 보조 기억 장치에 저장
  
  - 운영 체제는 다양한 저장 장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해 줌
  
  - operation
    
    - create, read, write, reposition(lseek), delete, open, close
    
    - reposition : 파일의 위치를 변경하는 연산자

- file attribute (속성) ( matadata)
  
  - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보
  
  - 파일 이름, 유형, 저장된 위치, 파일 사이즈
  
  - 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

- file system
  
  - 운영 체제에서 파일을 관리하는 부분
  
  - 파일 및 파일의 메타 데이터, 디렉토리 정보 등을 관리
  
  - 파일의 저장 방법 결정
  
  - 파일 보호 등

    

#### directory and logical disk

- 디렉토리
  
  - 파일의 메타 데이터 중 일부를 보관하고 있는 일종의 특별한 파일
  
  - 그 디렉토리에 속한 파일 이름 및 파일 속성
  
  - operation 
    
    - search for a file, create a file, delete a file
    - list a directory, rename a file, traverse the file system

- partition (local disk)
  
  - 하나의 물리적 디스크 안에 여러 파티션을 두는 것이 일반적
  
  - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
  
  - 물리적 디스크를 파티션으로 구성한 뒤 각각의 파티션에 파일 시스템을 깔거나 스와핑 등 다른 용도로 사용할 수 있음.

    

#### open()

- 디스크로부터 파일 c의 메타 데이터를 메모리로 가지고 옴

- 이를 위하여 directory path를 search
  
  - root directory를 open하고 그 안에서 파일 위치'a'의 위치 획득
  
  - 파일 'a'를 open한 후 read하여 그 안에서 파일 'b'의 위치 획득
  
  - 파일 'b'를 open한 후 read하여 그 안에서 파일 'c'의 위치 획득
  
  - 파일 'c'를 open

- 디렉토리 패스의 탐색에 너무 많은 시간 소요
  
  - open을 read/write와 별도로 두는 이유
  
  - 한번 open한 파일은 read/write 시 디렉토리 서치 불필요

- open file table
  
  - 현재 open  된 파일들의 메타데이터 보관소
  
  - 디스크의 메타데이터보다 몇 가지 정보가 추가
    
    - open한 프로세스의 수
    
    - file offset : 파일 어느 위치 접근 중인지 표시

- file descriptor : open file table에 대한 위치 정보

 cf) LRU, LFU와 같은 알고리즘을 사용한 버퍼 캐싱이 가능

버퍼 안에 데이터가 있든 없든, 시스템 콜을 호출하여 CPU의 제어권이 운영 체제로 넘어오기 때문

    

### filr protection

- 각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?

- Access Control 방법
  
  - Access Control matrix
  
  ![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-21-29-38-image.png)
  
  - Grouping
    
    - 전체 user를  owner, group, public의 세 그룹으로 구분
    
    - 각 파일에 대해 세 그룹의 접근 권한(rwx)을 3비트씩으로 표시
    
    - UNIX
  
  - password
    
    - 파일마다 패스워드를 두는 방법(디렉토리 파일에 두는 방법도 가능)
    
    - 모든 접근 권한에 대해 하나의 password
    
    - 접근 권한별 password : 암기 및 관리 문제 발생

    

#### file system의 mounting

- 다른 파티션에 있는 파일 시스템에 접근하려고 할 때

- 또다른 파티션에 있는 파일 시스템의 root 디렉토리에 접근 가능 

    

#### access methods

- 순차접근
  
  - 카세트 테이프 사용하는 방식 / a>b>c 순서대로 봐야함
  
  - 읽거나 쓰면 offset은 자동으로 증가

- 직접 접근
  
  - LP 판과 같이 접근 / a>c>b 등 마음대로 볼 수 있음
  
  - 파일을 구성하는 레코드를 임의의 순서로 접근 할 수 있음

    

## allocation of file data in disk

- contiguous

- linked

- indexed

    

#### contiguous(연속할당)

![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-21-36-59-image.png)

- 하나의 파일이 디스크 상에 연속해서 저장되는 방식, 각 블록들이 연속된 번호로 저장

- 단점
  
  - 외부조각 발생
  
  - file grow가 어려움
    
    - 파일 생성 시 얼마나 큰 hole를 배당할 것인가 (균일하지 않은 홀들 생김)
    
    - grow 가능 vs 낭비(내부 조각) 

- 장점
  
  - Fast I/O (빠른 입출력)
    
    - 한번의 seek/rotation으로 많은 바이트 transfer
    
    - realtime file 용으로, 또는 이미 run 중이던 process의 swapping 용
  
  - ditect access(직접접근) 가능

    

### linked (연결할당)

![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-21-43-28-image.png)

- 파일의 시작위치만 디렉토리가 가지고 있고 그 다음 위치들은 파일에 가보면 알 수 있다. 파일들이 연결

- 장점 : 외부조각 발생 X

- 단점
  
  - 직접 접근이 불가능 ( 첫 요소부터 차례대로 읽어야 하므로)
  
  - reliability : 한 섹터가 고장나 pointer가 유실되면 많은 부분을 잃음
  
  - pointer를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림

- 변형 : File-allocation table (FAT) 파일 시스템
  
  - 포인터를 별도의 위치에 보관하여 신뢰성 문제와 공간 효율성 문제를 해결한다.

    

### indexed

![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-21-47-43-image.png)

- 파일이 어디에 나눠져 있는지 인덱스를 적어두는인덱스 블록을 활용

- 장점 : 외부 조각 발생 X  / 직접 접근 가능

- 단점
  
  - 작은 파일의 경우 공간 낭비가 심하며, 실제로 많은 파일들이 작다
  
  - 매우 큰 파일의 경우 하나의 인덱스 블록으로 커버할 수 없다
    
    - 해결방안 : linked scheme(파일 위치를 담다가 또다른 인덱스 블록), 
      
                          multi-level index : 인덱스가 또 다른 인덱스 블록 가르킴

    

#### unix 파일 시스템 구조

- 유닉스 파일 시스템의 중요 개념
  - Boot block
    - 어떤 파일시스템에서도 맨 앞에 존재하는, 0번 블록
    
    - 부팅에 필요한 정보를 담고 있는 블록
  - Super block
    - 파일 시스템에 관한 총체적인 정보를 담고 있는 블록
    - 어디가 빈 블록이고 어디가 정보를 담고 있는 블록인지
  - Inode list
    - 파일 이름을 제외한 파일의 모든 메타 데이터를 따로 저장한다.
    - 파일 하나 당 Inode가 하나씩 할당이 되고, 해당 Inode는 그 파일의 메타 데이터를 갖고 있다.
    - 파일의 이름은 디렉토리가 직접 가지고 있다
    - 나머지 메타 데이터들은 inode에 저장 (unix의 핵심)
    - 인덱스 할당을 사용
    - direct blocks는 파일이 존재하는 인덱스를 저장하는 인덱스 블록이다. 작은 파일을 다름
    -  single indirect를 통해서 큰 파일 저장, 그보다 더 큰 파일은 double indirect, 더 큰 파일은 triple indirect 방식을 취한다.
  - Data block
    - 파일의 실제 내용을 보관하는 블록
    - 이 중 디렉토리 파일은 자신의 디렉토리에 속한 파일들의 이름과 Inode 번호를 가지고 있다.

    

#### FAT 파일 시스템

- windows 계열 시스템

- 메타데이터 일부를(위치정보) FAT에 저장, 나머지는 디렉토리가 가짐

- FAT이라는 별도의 테이블에 다음 블록의 위치를 할댕해서 관리

- 연결할당의 단점(직접 접근X, 신뢰성 공간 효율성 등)을 보완

   

### free space management

- Bit map or Bit vector
  
  - 0이면 빈 공간, 1이면 저장된 공간
  
  - 비트맵은 부가적인 공간을 필요로 함
  
  - 연속적인 n개의 free block를 찾는데 효과적

- Linked list
  
  - 모든 free 블록을 링크로 연결 (free list)
  - 연속적인 가용 공간을 찾는 것은 쉽지 않다.
  - 공간의 낭비가 없다.

- Grouping
  - Linked list  변형
  - 첫 번째 free 블록이 n 개의 pointer을 가짐
    - n-1 pointer는 free data block를 가리킴
    - 마지막 pointer가 가리키는 block는 또 다시 n pointer를 가짐
- Counting
  - 프로그램들이 종종 여러 개의 연속적인 블록을 할당하고 반납한다는 성질에 착안

    

### directory implementation

- Linear list
  
  - file name, matedate의 리스트
  
  - 구현이 간단
  
  - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요(O(N))

- Hash table
  
  - linear list + hashing
  
  - hash table은 file name을 이 파일의 선형 리스트의 위치로 바꾸어줌
  
  - search 타임을 없앰
  
  - collision 발생 가능

- 파일의 메타 데이터의 보관 위치
  
  - 디렉토리 내에 직접 보관
  
  - 디렉토리에는 포인터를 두고 다른 곳에 보관
    - Inode, FAT 등

- Long file name의 지원
  
  - file name, matedate의 리스트에서 각 엔트리는 일반적으로 고정 크기
  - file name이 고정 크기의 엔트리 길이보다 길어지는 경우 엔트리의 마지막 부분에 이름의 뒷 부분이 위치한 곳의 포인터를 두는 방법
  - file 이름의 나머지 부분은 동일한 directory file의 일부에 존재

    

#### VFS and NFS

- virtual file system : 서로 다른 다양한 파일 시스템에 대해 동일한 시스템 콜 인터페이스를 통해 접근할 수 있게 해주는 os의 layer

- network file system
  
  - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
  
  - NFS는 분산 환경에서의 대표적인 파일 공유 방법

    

### page cache and buffer cache

- page cache
  - 가상 메모리의 페이징 시스템에서 사용하는 페이지 프레임을 캐싱의 관점에서 설명하는 용어
  - Memory-Mapped I/O를 쓰는 경우 파일의 I/O에서도 페이지 캐시를 사용한다.
- Memory-Mapped I/O
  - 파일의 일부를 가상 메모리에 매핑한다.
  - 매핑한 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함
  - 장점 : 시스템콜 없이 I/O 작업
  - 페이지 캐시를 복사할 필요 없다
  - 단점 : 일관성 문제 발생
- buffer cache
  - 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 버퍼 캐시를 사용
  - 파일 사용의 locality활용
    - 한 번 읽어 온 블록에 대한 후속 요청 시, 버퍼 캐시에서 즉시 전달
  - 모든 프로세스가 공용으로 사용
  - replacement 알고리즘 필요 (LRU, LFU 등)
- unified buffer cache
  - 최근의 OS에서는 기존의 버퍼 캐시가 페이지 캐시에 통합됨.

![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-22-18-38-image.png)

    

![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-22-21-46-image.png)

      

### 프로그램의 실행

![](230213_10_File%20Systems%20Implementation_최상익_assets/2023-02-13-22-25-08-image.png)

- 프로그램이 실행되면 프로세스가 되어 프로세스 주소 공간이 생성




