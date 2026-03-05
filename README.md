# KT_AIVLE_AWS
📚 AI 이미지 생성 기반 도서 관리 시스템

---

# 💡 프로젝트 소개
본 프로젝트는 사용자가 자신만의 책을 등록하고 관리할 수 있는 개인 맞춤형 서재 웹 서비스입니다.
가장 큰 특징은 단순한 텍스트 기반의 도서 관리를 넘어, OpenAI의 DALL-E 3 모델을 활용하여 사용자가 입력한 책의 제목, 장르, 줄거리를 바탕으로 세상에 하나뿐인 AI 도서 표지를 자동으로 생성해 준다는 점입니다.

안정적인 REST API 통신과 체계적인 예외 처리, 그리고 CI/CD 파이프라인이 구축된 Spring Boot 백엔드와, React 기반의 직관적이고 반응형 UI를 제공하는 프론트엔드가 결합된 풀스택 프로젝트입니다.

---

# 🛠 Tech Stack
* **Frontend** : React, Vite, React Router, MUI (Material-UI)

* **Backend** : Spring Boot, Spring Data JPA, Hibernate, JWT

* **AI & API** : OpenAI Images API (DALL-E 3) 

* **Infra & DevOps** : AWS EC2, GitHub Actions

--- 

# 💡 핵심 기능 

### 1️⃣ 🔐 사용자 인증 및 개인 맞춤형 서재

* **JWT 기반 보안 및 인증 유지** : 백엔드의 JWT 기반 인증/인가 구현 및 프론트엔드의 localStorage를 활용한 로그인 상태 유지 처리

* **독립적인 도서 필터링** : 도서 데이터에 ownerId를 부여하여 사용자별 자신의 개인 서재 목록만 안전하게 조회하도록 데이터 격리 적용

### 2️⃣ 📖 도서 관리 시스템 (Book CRUD) 및 반응형 UI

* **SPA 기반의 매끄러운 화면 전환** : React Router를 활용한 회원가입, 메인(목록/검색), 도서 상세, 등록·수정 페이지 라우팅 설계

* **직관적인 사용자 경험(UX)** : 공통 AppBar 레이아웃 및 MUI 커스텀 테마를 적용하여 반응형 카드 디자인과 Hover 효과 구현

* **안정적인 데이터 쓰기/읽기** : 백엔드 도메인 설계 시 책 등록/수정/삭제 등 상태 변경 작업에 트랜잭션을 적용하여 데이터 무결성 보장

### 3️⃣ 🎨 OpenAI DALL-E 3 기반 표지 자동 생성

* **AI 프롬프트 최적화 및 커스텀 UI** : 도서 등록 시 제목, 장르, 줄거리 데이터와 사용자 선택 옵션(모델, 품질, 스타일, 해상도)을 결합하여 AI 이미지 생성 API 호출

* **실시간 미리보기 및 DB 연동** : 생성된 이미지 URL을 즉시 프론트엔드 coverPreview에 렌더링하고, 최종 등록 시 백엔드 imageUrl 필드에 안전하게 저장하여 메인/상세 화면에서 지속 활용

--- 

# 💡 시스템 아키텍처 및 기술적 주안점

본 프로젝트는 단순 기능 구현에 그치지 않고, 프론트엔드와 백엔드 간의 안정적인 데이터 교환과 서버 운영의 신뢰성 확보에 집중함.

### 1️⃣ 프론트-백엔드 API 연동 및 데이터 정합성 확보

