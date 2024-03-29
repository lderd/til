# REST

Representational State Transfer

API의 작동 방식에 대한 조건을 부과하는 소프트웨어 아키텍처

이를 이용하면 쉽게 구현할 수 있고 수정할 수 있음

## 원칙

- 클라이언트-서버 구조

  - 클라이언트와 서버는 독립적이어야 한다

- 균일한 인터페이스

  1. - 요청 시 개별 자원을 식별할 수 있어야 한다

     - 요청할 때 액세스 하려는 리소스를 식별할수 있어야 한

     - 이를 위해 균일한 식별자를 사용한다

  2. - 서버가 자원을 변경 삭제하기 위해서는 요청에 이에관한 정보가 있어야 한다

     - 동일한 URI를 통한 요청에 두 개 이상의 작업이 가능하다

     - 이는 [HTTP Method](./http_method.md)로 구분한다

  3. - 요청, 응답의 메시지는 그 자체만 보고 의미를 파악할 수 있어야 한다

  4. - 클라이언트가 작업을 완료하는데 필요한 다른 모든 리소스에 대한 정보를 수신해야 한다

     - 단순한 결과뿐 아니라 결과에 대한 정보도 포함해야 한다

- 무상태

  - 서버가 이전의 모든 요청과 독립적으로 모든 요청을 처리한다

  - 서버가 작업을 위한 정보를 따로 저장하지 않는다

  - 클라이언트에서 모든 요청에 각 요청을 처리할 모든 정보가 포함되어야 한다

- 계층화 시스템

  - 다중 계층을 가질 수 있다

  - 서버는 요청을 다른 서버로 전달할 수 있다

- 캐시 가능성

  - 서버는 헤더에 요청에 대한 응답을 캐싱해도 되는지 전달하여 캐싱이 가능하다면 클라이언트가 해당 응답을 캐싱하여 서버와 클라이언트 간의 상호작용을 줄일 수 있다

- 온디맨드 코드(optional)

  - 서버에서 프로그래밍 코드를 클라이언트에 전송하여 기능을 확장하거나 지정할 수 있다

  - 이를 통해 클라이언트가 사전에 구현해야 하는 기능의 수를 줄일 수 있다

## 개념

- URI를 통해 자원(리소스)를 명시하고 HTTP Method(Get, Post, Put, Delete)를 통해 자원에 대한 CRUD를 한다.

## 장단점

### 장점

- HTTP 프로토콜의 표준을 최대한 활용하여 추가적인 장점을 함께 가질 수 있다

- 클라이언트-서버의 상호작용을 최적화 하고 요청정보를 저장하지 않는 등의 특징으로 인해 서버 부하가 적다

- 클라이언트와 서버의 분리로 인해 서버의 플랫폼, 기술의 변경 등은 클라이언트에게 영향을 주지 않는다

### 단점

- 구형 브라우저는 PUT, DELETE 메서드가 지원되지 않는다
