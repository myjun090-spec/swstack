# swstack — 사회복지 실천 에이전트 스킬

사회복지 기관 운영을 위한 역할 기반 AI 에이전트 스킬 시스템입니다.
Claude Code를 사회복지 실천 현장의 가상 팀으로 전환합니다.

## 핵심 원칙

1. **클라이언트 중심**: AI는 판단을 대신하지 않으며, 실천가의 전문적 판단을 보조합니다
2. **개인정보 보호**: 모든 산출물에서 민감정보는 자동 마스킹합니다
3. **근거기반 실천**: 개입 방법 선택 시 근거 수준을 명시합니다
4. **강점관점**: 문제보다 강점과 자원에 초점을 맞춥니다

## 사용 가능한 스킬

### 사례관리 (Case Management)
- `/intake` — 초기 접수상담 기록 작성, 긴급도 분류
- `/assessment` — 욕구사정, 생태도/가계도, 강점사정, 위험요인 스크리닝
- `/plan-case` — 서비스 개입계획 수립, 목표-활동 구조화
- `/intervene` — 개입 기록(프로세스 노트), 회기별 진행상황
- `/case-conference` — 사례회의 안건 구성, 회의록, 결정사항 추적
- `/evaluate` — 사전-사후 비교, 목표달성 척도, 종결/연장 판단
- `/close-case` — 종결 요약서, 사후관리 계획

### 회계 (Accounting)
- `/budget` — 사업별 예산 편성/수정
- `/expense` — 지출 증빙 검토, 정산서 생성
- `/audit-prep` — 감사대비 자료 준비

### 홍보 (PR & Communication)
- `/pr-content` — SNS/카드뉴스/소식지 기획
- `/pr-report` — 후원자 보고서, 활동보고서
- `/event-plan` — 행사 기획안, 체크리스트

### 행정/보고 (Administration)
- `/report-gov` — 행복e음 실적 데이터, 분기/연간 보고서
- `/proposal` — 공모사업 제안서 작성
- `/minutes` — 회의록 작성, 결정사항 추적

### 슈퍼비전 (Supervision)
- `/supervise` — 사례 슈퍼비전, 윤리 딜레마 분석
- `/retro-welfare` — 월간/분기 사업 회고

### 유틸리티
- `/privacy-guard` — 개인정보 마스킹, 동의서 확인
- `/law-check` — 관련 법령 검토
- `/template` — 기관 양식 관리
- `/resource-map` — 지역사회 자원 DB

## 산출물 저장 경로

모든 사례 산출물은 `.swstack/cases/{case-id}/` 아래에 저장됩니다.

```
.swstack/
├── cases/
│   └── {case-id}/
│       ├── intake.md
│       ├── assessment.md
│       ├── plan.md
│       ├── notes/
│       ├── conferences/
│       ├── evaluation.md
│       └── closing.md
├── reports/
├── budgets/
└── pr/
```

## 스킬 체인 (파이프라인)

각 스킬은 이전 단계의 산출물을 자동으로 참조합니다:

```
/intake → /assessment → /plan-case → /intervene(반복) → /evaluate → /close-case
                                          ↑
                                   /case-conference
```

## 프로젝트 설정

- 테스트 명령: `bun test`
- 스킬 문서 생성: `bun run gen:skill-docs`
- 기관명, 지역, 운영 사업 등은 각 스킬이 CLAUDE.md에서 읽거나 사용자에게 물어 기록합니다
