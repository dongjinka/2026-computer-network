# Marp 문법 상세 참조

> marp-slide-architect가 슬라이드를 구현할 때, reviewer가 빌드 검증할 때 참조.

## 목차
1. 슬라이드 구분과 디렉티브
2. 레이아웃 (분할·배경·이미지)
3. 강조·크기 조절
4. 표·코드블록
5. 다이어그램 (Mermaid / ASCII)
6. 발표자 노트
7. 한글 폰트
8. 빌드 트러블슈팅

---

## 1. 슬라이드 구분과 디렉티브

- 슬라이드 구분: 빈 줄 + `---` + 빈 줄.
- **글로벌 디렉티브**(frontmatter, 전체 적용): `marp`, `theme`, `paginate`, `header`, `footer`, `size`.
- **로컬 디렉티브**(특정 슬라이드부터): 슬라이드 안에서 `<!-- _paginate: false -->`(밑줄 = 현재 슬라이드만), `<!-- _class: lead -->`.
- 제목 슬라이드는 보통 `<!-- _class: lead -->` + `<!-- _paginate: false -->`.

```markdown
---
marp: true
theme: default
paginate: true
size: 16:9
---

<!-- _class: lead -->
<!-- _paginate: false -->
# DTN
## 지연·단절 내성 네트워킹
홍길동 · 컴퓨터네트워크 · 2026

---

# 다음 슬라이드
```

## 2. 레이아웃 (분할·배경·이미지)

- **2단 분할**: CSS columns 또는 이미지 배경 활용. 간단히는 표/마크다운으로.
- **배경 이미지**: `![bg](image.png)`, 왼/오른쪽: `![bg left](img.png)`, 크기: `![bg fit](img.png)`, 불투명도: `![bg opacity:.3](img.png)`.
- **인라인 이미지 크기**: `![w:400px](img.png)` 또는 `![h:300px](img.png)`.
- 로컬 이미지를 쓰면 빌드에 `--allow-local-files` 필요.

## 3. 강조·크기 조절

- 굵게 `**텍스트**`, 큰 강조는 별도 슬라이드에 한 줄로.
- 글자 과다 슬라이드는 분할이 정답. 억지로 폰트 줄이지 말 것.
- lead 클래스(`<!-- _class: lead -->`)는 중앙 정렬 큰 제목용.

## 4. 표·코드블록

표 — 수치 비교에 최적:

```markdown
| 항목 | TCP/IP | DTN |
|------|--------|-----|
| 경로 가정 | 연속 end-to-end | 불필요(SCF) |
| RTT 가정 | 짧고 안정 | 길고 가변 OK |
| 화성 연결 | 핸드셰이크 타임아웃 | 번들 저장·전달 |
```

코드블록(데모 명령 등) — 언어 지정으로 하이라이트:

````markdown
```bash
# ION-DTN owltsim: Earth-Mars 편도 지연 시뮬레이션
owltsim config.owlt
```
````

## 5. 다이어그램 (Mermaid / ASCII)

- **Mermaid**: 기본 Marp 코어는 Mermaid를 자동 렌더하지 않는다. 안전한 선택지:
  - (a) ASCII/유니코드 박스 다이어그램을 코드블록으로 — 의존성 없이 항상 빌드됨.
  - (b) 사전에 Mermaid→PNG/SVG로 렌더해 이미지로 삽입.
  - (c) Mermaid 플러그인 사용(환경 의존). **불확실하면 (a)/(b)를 택해 빌드 안정성 우선.**
- 스택 대응·번들 전달 흐름은 ASCII 박스로 충분히 전달 가능:

```
[App]                         [App]
  │ Bundle                      ▲
  ▼                             │
[Bundle Layer] ─store-carry-forward─▶ [Bundle Layer]
  │ CLA(LTPCL)                  ▲ CLA(TCPCL)
  ▼   ~ light-minutes ~         │
[심우주 링크] ··· (contact 열림) ··· [지상망]
```

## 6. 발표자 노트

HTML 주석 `<!-- ... -->`이 발표자 노트가 된다(PPTX 노트·presenter view로 출력). 밑줄 디렉티브(`_class` 등)와 구분하기 위해, 노트는 `발표 멘트:`로 시작하면 가독성이 좋다.

## 7. 한글 폰트 — Pretendard만 사용

- 이 프로젝트의 한글 폰트는 **Pretendard로 고정**(테마 `theme.css`에 설정됨). Latin은 Inter, 한글은 Pretendard.
- 기본은 jsDelivr CDN `@import`로 로드 → 빌드 시 네트워크 필요.
- 오프라인/PDF 임베드가 필요하면 `assets/fonts/PretendardVariable.woff2`를 넣고 테마의 `@font-face` 주석 해제.
- 한글이 깨지면: 네트워크(@import) 확인 또는 로컬 `@font-face`로 전환. 다른 한글 폰트(나눔고딕 등)로 대체하지 말 것(브랜드 폰트 = Pretendard).

## 8. 빌드 트러블슈팅

| 증상 | 원인 | 해결 |
|------|------|------|
| 빌드 무반응/네트워크 오류 | npx가 패키지 처음 받음 | `-y` 플래그, 네트워크 확인. 한 번 받으면 캐시됨 |
| 로컬 이미지 안 나옴 | 권한 | `--allow-local-files` 추가 |
| 한글 깨짐 | 폰트 부재 | OS 한글 폰트 설치 또는 테마 CSS 지정 |
| 테마 적용 안 됨 | theme-set 미지정 | `--theme-set <경로>` 추가 또는 frontmatter theme 이름 일치 |
| Chromium 다운로드 실패 | 샌드박스/네트워크 | `CHROME_PATH` 환경변수로 시스템 크롬 지정 |

빌드 검증은 `--pdf`로 1회 통과를 확인하는 것으로 충분하다.
