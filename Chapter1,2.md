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
    - intel : x86 / apple : ARM
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

### 2.3.2 MySQL 8.0 업그레이드 시 고려 사항
- 사용자 인증방식 변경
  - 8.0부터 Caching SHA-2 Authentication 인증 방식이 기본으로 바뀜
  - 그 이전에는 Native Authentication 인증 방식 사용
- MySQL 8.0과의 호환성 체크
  - MySQL 5.7버전에서 손상된 FRM파일, 호환되지 않는 데이터 타입 또는 함수가 있는지 mysqlcheck 유틸리티 이용 권장
- 외래키 이름 길이 
  - MySQL 8.0에서는 외래키 이름이 64글자로 제한됨
- 인덱스 힌트 
  - 5.x 에서 사용하던 인덱스 힌트가 8.0에서는 오히려 성능 저하를 유발할 수 있으니 성능 테스트는 꼭 하자. 
- GROUP BY 사용 정렬 옵션
  - GROUP BY 뒤에 ASC, DESC 사용하고 있다면 제거 필요
- 파티션 공용 테이블 스페이스 
  - MySQL 8.x 에서는 파티션의 각 테이블스페이스를 공용 테이블스페이스에 저장할 수 없다.
  - 공용 테이블 스페이스에 저장된 것이 있다면, ALTER TABLE ... REORGANIZE 명령으로 개별 스페이스 사용하도록 변경 필요

## 2.4 서버 설정
- MySQL은 일반적으로 설정파일이 하나이다.
  - 리눅스 포함 유닉스 계열 : my.cnf
  - 윈도우 계열 : my.ini
- 설정 파일은 고정 경로가 아니다.
  - 여러개의 디렉토리를 순차적으로 탐색하면서 처음 발견된 my.cnf 사용
  - 탐색순서 
    - /etc/my.cnf
    - /etc/mysql/my.cnf
    - /usr/etc/my.cnf
    - ~/.my.cnf

### 2.4.2 MySQL 시스템 변수의 특징
- 시스템 변수 : MySQL 서버를 기동하면서 설정 파일의 내용을 읽어 메모리나 작동 방식을 제어하고, 접속된 사용자를 제어하기 위해 별도 저장하는 값
  - SHOW VARIABLES
  - SHOW GLOBAL VARIABLES
### 2.4.3 글로벌 변수와 세션 변수
- 글로벌 (범위의 시스템) 변수 : 하나의 서버 인스턴스에서 전체적으로 영향을 미치는 시스템 변수
- 세션 (범위의 시스템) 변수 : MySQL 클라이언트가 MySQL 서버에 접속할 때 기본으로 부여하는 옵션의 기본값 제어에 사용
### 2.4.4 정적 변수와 동적 변수
- 동적 변수 : MySQL 서버가 기동 중에 변경 가능한
- MySQL 8.0 -> SET PERSIST 명령어
  - 변경된 값을 서버에 즉시 적용하고 mysqld-auto.cnf 파일에 변경 내용을 추가로 기록해둠.
  - 재시동할때 my.cnf + mysqld-auto.cnf 같이 참조해서 시스템 변수가 적용됨.