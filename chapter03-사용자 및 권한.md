# 03 사용자 및 권한
## 3.1 사용자 식별
- MySQL 다른 DBMS와의 다른점
  - 사용자의 계정 뿐만 아니라 사용자의 접속 지점(클라이언트가 실행된 호스트명이나 도메인 or IP주소)도 계정의 일부가 된다.
- 계정을 언급할 때 항상 아이디와 호스트를 함께 명시해야한다.
  - 로컬에서만 접속하는 경우 : '계정명'@'127.0.0.1'
  - 모든 외부 컴퓨터에서 접속을 허용하려는 경우 : '계정명'@'%'
- 동일한 계정명이 존재하는 경우 범위가 작은 것부터 먼저 선택
## 3.2 사용자 계정 관리
- 시스템 계정 : 데이터베이스 서버 관리자용
- 일반 계정 : 응용 프로그램이나 개발자용
- MySQL에 내장된 계정 중 'root'@'localhost'를 제외한 아래 3가지는 삭제하지 말것
  - 'mysql.sys'@'localhost' : sys 스키마 객체(뷰, 함수, 프로시저)들이 DEFINER로 사용됨
  - 'mysql.session'@'localhost': MySQL플러그인이 서버 접근할 때 사용하는 계정
  - 'mysql.infoschema'@'localhost' : information_schema에 정의된 뷰의 DEFINER로 사용되는 계정
### 3.2.2 계정 생성
- MySQL 8.0 버전부터 계정생성은 CREAT USER, 권한 부여는 GRANT 명령어를 구분해서 사용
  - GRATE USER 'user'@'host' IDENTIFIED WITH 'mysql_native_password' BY 'password'
- IDENTIFIED WITH : 인증 방식
  - Caching SHA-2 Pluggable Authentication : 암호화 해시값 생을 위해 SHA-2(256비트) 알고리즘 사용 (MySQL 8.0 기본 인증 방식)
    - 내부적으로 salt키를 활용해 수천 번의 해시 계산을 수행함.
    - 동일한 키 값에 대해서도 결과가 달라짐
    - 성능 이슈로 해시 결과값을 메모리에 캐시에 사용함 (그래도 CPU 자원 소모가 많다.) 
    - SSL/TLS 또는 RSA 키페어를 반드시 사용해야하고 접속할 때 SSL 옵션 활성화 필요
## 3.3 비밀번호관리
### 3.3.1 고수준 비밀번호
- MySQL 서버 프로그램 > validate_password 컴포넌트 
  - 쉽게 유추 가능한 단어들을 사용되지 않게 조합 강제 및 금칙어 설정 가능
  - 기본값 : MEDIUM (LOW, MEDIUM, STRONG)
  - ```mysql > INSTALL COMPONENT 'file://component_validate_password'```
    - 높은 보안 수준을 요구하는 서비스에서 제약을 주려면 -> validate_password.dictionary_file 파일에 저장하면 된다.
    - github를 통해 참고 자료 찾기 기능!
- 파일 준비가 완료된다면
  - SET GLOBAL validate_password.dictionary_file = '파일명';
  - validate_password.policy = 'STRONG';
    - 금칙어 설정은 STRONG 만 가능
### 3.3.2 이중 비밀번호
- 등장 배경
  - 데이터베이스 계정 비밀번호는 서비스가 실행 중인 상태에서 변경이 불가능.
  - 공용 서버를 사용하는 경우, 보안상의 이슈가 있으나 주기적으로 변경이 어려운 문제가 생김.
- 이중 비밀번호
  - 이를 보완하기 위해 MySQL8.0 부터 도입된 개념
  - 2개 비밀번호 중 하나만 일치해도 통과됨.
  - Primary, Secondary 로 구분
    - Primary : 최근 설정한 비밀번호
    - Secondary : 변경 전 비밀번호 
- 명령어
  - ```ALTER USER 'user'@'ip' IDENTIFIED BY 'old_password';```
  - ```ALTER USER 'user'@'ip' IDENTIFIED BY 'new_password' RETAIN CURRENT PASSWORD;```
  - ```ALTER USER 'user'@'ip' DISCARD OLD PASSWORD;```
    - 서버 접속하는 모든 응용프로그램이 재시작이 완료된 경우 세컨더리 비밀번호 삭제
## 3.4 권한(Privilege)
- 글로벌 권한 / 객체 권한
  - 글러벌 권한은 GRANT 명령에서 특정 객체를 명시하면 안됨.
  - 객체 권한은 GRANT 명령에서 특정 객체 명시 필수.
- 사용자 권한
  - ```GRANT privilege_list ON DB명.오브젝트명(table명) TO 'username'@'host';```
    - MySQL부터 등록되지 않은 사용자면 오류 발생
- 글로벌 권한
  - ```GRANT SUPER ON *.* TO 'user'@'localhost';```
- DB 권한
  - ```GRANT EVENT ON *.* TO 'user'@'localhost';```
  - ```GRANT EVENT ON employees.* TO 'user'@'localhost';```
- 테이블 권한
  - ```GRANT SELECT, INSERT, UPDATE, DELETE ON DB명.TABLE명 TO 'user'@'localhost';```
  - 테이블 특정 컬럼에도 권한 부여가 가능하나 잘 사용하지 않음. (모든 테이블의 모든 칼럼에 대해 권한 체크를 하기 때문에 성능 영향)
- 컬럼 단위로 접군권한이 필요하다면 테이블에서 권한 허용하려는 컬럼만 별도 뷰(VIEW) 만드는 것 추천
  - 뷰도 하나의 테이블로 인식되기 때문
- 권한 조회 : SHOW GRANTS or table 조회 가능
## 3.5 역할(Role)
- MySQL 8.0 부터 나온 개념
- role 이름 설정
  - ```GRANT 'ROLE이름'```
    - 호스트를 명시하지 않는 경우 모든 호스트(%)로 자동 추가됨
- 역할에 권한 부여
  - ```GRANT ROLE(SELECT, INSERT, UPDATE, DELETE) ON DB.TABLE TO 'ROLE이름'```
- user생성
  - ```CREATE USER 'user'@'host' IDENTITFIED BY ~~~```
- role에 user 설정
  - ```GRANT ROLE이름 TO 'user'@'host'```
- 역할 사용할 수 있도록 실행
  - ```SET ROLE 'ROLE이름'```
- 특징
  - MySQL 서버 내부적으로는 역할과 계정의 차이가 없는 같은 객체로 바라본다.
  - 구분을 하기 위해선 관리자가 post, prefix 구분자로 관리해야한다.
- 그럼 굳이 계정과 역할을 나누어서 사용할까?
  - 데이터베이스 관리 직무를 분리할 수 있게 해, 보안을 강화하는 용도로 사용될 수 있게 하기 위함.
