### Pre

- UNIX Architecture
- Shell
- File
- Program vs Process
- Thread
- Concurrency Control
- System Call

### The File

##### File

- 데이터를 저장하는 공간
- Physically, Seq of Bytes
- OS에의해서 형식이 정해지지 않음
  - 사용자가 형식을 지정
- 각 바이트들은 주소로 접근 가능
- in Linux, Everything is File(외부 기기도 파일로 인식)

##### File System

- 파일을 관리하는 시스템
- 용도, 환경에 따라서 사용하는 시스템이 달라짐
- 시퀀스로 저장된 파일을 사용자가 친화적인 트리 구조로 표현

##### File Descriptor

- 열려 있는 파일의 고유번호
- open()이나 creat()을 호출할 때 러턴값으로 받음
- 커널이 직접 할당
- 해당하는 파일에 접근할 때 사용

##### open

```c++
int open(const char *path, int oflag, mode_t mode);
```

- return File Descriptor or -1(Fail to Open)
- oflag: O_RDONLY, O_WRONLY, O_RDWR, O_APPEND, O_CREAT
- mode: O_CREAT에서만 사용, 파일의 권한 설정

##### creat

```c++
int creat(const char *path, mode_t mode);
```

- open의 O_CREAT를 준 것과 동일
- 파일이 이미 있으면 O_TRUNC(덮어쓰기)처럼 작동

##### close

```c++
int close(int fd);
```

- return 0(success) or -1(fail)
- 요즘엔 자동으로 프로그램 끝날 때 닫아줌

##### read

```c++
ssize_t read(int fd, void *buffer, size_t n);
```

- ssize_t => 헤더에 미리 지정된 signed size
- n바이트를 fd에서 읽어서 버퍼에 저장
- return 읽은 바이트, 0이면 EOF인데 읽기 시도, -1이면 Fail
- f_position: 커서의 현재 위치, 파일 시스템에서 관리

##### write

```c++
ssize_t write(int fd, void *buffer, size_t n);
```

- 버퍼에서 n바이트를 fd에 작성
- return 쓴 바이트, -1이면 Fail
- O_APPEND로 열린 파일이 아닌 경우에는 항상 0부터 쓰기 시작

##### read, write Efficiency

- buffer의 크기가 늘어날수록 속도가 빨라짐
- 디스크의 페이지 크기인 4K 이상에서는 차이 없음
- 결론, 페이지 크기(4K)의 배수로 설정하게 되면 속도가 가장 빠르다

- 특히 write에서는 여러개의 요청을 모았다가 실행하기 때문에 특히 심하게 나타남

##### lseek

```c++
off_t lseek(int fd, off_t offset, int start_flag);
```

- f_position을 offset에 위치
- return 옯긴 크기
- start_flag: SEEK_SET(시작), SEEK_CUR, SEEK_END(끝)
- filesize = lseek(fd, (off_t)0, SEEK_END);
  - 파일 크기를 찾는 Trick

### File Share

- Concurrency Control을 하지 않으면 일관성이 없어짐
- fd로 파일을 관리하긴 하지만 같은 파일을 가리키는 경우, 커널의 v-node는 동일함

##### dup, dup2

```c++
int dup(int fd);
int dup2(int fd1, int fd2);
```

- dup: 같은 파일을 하나 더 open
- dup2: fd1을 fd2에 dup
- 파일 자체를 복사하는 것이 아니라 같은 파일 하나를 더 여는 것

##### fcntl

```c++
int fcntl(int fd, int cmd, ...);
```

- File Control
- cmd
  - F_DUPFD (dup)
  - F_GETFD/F_SETFD (fd)
  - F_GETFL/F_SETFL (flag)
  - F_GETOWN/F_SETOWN
