---
name: marp-slide-builder
description: Marp Markdown으로 발표 슬라이드를 제작·변환할 때 사용하는 스킬. Marp frontmatter·슬라이드 구분·테마·발표자 노트·다이어그램·코드 슬라이드·PDF/PPTX/HTML 빌드 명령을 다룬다. 발표 자료, 슬라이드, presentation.md, Marp, PPT 산출을 만들거나 수정·빌드할 때 반드시 이 스킬을 사용할 것.
---

# Marp Slide Builder — Marp 발표 슬라이드 제작 스킬

Marp는 Markdown으로 슬라이드를 쓰고 PDF/PPTX/HTML로 변환하는 도구다. 버전관리·수정이 쉽고 기술 발표에 적합하다. 이 스킬은 빌드 가능하고 청중 친화적인 슬라이드를 만드는 규칙을 정의한다.

## 왜 규칙이 필요한가

Marp 슬라이드의 흔한 실패: (1) frontmatter 누락으로 빌드 실패, (2) 한 슬라이드에 글자 과다로 가독성 붕괴, (3) 발표 멘트를 본문에 욱여넣음, (4) 출처 누락. 규칙은 이걸 막는다. 핵심 원칙: **한 슬라이드 한 메시지, 말할 내용은 발표자 노트로.**

## 필수 문서 구조

파일 맨 위에 YAML frontmatter:

```markdown
---
marp: true
theme: dtn
paginate: true
header: 'DTN · 지연·단절 내성 네트워킹'
footer: '컴퓨터네트워크 발표 · 2026'
---
```

- `theme: dtn` 은 번들된 커스텀 테마(`assets/theme.css`)다 — **xAI(x.ai) 디자인 시스템을 구현**한 다크 테마. 기본 테마면 `default`/`gaia`/`uncover`.
- 슬라이드 구분은 `---`(빈 줄로 감싼 수평선).
- 첫 슬라이드는 제목/발표자/소속.

## 디자인 언어 — xAI 미감 (필수 준수)

이 발표는 xAI 디자인을 따른다. **상세 적용 가이드·작성 패턴은 `references/xai-design.md`를 반드시 읽어라.** 요약:

- **다크 캔버스 전용** (`#0a0a0a`). 라이트 모드·배경 이미지·그라데이션 없음.
- **헤드라인 weight 400 + 음수 트래킹** (테마가 자동 적용). **볼드 금지** — 강조는 크기·트래킹·eyebrow로.
- **Eyebrow**: 모든 콘텐츠 슬라이드 헤드라인 위에 `<p class="eyebrow">대문자 라벨</p>`(JetBrains Mono 대문자 트래킹) — 코드 주석처럼 읽히는 브랜드 시그니처.
- **Pill**: CTA·강조 표식은 `<span class="pill">…</span>` 아웃라인 pill, 채움 변형은 `pill pill-fill`(드물게).
- **카드 = blockquote**(charcoal 8px + hairline, **그림자 없음**). 코드블록·표도 hairline 테두리.
- **악센트(sunset-orange 등)는 드물게** — 다이어그램 포인트·inline code 정도로만.

상세 문법·레이아웃·디렉티브·다이어그램·빌드 트러블슈팅은 `references/marp-syntax.md`를 읽어라.

## 슬라이드 작성 원칙

| 원칙 | 이유 |
|------|------|
| 한 슬라이드 한 메시지 | 인지 부하 관리. 슬라이드 제목이 곧 그 슬라이드의 결론. |
| 본문은 불릿 3~5개, 한 줄 짧게 | 청중은 읽으면서 듣지 못한다. 텍스트는 트리거, 설명은 입으로. |
| 말할 내용은 발표자 노트로 | `<!-- 발표 멘트: ... -->` 주석에. 슬라이드 본문이 대본이 되면 안 됨. |
| 수치·비교는 표 또는 강조 | "3–22분", BDP 비교 등은 표/큰 글씨로 체감시킴. |
| 다이어그램 우선 | 스택 대응, 번들 전달 흐름은 글보다 그림(ASCII/Mermaid/표). |
| 출처는 끝 References 슬라이드 | 본문엔 각주 번호만, 마지막에 모아서. |

## 슬라이드 구성 템플릿 (기술 발표)

1. **제목** — 주제·발표자
2. **후킹/문제 제기** — "왜 화성엔 TCP/IP를 못 쓰나?" (관통 질문)
3. **목차** (선택)
4. **배경** — long/variable delay·단절이 뭔지, 기존 인터넷 가정
5. **문제 심화** — TCP가 깨지는 지점(수치)
6. **핵심 아이디어** — Store-Carry-Forward / Custody (비유)
7. **아키텍처/프로토콜** — 번들층·CLA·LTP, 스택 대응 그림
8. **라우팅** — CGR vs 기회적
9. **사례** — 우주(DINET/ISS/Psyche/LunaNet) + 지상(재난/센서)
10. **[발전] 차별화 섹션** — 정량 비교 / owltsim 데모 / 최신 동향 / 한계 비판
11. **정리** — 한 줄 메시지 재확인
12. **References** — 출처
13. **(부록)** — 시간 초과분, Q&A 대비 심화

## 발표자 노트

각 슬라이드 끝에 HTML 주석으로:

```markdown
<!--
발표 멘트: 여기에 실제 말할 내용을 1인칭 구어체로. 슬라이드에 안 적은 보충 설명, 수치의 의미, 다음 슬라이드로의 연결.
-->
```

Marp는 이 주석을 PPTX 발표자 노트로 내보내고, HTML presenter view에서 보여준다.

## 빌드 명령

프로젝트 루트에서:

```bash
# 권장(커스텀 테마 + 모든 옵션). --no-stdin 필수: 없으면 marp가 stdin 대기로 멈춘다.
npx -y @marp-team/marp-cli presentation.md --pdf --no-stdin \
  --theme-set .claude/skills/marp-slide-builder/assets/theme.css --allow-local-files
# PPTX (편집용) / HTML (브라우저 발표)는 --pdf 자리에 --pptx / --html
```

빌드가 통과해야 산출물이 완성된 것이다. 실패 시 `references/marp-syntax.md`의 트러블슈팅 참조.

## 검증

- frontmatter에 `marp: true`가 있는가.
- 모든 슬라이드가 한 메시지인가, 글자 과다는 없는가.
- 발표자 노트가 슬라이드마다 있는가.
- References 슬라이드에 출처가 모였는가.
- `--pdf` 빌드가 에러 없이 통과하는가.
