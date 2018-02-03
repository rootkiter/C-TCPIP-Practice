# C-Socket-Practice

윤성우 열혈 TCP/IP 스터디하는 저장소입니다.

[CH 7\. TCP 기반의 Half-close](./MD/CH7.md)<br>
`Half-close 개념, 우아한 종료를 위한 shutdown`

[CH 8\. IP주소와 도메인 이름 사이의 변환](./MD/CH8.md)<br>
`도메인 이름, IP 를 사용해서 host 정보 얻어오기.`

[CH 9\. 소켓의 다양한 옵션](./MD/CH9.md)<br>
`소켓의 옵션 확인 및 설정 방법`

[CH 10\. 멀티프로세스 기반의 서버 구현](./MD/CH10.md)<br>
`fork, waitpid, WIFEXITED, WEXITSTATUS, sigaction, 멀티태스킹 기반의 다중접속 서버, TCP의 입출력 루틴 분할.`

[CH 11\. 프로세스간 통신의 기본 개념](./MD/CH11.md)<br>
`파이프(PIPE) 기반의 프로세스간 통신(feat.fork)`

[CH 12\. IO 멀티플렉싱](./MD/CH12.md)<br>
`select에 대해 배움. epoll, IOCP 전 워밍업`

## Linux

### Linux Socket API

#### [socket](http://man7.org/linux/man-pages/man2/socket.2.html)

```c
// 성공 시 파일 디스크립터, 실패 시 -1 반환
int socket(int domain, int type, int protocol);
```

#### [bind](http://man7.org/linux/man-pages/man2/bind.2.html)

```c
// 성공 시 0, 실패 시 -1 반환
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

#### [listen](http://man7.org/linux/man-pages/man2/listen.2.html)

```c
// 성공 시 0, 실패 시 -1 반환
int listen(int sockfd, int backlog);
```

#### [accept](http://man7.org/linux/man-pages/man2/accept.2.html)

```c
// 성공 시 파일 디스크립터, 실패 시 -1 반환
// 연결요청이 없는 상태에서 이 함수가 호출되면, 연결요청이 있을 때까지 함수는 반환하지 않는다.
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```

#### [connect](http://man7.org/linux/man-pages/man2/connect.2.html)

```c
// 성공 시 0, 실패 시 -1
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

### Linux Socket Data Reception, Transmission

> 리눅스에서의 소켓조작은 파일조작과 동일하게 간주된다. 리눅스는 소켓을 파일의 일종으로 구분한다.

#### [open](http://man7.org/linux/man-pages/man2/open.2.html)

```c
// 성공 시 파일 디스크립터, 실패 시 -1 반환
int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);
```

#### [write(tcp)](http://man7.org/linux/man-pages/man2/write.2.html)

```c
// 성공 시 전달한 바이트 수, 실패 시 -1 반환
ssize_t write(int fd, const void *buf, size_t count);
```

#### [read(tcp)](http://man7.org/linux/man-pages/man2/read.2.html)

```c
// 성공 시 수신한 바이트 수(단 파일의 끝을 만나면 0), 실패 시 -1 반환
ssize_t read(int fd, void *buf, size_t count);
```

#### [sendto(udp)](http://man7.org/linux/man-pages/man3/sendto.3p.html)

```c
// 성공 시 전송된 바이트 수, 실패 시 -1 반환
ssize_t sendto(int                    socket,
               const void            *message,
               size_t                 length,
               int                    flags,
               const struct sockaddr *dest_addr,
               socklen_t              dest_len);
```

#### [recvfrom(udp)](http://man7.org/linux/man-pages/man3/recvfrom.3p.html)

```c
// 성공 시 수신한 바이트 수, 실패 시 -1 반환
ssize_t recvfrom(int                       socket,
                 void *restrict            buffer,
                 size_t                    length,
                 int                       flags,
                 struct sockaddr *restrict address,
                 socklen_t *restrict       address_len);
```

## Windows

윈속(윈도우 소켓)을 기반으로 프로그램을 개발하기 위해서는 기본적으로 다음 두 가지를 진행해야 한다.

- 헤더파일 winsock2.h를 포함한다.
- ws2_32.lib 라이브러리를 링크시켜야 한다.

  - 프로젝트 단위 링크 방법: 프로젝트 '속성' - '구성 속성' - '링커' - '입력' - '추가 종속성' - 'ws2_32.lib' 추가.

## Windows Socket API

### WSAStartup

윈속 프로그래밍을 할 때에는 반드시 WSAStartup 함수를 호출해서, 프로그램에서 요구하는 윈도우 소켓 버전을 알리고, 해당 버전을 지원하는 라이브러리의 초기화 작업을 진행해야 한다.<br>

- `LPWSADATA` : WSADATA 구조체 변수의 포인터 형이다.

```c
// 성공 시 0, 실패 시 0이 아닌 에러코드 값 반환
WSAStartup(WORD wVersionRequested, LPWSADATA lpWSAData );
```

### WSACleanup

프로그램이 종료 되기 직전에 실행하는 것이 보통이며, 할당된 윈속 라이브러리를 윈도우 운영체제에 반환한 다.<br>

```c
// 성공 시 0, 실패 시 SOCKET_ERROR 반환
int WSACleanup(void);
```

### [socket](https://msdn.microsoft.com/en-us/library/windows/desktop/ms740506(v=vs.85).aspx)

```c
// 성공 시 소켓 핸들, 실패 시 INVALID_SOCKET 반환
SOCKET socket(int af, int type, int protocol);
```

### bind

```c
// 성공 시 소켓 핸들, 실패 시 INVALID_SOCKET 반환
int bind(SOCKET s, const struct sockaddr *name, int namelen );
```

### listen

```c
// 성공 시 0, 실패 시 SOCKET_ERROR 반환
int listen(SOCKET s, int backlog);
```

### accept

```c
// 성공 시 소켓 핸들, 실패 시 INVALID_SOCKET 반환
SOCKET accept(SOCKET s, struct sockaddr *addr, int *addrlen);
```

### connect

```c
// 성공 시 0, 실패 시 SOCKET_ERROR 반환
int connect(SOCKET s, const struct sockaddr *name, int namelen );
```

### Windows Socket Data Reception, Transmission

리눅스는 소켓도 파일로 간주하기 때문에, 파일 입출력 함수인 read와 write 를 이용해서 데이터를 송수신 할 수 있다.<br>
하지만 윈도우는 파일 입출력 함수와 소켓 입출력 함수가 구분되어 있다. 아래는 윈도우 소켓 기반의 데이터 입출력 함수이다.

#### send(tcp)

```c
// 성공 시 전송된 바이트 수, 실패 시 SOCKET_ERROR 반환
int send(SOCKET s, const char *buf, int len, int flags);
```

#### recv(tcp)

```c
// 성공 시 수신한 바이트 수(단 EOF 전송 시 O), 실패 시 SOCKET_ERROR 반환
int recv(SOCKET s, const char *buf, int len, int flags);
```

#### sendto(udp)

```c
// 성공 시 전송된 바이트 수, 실패 시 SOCKET_ERROR 반환
int sendto(SOCKET                 s,
           const char            *buf,
           int                    len,
           int                    flags,
           const struct sockaddr *to,
           int                    tolen);
```

#### recvfrom(udp)

```c
// 성공 시 수신한 바이트 수, 실패 시 SOCKET_ERROR 반환
int recvfrom(SOCKET           s,
             char            *buf,
             int              len,
             int              flags,
             struct sockaddr *from,
             int              fromlen);
```
