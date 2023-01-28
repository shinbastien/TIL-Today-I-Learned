### blocking, nonBlocking, sync, async I/O

* 참고문헌
  * [blocking I/O, non-blocking I/O에 대하여, 티스토리 블로그](https://etloveguitar.tistory.com/140)
  * ㄹㅇㄹ

### 먼저, I/O 란?
* input 과 output을 일컫는 말
  * 단순 파일 i/o 만이 아닌, 네트워크 통한 데이터 전송도 포함

### blocking, non-blocking I/O 란?
* blocking I/O
  * I/O 작업동안 유저 프로세스 중단 => I/O 작업 종료 후 재개
* non-blocking I/O
  * 기존 유저 프로세스를 중단하지 않고 즉각적으로 I/O 호출 리턴.
  * 유저 프로세스 작업 도중 시스템 콜을 보내, I/O 작업의 완료 여부를 확인

### async, sync I/O 는?
* 호출되는 함수 작업 완료에 따라 이어지는 작업을 누가 수행하는지에 대한 내용
* sync => I/O 작업이 완료된 이후 작업을 호출한 함수에서 갖고 간다.
* async => 작업 완료 여부와 상관없이 다음 동작 수행.
  * 완료 이후는 콜백 함수에서 실행하기에, 기존 호출 함수와는 무관.

### 복합 구조, 서로 합쳐졌을 때는 어떻게 되는건가?
* sync + blocking
  * blocking: I/O 작업이 처리될 때까 제어권을 커널에서 갖고 간다.
  * sync: 작업이 완료되면 작업 결과를 갖고 어플리케이션에서 직접 처리  
  

* async + blocking
  * blocking: 제어권을 I/O에서 갖고 있기 때문에, 유저 프로세스는 I/O 작업 완료 이전에는 다른 작업 실행할 수 없다.
  * async: 작업 결과를 어플리케이션 내 호출 함수가 아닌, 콜백 함수가 처리
  * 이 모델이 의도적으로 쓰이기보다는, non-blocking + async 을 하다가, 일부 과정에서 blocking을 쓰는 경우.
    * 예시) nodejs 에서 blocking 방식인 mysql 드라이버를 실행하는 경우
    

* sync + non-blocking
  * non-blocking: 커널 I/O 작업 여부와 무관하게 어플리케이션 작업 수행
  * sync: 그러나 sync 이기 때문에, 호출 함수에서 I/O 결과를 바탕으로 작업 수행. 
    * 그러면 경우에 따라선 자연스럽게 blocking이 될수도 있는 거일지도..? (응답 결과를 사용하는 부분이 바로 있다면?)
    * I/O 작업 처리 여부를 시스템 콜로 지속적으로 확인
  * 결과를 반환받기까지 busy-wait 상태가 반복된다.
  

* async + non-blocking
  * non-blocking: 유저 프로세스는 I/O 완료되기 전에 작업 수행 가능
  * async: 작업 완료 결과도 호출 함수가 아닌, 콜백에서 갖고 가기에, 이후 콜백 함수를 별도로 실행
    * 유저 프로세스 => 커널 쪽 시스템 콜이 아닌, 커널이 완료되 시스템 콜을 유저 쪽으로 주기만 하면 된다.

### 이후 공부할 것들
  * I/O multiplexing, Socket I/O
  * 싱글 스레드가 아닌 경우 어떻게 대응되는 것인가? 에 대한 이해.
