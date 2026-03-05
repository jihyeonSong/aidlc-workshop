# 🔧 DevOps - CONSTRUCTION PHASE 프롬프트

## 역할 소개

당신은 테이블오더 서비스 프로젝트의 DevOps 엔지니어입니다.
인프라 설계 검증, CI/CD 파이프라인, 배포 전략을 담당합니다.

## Git Branch

```bash
git checkout -b construction/infra-devops
```

---

## 담당 단계 및 작업

### 1단계: NFR Requirements (Backend) - 리뷰/승인

**브랜치**: `construction/infra-devops`

**체크포인트**:
- [ ] 성능 요구사항(SSE 2초 이내 응답)이 인프라로 실현 가능한가?
- [ ] 보안 요구사항(SECURITY-01~15)이 AWS 서비스로 구현 가능한가?
- [ ] 가용성/확장성 요구사항이 현실적인가?
- [ ] 모니터링/로깅 요구사항이 정의되어 있는가?

**프롬프트 (AI에게 전달)**:
```
나는 DevOps 역할입니다. Backend NFR Requirements를 인프라 관점에서 리뷰합니다.

AI-DLC 워크플로우의 NFR Requirements 단계를 실행해주세요.
대상: Unit 2 (Backend API)

검증 기준:
1. 성능 요구사항이 AWS 인프라로 실현 가능한가?
2. 보안 요구사항(Security Baseline Extension)이 구현 가능한가?
3. SSE 실시간 통신의 인프라 요구사항은?
4. 모니터링/로깅 전략은?

참조:
- aidlc-docs/inception/requirements/requirements.md
- aidlc-docs/inception/application-design/services.md
```

---

### 2단계: NFR Design (Backend) - 리뷰/승인

**체크포인트**:
- [ ] JWT 토큰 관리 패턴이 배포 환경에서 적합한가? (시크릿 관리)
- [ ] SSE 연결 관리가 로드밸런서와 호환되는가?
- [ ] 데이터베이스 연결 풀 설정이 적절한가?
- [ ] 로깅/모니터링 패턴이 운영 환경에 적합한가?

**프롬프트 (AI에게 전달)**:
```
나는 DevOps 역할입니다. Backend NFR Design을 인프라/배포 관점에서 리뷰합니다.

AI-DLC 워크플로우의 NFR Design 단계를 실행해주세요.
대상: Unit 2 (Backend API)

검증 기준:
1. 보안 패턴(JWT 시크릿, bcrypt)이 배포 환경에서 안전한가?
2. SSE가 ALB/NLB와 호환되는가?
3. DB 연결 풀(HikariCP) 설정이 적절한가?
4. 로깅(CloudWatch) 통합이 가능한가?

참조:
- NFR Requirements 산출물 (생성 후 참조)
```

---

### 3단계: Infrastructure Design (Backend) - 리뷰/승인

**체크포인트**:
- [ ] AWS 리소스 선택이 적절한가? (ECS vs Lambda, RDS vs Aurora 등)
- [ ] VPC/서브넷/보안그룹 설계가 보안 요구를 충족하는가?
- [ ] 비용 최적화가 고려되었는가? (MVP 규모)
- [ ] 배포 전략(Blue/Green, Rolling)이 정의되었는가?
- [ ] 환경 분리(dev/staging/prod)가 설계되었는가?

**프롬프트 (AI에게 전달)**:
```
나는 DevOps 역할입니다. Backend Infrastructure Design을 진행해주세요.

AI-DLC 워크플로우의 Infrastructure Design 단계를 실행합니다.
대상: Unit 2 (Backend API)

요구사항:
- AWS 클라우드 배포
- 모놀리스 아키텍처 (Spring Boot + React)
- PostgreSQL 데이터베이스
- SSE 실시간 통신 지원
- MVP 규모 (비용 최적화 우선)

참조:
- aidlc-docs/inception/requirements/requirements.md (제약사항)
- aidlc-docs/inception/application-design/components.md (컴포넌트 구조)
- NFR Design 산출물 (생성 후 참조)
```

---

### 4단계: Code Generation (Backend) - 배포 설정 검증

**체크포인트**:
- [ ] Dockerfile이 적절한가? (멀티스테이지 빌드, 보안)
- [ ] application.yml 환경별 설정이 분리되어 있는가?
- [ ] 환경 변수로 시크릿이 관리되는가? (하드코딩 없음)
- [ ] 헬스체크 엔드포인트가 있는가?

---

### 5단계: Build and Test - CI/CD 검증

**체크포인트**:
- [ ] CI/CD 파이프라인 설정이 적절한가?
- [ ] 빌드 스크립트가 동작하는가?
- [ ] 배포 스크립트가 준비되었는가?
- [ ] 환경별 배포 설정이 분리되어 있는가?

---

## 참조 문서

| 문서 | 경로 | 용도 |
| --- | --- | --- |
| 요구사항 | `aidlc-docs/inception/requirements/requirements.md` | 제약사항/NFR 확인 |
| 컴포넌트 구조 | `aidlc-docs/inception/application-design/components.md` | 배포 대상 확인 |
| 서비스 설계 | `aidlc-docs/inception/application-design/services.md` | SSE/인증 구조 확인 |
| 작업 단위 | `aidlc-docs/inception/application-design/unit-of-work.md` | 역할 매트릭스 |
| 의존성 | `aidlc-docs/inception/application-design/unit-of-work-dependency.md` | 통합 전략 확인 |
