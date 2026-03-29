# /knowledge-index — 지식 색인 스킬

> 역할: 지식관리 담당자
> 유형: 지식그래프 (Knowledge Graph)

## 개요

사례 문서, 회의록, 상담기록, 보고서 등을 구조화된 지식 베이스로 색인합니다.
문서에서 엔티티(클라이언트, 서비스, 제공기관, 성과)와 관계를 추출하여 지식그래프 형태의 JSON을 생성합니다.
축적된 지식은 `/knowledge-query`, `/auto-report` 등 다른 스킬에서 활용됩니다.

## 실행 조건

- 색인할 문서가 존재해야 합니다 (파일 경로 또는 텍스트 직접 입력)
- `.swstack/knowledge/` 디렉토리에 색인 결과를 저장합니다

## 워크플로

### Step 1: 문서 수집

사용자에게 다음을 질문합니다:

1. **색인 대상**: 어떤 문서를 색인할 것인지
   - 특정 사례 전체: `.swstack/cases/{case-id}/` 하위 모든 파일
   - 개별 문서: 파일 경로 지정
   - 직접 입력: 텍스트 붙여넣기
2. **문서 유형**: 접수기록 / 사정보고서 / 개입계획서 / 상담기록 / 회의록 / 보고서 / 기타
3. **색인 범위**: 전체 재색인 / 신규 문서만 추가

### Step 2: 엔티티 추출

문서를 읽고 다음 유형의 엔티티를 식별합니다:

| 엔티티 유형 | 추출 대상 | 예시 |
|------------|----------|------|
| 클라이언트 | case_id, 인구통계 특성, 가구 유형 | `CASE-20260301-001`, `70대 여성 독거` |
| 욕구/문제 | 욕구 영역, 구체적 문제 | `경제적 어려움`, `우울 증상 (PHQ-9: 18점)` |
| 강점/자원 | 개인적/사회적/지역사회 강점 | `종교활동 참여`, `자녀 지지체계` |
| 서비스 | 제공된 서비스, 프로그램 | `심리상담 주1회`, `긴급생계지원` |
| 제공기관 | 내부 서비스, 연계기관 | `OO종합사회복지관`, `정신건강복지센터` |
| 개입방법 | 사용된 이론/기법 | `인지행동치료`, `강점관점 상담` |
| 성과 | 목표 달성도, 척도 변화 | `PHQ-9: 18→8`, `GAS +1` |
| 위험요인 | 감지된 위험 | `자살위험 중`, `학대 의심` |
| 결정사항 | 회의/슈퍼비전 결정 | `연장 결정`, `타기관 의뢰` |

### Step 3: 관계 추출

엔티티 간 관계를 식별합니다:

| 관계 유형 | 설명 | 예시 |
|----------|------|------|
| `HAS_NEED` | 클라이언트→욕구 | `CASE-001 → HAS_NEED → 우울 증상` |
| `HAS_STRENGTH` | 클라이언트→강점 | `CASE-001 → HAS_STRENGTH → 종교활동` |
| `RECEIVED_SERVICE` | 클라이언트→서비스 | `CASE-001 → RECEIVED_SERVICE → 심리상담` |
| `PROVIDED_BY` | 서비스→기관 | `심리상담 → PROVIDED_BY → 정신건강복지센터` |
| `USED_METHOD` | 서비스→개입방법 | `심리상담 → USED_METHOD → 인지행동치료` |
| `RESULTED_IN` | 서비스→성과 | `심리상담 → RESULTED_IN → PHQ-9 감소` |
| `RISK_FACTOR` | 클라이언트→위험요인 | `CASE-001 → RISK_FACTOR → 자살위험` |
| `DECIDED` | 회의→결정사항 | `사례회의 0301 → DECIDED → 서비스 연장` |
| `SIMILAR_TO` | 사례↔사례 | `CASE-001 → SIMILAR_TO → CASE-015` |

### Step 4: 유사 사례 태깅

추출된 엔티티 프로필(욕구 영역, 인구통계, 서비스 유형)을 기반으로 기존 색인된 사례와의 유사도를 판단합니다:

- **높은 유사도**: 욕구 영역 + 인구통계 + 가구 유형이 일치
- **중간 유사도**: 욕구 영역 2개 이상 중첩
- **낮은 유사도**: 욕구 영역 1개 중첩

