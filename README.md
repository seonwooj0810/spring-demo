# spring-demo

회원(Member) 도메인을 예시로, **계층형 패키지 구조**와 **Command/Query 리포지토리 분리**를 연습하는 Spring Boot 데모 프로젝트.

## 기술 스택

- Java 17
- Spring Boot 4.0.0 / Spring Web MVC
- Spring Data JPA
- PostgreSQL (운영 datasource), H2 (테스트)
- springdoc-openapi (Swagger UI)
- P6Spy (`p6spy-spring-boot-starter`) — SQL 로그 포매팅
- Lombok

> 빌드 파일 기준. `build.gradle`의 Spring Boot 버전은 `4.0.0`으로 명시되어 있다.

## 실행

```bash
./gradlew bootRun
```

기본 프로파일은 `DEV`이며, `application.yml`은 로컬 PostgreSQL(`jdbc:postgresql://localhost:5432/study`)을 사용한다. `ddl-auto: create`로 기동 시 스키마를 새로 만든다. (로컬 DB 접속 정보는 환경에 맞게 수정 필요.)

- Swagger UI: 기동 후 `/swagger-ui/index.html`

## 구조

```
com.example.demo
├── DemoApplication.java
├── common/
│   ├── ApiResponse.java        # 공통 응답 래퍼
│   └── BaseTimeEntity.java     # 생성/수정 시각 공통 엔티티
├── config/
│   ├── SwaggerConfig.java
│   ├── P6SpySqlFormatConfig.java   # SQL 로그 포맷
│   └── TestDataConfig.java
└── member/
    ├── presentation/           # 컨트롤러 + 요청/응답 DTO(in/out)
    │   ├── MemberController.java        # 회원 가입(명령)
    │   └── MemberQueryController.java   # 조회
    ├── application/            # 서비스
    │   ├── MemberService.java           # 가입 처리
    │   └── MemberQueryService.java
    ├── domain/                 # 엔티티/enum (Member, MemberAuth, UserType, ProviderType, AdminType)
    └── repository/
        ├── command/            # 쓰기용 (MemberRepository)
        └── query/              # 읽기용 (MemberAuthQueryRepository)
```

## API

- `POST /v1/members` — 회원 가입. `RegisterMemberRequest`(nickname, phoneNumber, provider, uid, password)를 받아 `Member` + `MemberAuth`를 생성하고 생성된 회원 id를 `ApiResponse`로 반환한다.

> 위 엔드포인트는 코드(`MemberController`)에서 확인된 항목이다. 조회 컨트롤러(`MemberQueryController`)도 존재한다.

## 설계 포인트

- **패키지 by feature + 계층 분리**: `member` 도메인 아래 presentation / application / domain / repository를 둔다.
- **Command/Query 리포지토리 분리**: `repository/command`(쓰기)와 `repository/query`(읽기)를 디렉터리로 나눠 책임을 구분.
- **공통 응답/엔티티**: `ApiResponse`로 응답 형식을 통일하고, `BaseTimeEntity`로 생성/수정 시각을 공통화.
