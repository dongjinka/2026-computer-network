---
name: dtn-presentation-orchestrator
description: DTN(지연·단절 내성 네트워킹) 발표 자료를 조사·개선·차별화·제작하고 Q&A까지 방어하는 에이전트 팀 오케스트레이터. extreme/long delay 네트워크 통신, DTN 발표/슬라이드/Marp 제작, 팀의 기존 조사 개선·발전, 경쟁 팀 대비 차별화, 발표 후 Q&A·예상 질문 대비 요청 시 사용. "DTN 발표 자료 만들어줘", "발표 자료 개선/보완/업데이트", "슬라이드 다시 만들어줘", "조사 내용 발전시켜줘", "다른 팀과 차별화", "Q&A 대비/예상 질문 뽑아줘", "특정 슬라이드만 수정", "이전 결과 기반으로 다시" 등 초기 제작과 후속 수정 모두 트리거.
---

# DTN 발표 제작 오케스트레이터

DTN 발표 자료를 **조사 → 개선·차별화 → Marp 슬라이드 제작 → 검수 + Q&A 방어**의 워크플로우로 만드는 에이전트 팀을 조율한다. 사용자의 핵심 요구는 "팀의 기존 조사를 더 개선·발전"이며, 추가로 **(1) 같은 토픽 경쟁 팀 대비 차별화로 더 높은 평가, (2) 발표 후 Q&A에서 까다로운 질문 방어**가 목표다. 단순 정리가 아니라 차별화 가치를 더하고 방어 가능한 자료를 만든다.

**발표 시간: 15분 확정** — 본편 10~12장 + 제목/정리/References, 깊은 디테일은 부록 슬라이드로.

## 실행 모드: 에이전트 팀 (하이브리드)

- 기본은 **에이전트 팀**(`TeamCreate` + `TaskCreate` + `SendMessage`).
- 조사 단계는 주제별 **병렬**(여러 dtn-researcher), 제작·검수는 **생성-검증 루프**.
- 데이터 전달: **태스크 기반**(조율) + **파일 기반**(`_workspace/` 산출물) + **메시지 기반**(실시간).

## 프로젝트 경로

- 루트: `~/2026_spring/dtn-presentation/`
- 입력: `_input/` (노션 export Markdown), 보유 자산: `~/2026_spring/ION-DTN/`
- 중간 산출물: `_workspace/` (`{phase}_{agent}_{artifact}` 컨벤션)
- 최종 산출물: `presentation.md` + `presentation.pdf` (루트)

## Phase 0: 컨텍스트 확인 (실행 모드 판별)

워크플로우 시작 시 기존 산출물을 확인해 실행 모드를 정한다:

- `_workspace/`에 산출물 없음 → **초기 실행** (전체 Phase)
- `_workspace/` 있음 + 사용자가 **부분 수정** 요청(예: "라우팅 슬라이드만", "최신 동향 보강") → **부분 재실행** (해당 에이전트만 재호출, 나머지 산출물 재사용)
- `_workspace/` 있음 + 사용자가 **새 입력**(_input 갱신) 제공 → **새 실행** (기존 `_workspace/`를 `_workspace_prev/`로 이동 후 재시작)
- `_input/`이 비어 있으면 → 사용자에게 노션 export 배치를 요청하거나, 동의 시 **신규 조사 모드**로 진행

## 에이전트 팀

| 에이전트 | 타입 | 역할 | 주요 스킬 |
|----------|------|------|-----------|
| research-curator | (정의 파일) | 기존 자료 감사·공백·개선 기회 | dtn-research |
| dtn-researcher | (정의 파일) | 주제별 심층 조사·인용 (병렬) | dtn-research |
| content-pedagogist | (정의 파일) | 학부생용 내러티브·슬라이드 청사진 (15분) | dtn-research |
| marp-slide-architect | (정의 파일) | Marp 슬라이드 구현·빌드 | marp-slide-builder |
| technical-reviewer | **general-purpose** | 정확성·인용·흐름·빌드 검수 | dtn-research |
| qa-defense-strategist | (정의 파일) | 경쟁 차별화 + 적대적 Q&A 방어·하드닝 | presentation-defense, dtn-research |

모든 Agent 호출에 **`model: "opus"`** 를 명시한다. technical-reviewer는 빌드 스크립트 실행이 필요하므로 `general-purpose` 타입을 쓴다(읽기전용 Explore 금지). curator도 차별화 분석을 위해 `presentation-defense` 스킬을 로드한다.

## 워크플로우 (초기 실행)

### Phase 1: 감사 (curator)
- research-curator가 `_input/*.md` + ION-DTN 자산을 dtn-research 지식 맵 기준으로 감사.
- 산출: `_workspace/01_audit.md` (커버리지 맵·공백·**개선 기회**·검증 필요 항목).

### Phase 2: 조사 (dtn-researcher 병렬)
- `01_audit.md`의 우선순위 주제를 dtn-researcher 인스턴스들에게 **분담**(서로 다른 `<topic>`).
- 권장 분담: `problem`, `protocol`(BP+LTP+아키텍처), `routing`, `usecases`, `latest`, `limitations`.
- 각자 `_workspace/02_research_<topic>.md` 산출 (인용·검증 수치·후킹 포인트 포함).
- 병렬 실행으로 시간 단축. 토큰/조율 부담이 크면 주제를 묶어 인스턴스 수를 줄인다.

