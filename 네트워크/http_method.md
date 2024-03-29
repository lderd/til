# HTTP request methods

http 통신의 특성에 의해 [REST](./RESTful.md)가 등장함. REST를 지키면서 서버와 클라이언트가 통신을 하는 방법. 각각의 메서드는 각각 다른 의미를 나타내지만 몇 개의 그룹으로 나뉘어져서 각각이 공유하는 특성도 있음.
safe(안전함, http 메서드가 서버의 상태 변경을 요청하지 않는 경우 안전하다고 함)
idempotent(멱등성, 몇 번을 요청하더라도 결과가 같은 경우 멱등하다 함)
cacheable(캐시가능, 저장 가능함)

## 종류

### GET

`GET` method는 지정된 리소스의 표현을 요청함. 오직 데이터의 획득에만 사용함.
리소스의 조회에 사용하는 메서드. 서버에 전달할 데이터는 쿼리를 통해 전달. 요청내용이 주소창에 표시됨.

### HEAD

`HEAD` method는 `GET` method와 동일하지만 response body가 없는 응답을 받음.
response는 필요 없고 자원을 찾기만 원할 때, 응답의 상태 코드를 확인할 때, 응답의 헤더를 확인할 때 등에 쓸 수 있다.

### POST

`POST` method는 entity를 지정된 리소스에 제출하며 종종 서버의 상태 변경이나 side effect를 유발함.
새로운 리소스를 생성하는데 주로 사용.

### PUT

`PUT` method는 대상의 모든 리소스를 현재 요청의 payload로 바꿈.
리소스를 대체(수정, 생성)하는 메서드. 요청의 리소스가 존재한다면 덮어쓰고, 존재하지 않는다면 새로 생성한다. 단, 리소스의 모든 정보를 덮어쓰기 하기 때문에 요청에 수정할 내용 일부만을 넣어 보내면 요청에 존재하지 않는 부분은 삭제됨.

### DELETE

`DELETE` method는 지정된 리소스를 삭제함.

### CONNECT

`CONNECT` method는 대상 리소스로 식별된 서버의 터널을 설정함. 프록시 동작의 터널 접속을 변경함.

### OPTIONS

`OPTIONS` method는 대상 리로스에 대한 통신 옵션을 설명함.
[`preflight`](./CORS.md)에 사용되는 메서드. 지원 가능한 HTTP method와 출처를 응답받음.

### TRACE

`TRACE` method는 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트를 수행함.
서버에 도달했을 때의 최종 패킷의 요청 패킷 내용을 응답함.
클라이언트의 요청 패킷이 방화벽, 프록시 서버, gateway 등을 거치면서 변조가 일어날 수 있는데, `TRACE` 메서드를 사용해 요청한 패킷 내용과 응답받은 패킷 내용을 비교하여 변조가 일어났는지를 확인할 수 있음.

### PATCH

`PATCH` method는 리소스를 부분 수정함.
