Http Header에는 공통헤더, 응답헤더, 요청헤더가 있습니다.

### 공통 헤더

+ **Date** : 현재 시간(Wed, 23 Feb 2022 GMT)
+ **Cache-Control** : 캐시 제어
    + no-store : 캐시를 저장하지 않음
    + no-cache : 모든 캐시를 쓰기 전에 서버에 해당 캐시를 사용해도 되는지 확인함
    + must-revalidate : 만료된 캐시만 서버에 확인함
    + private : 브라우저 같은 특정 사용자 환경에만 저장함
    + max-age : 캐시의 유효시간 명시
+ **Transfer-Encoding** : body 내용 자체 압축 방식을 지정
    + 'chuncked'면 본문의 내용이 동적으로 생성되어 길이를 모르기 때문에 나눠서 보낸다는 의미다.
+ **Upgrade** : 프로토콜 변경시 사용 ex) HTTP/2.0
+ **Via** : 중계(프록시)서버의 이름, 버전, 호스트명
+ **Content-Encoding** : 본문의 리소스 압축 방식 (transfer-encoding은 body 자체이므로 다름)
+ **Content-Type** : 본문의 미디어 타입(MIME) ex) application/json, text/html
+ **Content-Length** : 본문의 길이
+ **Content-Language** : 본문을 이해하는데 가장 적절한 언어 ex)ko
+ **Expires** : 자원의 만료 일자
+ **Allow** : 사용이 가능한 HTTP메소드 방식 ex) GET, HEAD, POST
+ **Last-Modified** : 최근에 수정된 날짜
+ **ETag** : 캐시 업데이트 정보를 위한 임의의 식별 숫자
+ **Connection** : 클라이언트와 서버의 연결 방식 설정 HTTP/1.1은 Keep-alive로 연결 유지하는것이 default

### 요청 헤더
+ **Host** : 요청하려는 서버호스트 이름과 포트번호
+ **User-agent** : 클라이언트 프로그램 정보
+ **Referer** : 바로 직전에 머물렀던 웹 링크 주소
+ **Accept** : 클라이언트가 처리 가능한 미디어 타입 종류 나열
+ **Accept-charset** : 클라이언트가 지원 가능한 문자열 인코딩 방식
+ **Accept-language** : 클라이언트가 지원 가능한 언어 나열
+ **Accept-encoding** : 클라이언트가 해석 가능한 압축 방식 지정
+ **Content-location** : 해당 개체의 실제 위치
+ **Content-disposition** : 응답 메세지를 브라우저가 어떻게 처리하는지 알려줌
+ **Content-Security-Policy** : 다른 외부 파일을 불러오는 경우 차단할 리소스와 불러올 리소스 명시
+ **if-Modified-Since** : 여기에 쓰여진 시간 이후로 변경된 리소스 취득
+ **Autorization** : 인증 토큰을 서버로 보낼 때 쓰임
+ **Origin** : 서버로 Post요청을 보낼때 요청이 어느 주소에서 시작되었는지 나타내는 값
+ **Cookie** : 쿠키 값 key-value로 표현

### 응답 헤더
+ **Location** : 301, 302상태 코드일 경우에만 볼 수 있는 헤더로 서버의 응답이 다른 곳에 있다고 알려주면서 해당 위치를 지정
+ **Server** : 웹 서버의 종류
+ **Age** : max-age 시간 내에서 얼마나 흘렀는지 초 단위로 알려줌
+ **Referrer-policy** : 서버 referrer 정책을 알려주는 값
* **www-Authenticate** : 사용자 인증이 필요한 자원을 요구할 시, 서버가 제공하는 인증 방식
+ **Proxy-Authenticate** : 요청한 서버가 프록시 서버인 경우 유저 인증을 위한 값


### 마무리

간단하게 종류와 해당 옵션이 무엇을 의미하는지 정리해보았습니다. 



