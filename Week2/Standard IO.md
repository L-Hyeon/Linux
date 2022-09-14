### Standard I/O

- fd 0 = standard input
- fd 1 = standard output
  -> shell에서 input output을 받거나 보여주는 방법
- pg < inputfile
  - pg의 인풋은 inputfile에서 읽어온 값으로 설정
- pg > outputfile

  - pg의 아웃풋은 outputfile에 인풋으로 설정

- 실제 코드에서 0번을 읽으면 쉘에서의 인풋을 읽음
- 1번에 write하면 쉘에 아웃풋을 써줌

### Standard I/O Library

- 일반적인 UNIX는 파일 관리에 fd를 사용
- 라이브러리는 FILE\* 데이터 타입을 사용해서 파일 관리
- System Call 앞에 f를 붙여서 사용
  - ex. fopen, fclose, fread, fwrite, fgets, fputs, fscanf, fprintf

##### fopen

```c++
FILE* fopen(const char *restrict path, const char *restrict type);
```

- type

  - r(read), w(write), a(append)
  - 위 3개에 b가 붙으면 binary
  - +가 붙으면 rw, ra

- restrict
  - C에서만 있는 개념
  - 같은 주소를 가리키는 다른 포인터를 허용하지 않음
  - restrict가 붙은 포인터는 유니크한 주소를 가짐