### Step 5: 색인 결과 저장

## 산출물 형식

`.swstack/knowledge/index/{case-id}-index.json`에 저장합니다.

```json
{
  "version": "1.0",
  "indexed_at": "2026-03-29",
  "source": {
    "case_id": "CASE-20260301-001",
    "documents": [
      "intake.md",
      "assessment.md",
      "plan.md"
    ]
  },
  "entities": [
    {
      "id": "E001",
      "type": "client",
      "attributes": {
        "case_id": "CASE-20260301-001",
        "demographics": "70대 여성",
        "household": "독거",
        "region": "OO구"
      }
    },
    {
      "id": "E002",
      "type": "need",
      "attributes": {
        "domain": "심리정서",
        "description": "우울 증상",
        "severity": "중등도-심함",
        "scale": "PHQ-9",
        "score": 18
      }
    },
    {
      "id": "E003",
      "type": "service",
      "attributes": {
        "name": "심리상담",
        "frequency": "주1회",
        "method": "인지행동치료"
      }
    },
    {
      "id": "E004",
      "type": "outcome",
      "attributes": {
        "scale": "PHQ-9",
        "pre_score": 18,
        "post_score": 8,
        "change_rate": "-55.6%",
        "clinically_significant": true
      }
    }
  ],
  "relationships": [
    {
      "from": "E001",
      "relation": "HAS_NEED",
      "to": "E002"
    },
    {
      "from": "E001",
      "relation": "RECEIVED_SERVICE",
      "to": "E003"
    },
    {
      "from": "E003",
      "relation": "RESULTED_IN",
      "to": "E004"
    }
  ],
  "tags": ["독거노인", "우울", "심리상담", "인지행동치료"],
  "similar_cases": [
    {
      "case_id": "CASE-20251105-012",
      "similarity": "high",
      "matching_factors": ["독거노인", "우울", "심리상담"]
    }
  ]
}
```

또한 전체 색인 요약을 `.swstack/knowledge/catalog.json`에 업데이트합니다:

```json
{
  "total_cases_indexed": 45,
  "last_updated": "2026-03-29",
  "entity_counts": {
    "client": 45,
    "need": 128,
    "strength": 87,
    "service": 63,
    "provider": 22,
    "outcome": 41
  },
  "top_needs": ["경제적 어려움", "우울", "사회적 고립", "건강 문제"],
  "top_services": ["심리상담", "긴급생계지원", "돌봄서비스", "자원연계"],
  "cases": [
    {
      "case_id": "CASE-20260301-001",
      "indexed_at": "2026-03-29",
      "tags": ["독거노인", "우울", "심리상담"],
      "document_count": 3
    }
  ]
}
```

## 개인정보 보호 규칙

- 색인에는 case_id만 사용하며, 실명/주민번호/주소/연락처는 절대 포함하지 않습니다
- 인구통계 정보는 성별, 연령대, 가구 유형 수준까지만 기록합니다
- 지역 정보는 구/동 단위까지만 기록합니다
- 색인 결과를 외부에 공유하기 전에 `/privacy-guard`를 실행합니다

## 사용 예시

```
사용자: /knowledge-index
AI: 어떤 문서를 색인하시겠습니까?
    1) 특정 사례 전체 (case-id 입력)
    2) 개별 문서 (파일 경로 입력)
    3) 텍스트 직접 입력

사용자: CASE-20260301-001 사례 전체를 색인해줘

AI: CASE-20260301-001 사례의 문서 3건을 읽었습니다.
    - intake.md (접수기록서)
    - assessment.md (사정보고서)
    - plan.md (개입계획서)

    추출 결과:
    ├── 엔티티 12개 (클라이언트 1, 욕구 4, 강점 3, 서비스 2, 성과 2)
    ├── 관계 15개
    ├── 태그: [독거노인, 우울, 경제적어려움, 심리상담, 긴급생계지원]
    └── 유사 사례: CASE-20251105-012 (높은 유사도)

    색인이 완료되었습니다.
    저장 위치: .swstack/knowledge/index/CASE-20260301-001-index.json
```

## 연결 스킬

- **활용**: `/knowledge-query` — 색인된 지식을 검색
- **활용**: `/auto-report` — 보고서 자동 생성 시 지식 참조
- **보호**: `/privacy-guard` — 색인 결과의 개인정보 점검
