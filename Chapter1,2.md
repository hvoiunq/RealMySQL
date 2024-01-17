# Chapter1
- 오라클보다 MySQL의 경쟁력? 
- RDBMS를 고르는 기준
  - 안정성
  - 성능, 기능
  - 커뮤니티, 인지도

# Chapter2
## 2.1 MySQL 서버 설치
- 서버 버전 선택 기준 : 기존 솔루션이 특정 버전만 지원하는 경우가 아니라면 최신 버전을 추천
- 기존 버전에서 업그레이드 하는 경우에는 패치 버전이 15~20번 이상 릴리즈된 버전을 선택해야 안정적
- MySQL 8.0 버전의 경우 8.0.15부터 8.0.20 사이 버전 시작하는 것을 추천
- 엔터프라이즈와 커뮤니티는 서버의 기능 차이보다는 기술 지원의 차이만 있음.
  - MySQL 상용화전략 : 엔터프라이즈와 커뮤니티 에디션 모두 동일함.
  - 특정 부가 기능들만 상용 버전인 엔터프라이즈 에디션에 포함되는 방식.

### 2.1.2 MySQL 설치
- mac에서 설치하기
  - https://dev.mysql.com/downloads/mysql/
  - os에 맞게 dmg 파일 다운
  - 인증방식 : 'Use Strong Password Encryption' 사용 (인터넷 경유해 MySQL 서버를 접속하는 경우)
- MySQL 디렉토리
  - bin : MySQL 서버, 클라이언트 프로그램, 유틸리티를 위한 디렉터리
  - data : 로그 파일, 데이터 파일 저장 디렉터리
  - include : C/C++ 헤더 파일들 저장 디렉터리
  - lib : 라이브러리 파일 저장 디렉터리
  - share : 다양한 지원파일 저장, 에러메세지, 샘플 설정파일 (my.cnf) 디렉터리
- 실행방법
  - 시스템 환경설정에서 MySQL 서버 시작 및 종료 가능
  - 터미널 : ```sudo /usr/local/mysql/support-files/mysql start```  ```sudo /usr/local/mysql/support-files/mysql stop```
- Configuration file 설정
  - /usr/local/mysql 하위에 my.cnf 빈파일 생성 <-- 이 경로는 많이 쓰이니 꼭 기억해두자!