* **REST API 중심 구조 전환** : 기존 프론트엔드의 Mock 데이터(data/*.js) 구조를 전면 제거하고, Axios 및 bookService 모듈 기반의 REST API 호출 구조로 리팩터링 완료

* **DTO 1:1 매핑 및 트러블슈팅** : 백엔드 Swagger 문서를 기준으로 요청/응답(JSON) 필드를 정확히 매핑. 개발 과정에서 발생한 CORS 문제, 파라미터 누락, 400 Bad Request 등의 오류를 Network 탭과 서버 로그를 교차 분석하여 완벽히 해결

### 2️⃣ 트랜잭션(@Transactional) 기반의 서비스 설계

* **쓰기 작업(CUD) 원자성 보장** : 도서 등록, 수정, 삭제 로직에 @Transactional을 적용하여 예외 발생 시 자동 롤백되도록 설계. ➡️ 데이터 일관성 유지

* **조회 작업(Read) 성능 최적화** : 도서 목록 및 상세 조회에는 @Transactional 옵션을 부여하여 JPA의 변경 감지 및 불필요한 flush를 방지, 성능 향상 달성.

### 3️⃣ 도메인 기반 예외 처리 및 입력값 검증 구조화

* **표준화된 에러 응답 체계** : 백엔드에 ErrorCode Enum을 정의하고 GlobalExceptionHandler를 통해 도서 및 유저 도메인의 예외를 통일된 JSON 포맷으로 프론트엔드에 전달

* **계층별 2중 검증** : 프론트엔드 단에서의 필수 입력 항목 체크 로직에 더해, 백엔드 DTO에 @NotBlank, Controller에 @Valid를 적용하여 잘못된 데이터의 서버 내부 로직 진입 사전 차단

### 4️⃣ GitHub Actions + AWS EC2 기반 CI/CD 구축

* **배포 자동화 파이프라인** : main 브랜치 Push 시 자동 빌드 및 AWS EC2 자동 배포가 이루어지는 Workflow 구축. (배포 소요 시간 약 10분 → 2분으로 단축)

* **운영 안정성 및 보안** : GitHub Secrets를 활용한 SSH Key 및 호스트 IP 은닉, nohup과 pkill을 활용한 무중단 백그라운드 실행 환경 구성

---

### **전체 구현 목록 - Swagger 문서**

<div align="center">
  <img width="800" height="500" alt="Swagger 문서" src="https://github.com/user-attachments/assets/c6018a9d-8a64-4c29-9f0b-643387e2ae6f" />
</div>

### **ERD 다이어그램**

<div align="center">
  <img width="800" height="300" alt="ERD 다이어그램" src="https://github.com/user-attachments/assets/0d7129c2-37aa-4f49-8713-63d390d0aefb" />
</div>

### **배포 아키텍쳐**

<div align="center">
  <img width="480" height="500" alt="배포 아키텍쳐" src="https://github.com/user-attachments/assets/94dc88f7-e457-40cc-930d-6742318bf9dc" />
</div>

# 👨‍💻 Contributors
* **김대호 (PM / FE)** : 요구사항 명세, 화면 흐름도 및 와이어프레임 설계, MUI 기반 UI 레이아웃 및 디자인 구체화, 프론트-백엔드 데이터 전달 방식 조율
* **김효성 (FE)** : React+Vite 초기 환경 및 라우팅 구축, 로그인 폼 및 개인 서재 데이터 필터링 구현, REST API 통신 모듈 전환 및 연동 에러 분석/디버깅
* **신준섭 (FE)** : 프론트-백엔드 API 연동 총괄, Swagger 기준 DTO 1:1 필드 매핑 리팩터링, CORS 및 Request Payload 통신 오류 단계적 추적 및 해결
* **이시연 (FE)** : 도서 등록/수정 페이지 중심 UI 구현, 기본 입력 폼 검증 로직 작성, AI 표지 데이터 렌더링 흐름 분석 및 개선 논의 참여
* **유대영 · 정현호 (AI 연동 / BE)** : DALL-E 3 API 호출 handleGenerateCover 통합 로직 설계 및 프론트 UI 매핑, User 도메인 모델 설계 및 CRUD 로직 공동 구현
* **서범수 (BE)** : GitHub Actions 및 AWS EC2를 활용한 CI/CD 자동화 구축, Global Exception 구조화 설계, JWT 기반 인증/인가 인프라 구현
* **장혁준 (BE)** : Book 도메인 설계 및 핵심 CRUD 비즈니스 로직 개발, 서비스 레이어 트랜잭션 고도화, 도서 검색 및 표지 이미지 URL 저장 API 구현






