# swstack — 사회복지 실천 에이전트 스킬

> Claude Code를 사회복지 기관의 가상 팀으로 만드는 역할 기반 AI 스킬 시스템

[gstack](https://github.com/garrytan/gstack) (YC CEO Garry Tan)의 "가상 엔지니어링 조직" 구조를 사회복지 실천 현장에 맞게 재설계했습니다.

## 한눈에 보기

```
접수 → 사정 → 계획 → 개입 → 평가 → 종결
```

사회복지 사례관리 표준 프로세스를 슬래시 커맨드 스킬 체인으로 구현합니다.
각 스킬은 이전 단계의 산출물을 자동으로 읽어 다음 단계에 연결합니다.

## 스킬 목록 (22개)

### 사례관리
| 스킬 | 역할 | 설명 |
|------|------|------|
| `/intake` | 접수상담원 | 초기 접수, 긴급도 분류(위기/긴급/일반), 동의서 체크 |
| `/assessment` | 사례관리자 | 6개 영역 욕구사정, 생태도, 강점사정, 위험요인 스크리닝 |
| `/plan-case` | 슈퍼바이저 | SMART 목표 수립, 서비스 연계 계획, 역할 분담 |
| `/intervene` | 실천가 | 회기별 상담기록, 목표 진행률 추적 |
| `/case-conference` | 회의 진행자 | 사례회의 안건 구성, 회의록, 결정사항 추적 |
| `/evaluate` | 평가자 | GAS 목표달성척도, 사전-사후 비교, 종결/연장 판정 |
| `/close-case` | 종결담당 | 종결 요약서, 사후관리 계획 |

### 회계
| 스킬 | 설명 |
|------|------|
| `/budget` | 사업별 예산 편성, 보조금 항목 기준, 집행률 모니터링 |
| `/expense` | 품의서 작성, 증빙 체크리스트, 정산서 생성 |
| `/audit-prep` | 감사 체크리스트, 증빙 누락 확인, 보조금 정산보고서 |

### 홍보
| 스킬 | 설명 |
|------|------|
| `/pr-content` | SNS 게시글, 카드뉴스, 보도자료 (낙인표현 자동 필터링) |
| `/pr-report` | 후원자 감사서한, 후원금 사용 보고서, 활동보고서 |
| `/event-plan` | 행사 기획안, 예산, 인력배치, D-Day 체크리스트 |

### 행정/보고
| 스킬 | 설명 |
|------|------|
| `/report-gov` | 행복e음 실적 정리, 분기/연간 보고서 |
| `/proposal` | 공모사업 제안서 (논리모델, 성과지표, 평가계획 포함) |
| `/minutes` | 회의록 작성, 결정사항 추출, 후속 조치 추적 |

### 슈퍼비전
| 스킬 | 설명 |
|------|------|
| `/supervise` | 사례 슈퍼비전, 윤리 딜레마 분석, 소진 체크 |
| `/retro-welfare` | 월간/분기 KPT 사업 회고 |

### 유틸리티
| 스킬 | 설명 |
|------|------|
| `/privacy-guard` | 개인정보 마스킹, 동의서 점검, 낙인표현 검사 (모든 스킬에서 자동 활성화) |
| `/law-check` | 의무신고 판단, 관련 법령 검토, 윤리강령 안내 |
| `/template` | 기관 양식 관리 (접수서, 사정지, 계획서 등) |
| `/resource-map` | 지역사회 자원 DB 검색, 연계기관 관리 |

## 설치

```bash
git clone https://github.com/myjun090-spec/swstack.git ~/.claude/skills/swstack
```

또는 프로젝트 내부에 설치:

```bash
git clone https://github.com/myjun090-spec/swstack.git .claude/skills/swstack
```

## 사용법

Claude Code에서 슬래시 커맨드로 실행합니다:

```
/intake
→ "70대 여성, 독거, 주민센터 의뢰. 낙상 후 거동 불편, 식사 못함"

/assessment
→ 접수기록 자동 참조 → 영역별 사정 → 생태도 + 욕구우선순위 생성

/plan-case
→ 사정 결과 기반 → SMART 목표 + 서비스 연계 계획 생성

/proposal
→ "복지부 공모, 독거노인 돌봄 사업, 5천만원" → 제안서 초안 생성

/budget
→ "경로식당 사업 시보조금 3천만원" → 관-항-목 예산표 생성
```

## 산출물 흐름

스킬 간 파일이 자동으로 연결됩니다:

```
/intake    → .swstack/cases/{id}/intake.md
                ↓ (자동 참조)
/assessment → .swstack/cases/{id}/assessment.md
                ↓
/plan-case  → .swstack/cases/{id}/plan.md
                ↓
/intervene  → .swstack/cases/{id}/notes/2026-03-24.md
                ↓
/evaluate   → .swstack/cases/{id}/evaluation.md
                ↓
/close-case → .swstack/cases/{id}/closing.md
```

## 핵심 원칙 (ETHOS.md)

1. **클라이언트 중심** — AI는 판단을 대신하지 않으며, 실천가의 전문적 판단을 보조합니다
2. **강점관점** — 문제보다 강점과 자원에 초점을 맞춥니다
3. **개인정보 보호** — 실명/주민번호/주소는 자동 마스킹, 비식별 정보만 기록
4. **근거기반 실천** — 표준화 척도(PHQ-9, GAD-7 등) 우선 사용
5. **AI의 한계** — 위기 상황 감지 시 즉시 실천가에게 알림, 최종 결정권은 사회복지사에게

## 웹앱 버전

Claude Code를 사용하지 못하는 사회복지사를 위한 웹앱 버전도 있습니다:

- **저장소**: [swstack-web](https://github.com/myjun090-spec/swstack-web)
- 브라우저에서 바로 사용 (설치 불필요)
- Google Gemini 무료 API로 사용 가능

## 라이선스

MIT