### Phase 3: 내러티브 설계 (pedagogist)
- content-pedagogist가 모든 `02_research_*.md`를 종합해 학부생용 발표 내러티브 + 슬라이드 청사진 작성.
- 사용자의 "발전" 요구를 후반 하이라이트 섹션으로 명시 배치.
- 산출: `_workspace/03_narrative.md`.

### Phase 4: 슬라이드 제작 (architect) ↔ 이중 검수 루프
- marp-slide-architect가 `03_narrative.md`로 `presentation.md` 작성, Marp 빌드 확인.
- **두 검수자가 병렬로** architect에 피드백(서로 다른 축):
  - **technical-reviewer**: `presentation.md` ↔ `02_research_*.md` 교차 검증 + 빌드 실행 → `_workspace/05_review.md` ("사실이 맞는가").
  - **qa-defense-strategist**: 차별화 강도 점검 + 적대적 Q&A 시뮬레이션 + 슬라이드 하드닝(약한 주장 제거) → `_workspace/06_qa_prep.md` ("평가에서 이기고 Q&A를 방어하는가"). `_input/`의 다른 팀 QA 리스트가 있으면 반영.
- **생성-검증 루프**: 두 판정의 지적(blocker/하드닝)을 architect가 반영 → 재검수. blocker 0 + 의미 있는 개선이 멈출 때까지(최대 3라운드) 반복. 동일 지적 2회 반복 시 리더에 에스컬레이션.

### Phase 5: 종합 보고
- 리더가 최종 `presentation.md`/`presentation.pdf` + `_workspace/06_qa_prep.md`(Q&A 준비 시트)와 변경 요약, 남은 minor 이슈, 빌드 명령을 사용자에게 보고.
- **반드시 정리해 보고**(사용자 핵심 요구): (a) 기존 조사 대비 발전점, (b) 경쟁 팀 대비 차별화 포인트, (c) 예상 Q&A와 답변 준비 상태.

## 데이터 흐름

```
_input/노션export.md ─┐
_input/other-team-qa.md (선택) │
~/ION-DTN/ (자산)     ├─▶ [curator] ─ 01_audit.md ─▶ [researcher×N] ─ 02_research_*.md
                      ┘     (차별화 분석 포함)                       │
                                                                     ▼
                          presentation.pdf ◀─[빌드]─ presentation.md ◀─[architect]─ 03_narrative.md ◀─[pedagogist]
                                                          ▲   ▲           │
                          [technical-reviewer]─ 05_review.md ─┘   │
                          [qa-defense-strategist]─ 06_qa_prep.md ─┘   (이중 검수 루프)
```

## 에러 핸들링

- **에이전트 1회 실패 → 1회 재시도**, 재실패 시 해당 산출물 없이 진행하고 최종 보고에 누락 명시.
- **상충 데이터**: 삭제하지 않고 출처 병기(researcher·reviewer 공통 원칙).
- **_input 비어 있음**: 사용자 확인 후 신규 조사 모드 또는 대기.
- **Marp 빌드 실패**: architect가 frontmatter/문법 수정 2회 시도, 실패 시 로그 첨부하고 Markdown 소스는 보존(부분 산출).
- **검수 루프 무한 방지**: 동일 지적 2회 반복 또는 3라운드 초과 시 리더에게 에스컬레이션, 남은 이슈를 사용자에게 보고.
- **한글 폰트 깨짐**: marp-slide-builder 트러블슈팅 참조(테마 CSS 폰트 지정/OS 폰트 설치).

## 테스트 시나리오

**정상 흐름:** 사용자가 `_input/`에 노션 export(+선택: 다른 팀 QA 리스트) 배치 → "DTN 발표 자료 만들어줘" → Phase 1~5 수행 → `presentation.md`+PDF + `06_qa_prep.md` 산출, 발전점·차별화·Q&A 대비를 요약 보고.

**Q&A 대비 흐름:** 기존 산출물 존재 + "예상 질문 뽑고 슬라이드 약한 부분 다듬어줘" → Phase 0이 부분 재실행 판별 → qa-defense-strategist가 적대적 Q&A 시뮬레이션 + 하드닝 권고 → architect가 약한 주장 수정·부록 슬라이드 추가 → `06_qa_prep.md` 갱신.

**부분 재실행 흐름:** 기존 산출물 존재 + "최신 동향 슬라이드만 더 보강해줘" → Phase 0이 부분 재실행 판별 → dtn-researcher(latest)만 재호출해 `02_research_latest.md` 갱신 → architect가 해당 슬라이드만 수정 → reviewer 재검수.

**에러 흐름:** `_input/` 비어 있음 → 오케스트레이터가 노션 export 배치 요청. 사용자가 "그냥 새로 조사해줘" → 신규 조사 모드로 curator가 지식 맵 전체를 공백으로 처리하고 진행.

## 후속 작업 지원

description에 후속 키워드 포함됨("개선/보완/업데이트/다시/특정 슬라이드만/이전 결과 기반"). 후속 요청 시 Phase 0에서 부분 재실행 여부를 판별하고, 각 에이전트는 이전 산출물을 읽어 해당 부분만 갱신한다.
