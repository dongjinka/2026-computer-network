# xAI 디자인 시스템 — 발표 적용 가이드

> 출처: x.ai design.md. 본 발표는 이 미감을 `assets/theme.css`(theme: dtn)로 구현한다.
> architect는 이 파일을 읽고 슬라이드를 이 언어로 작성한다.

## 핵심 정체성 (한 줄)

near-black 캔버스 위에 화이트 아웃라인 pill — "연구실이 작업을 발표하는" 절제된 희소함. 그라데이션·배경 이미지·제품 스크린샷 없음.

## 절대 규칙 (Do / Don't)

**Do**
- `#0a0a0a` near-black을 **유일한** 페이지 표면으로. 다크 캔버스 전용.
- 디스플레이 헤드라인은 weight **400** + 음수 트래킹. "정밀함이 곧 목소리".
- 모든 인터랙티브 요소(슬라이드에선 CTA 표식)는 **pill**(9999px) 아웃라인.
- Inter(문장 케이스) + JetBrains Mono **대문자**(eyebrow·라벨·수치 카운터) 페어링.
- 아웃라인 pill 테두리는 **translucent white**(`rgba(255,255,255,0.25)`).

**Don't**
- 라이트 모드 만들지 않기. 다크 전용.
- 디스플레이 헤드라인 **볼드 금지**. weight 400이 전체 스케일.
- 채워진 버튼 남발 금지. 아웃라인 pill이 기본, 화이트 채움 pill은 단 하나(가입)만 예외.
- 카드에 드롭섀도 금지. **hairline 테두리**가 입체감을 담당.
- 음수 트래킹 없이 제네릭 산세리프로 대체 금지(트래킹이 브랜드의 일부).

## 토큰 → 테마 매핑 (이미 theme.css에 구현됨)

| 역할 | 토큰 | 값 | Marp 적용 |
|------|------|-----|-----------|
| 캔버스 | canvas | #0a0a0a | `section` 배경 |
| 카드 | canvas-card | #191919 | `blockquote`, `th` 배경 |
| 코드 목업 | canvas-mid | #363a3f | `pre` 배경 |
| hairline | hairline | #212327 | 테두리·`hr`·표 경계 |
| 본문 | body | #dadbdf | `p`, `li` |
| 뮤트 | body-mid | #7d8187 | header/footer/페이지번호 |
| 잉크 | ink | #ffffff | 헤드라인·기본 텍스트 |
| pill 테두리 | — | rgba(255,255,255,.25) | `.pill` 테두리 |

타이포 사다리(테마 구현): h1 64px(lead 84px) / h2 40px / h3 28px, 모두 weight 400 + 음수 트래킹. eyebrow 14px 모노 대문자 1.4px 트래킹.

악센트(sunset-orange #ff7a17 등)는 **제품 일러스트/아이콘/다이어그램 강조에만 드물게**. 본문 마케팅 표면엔 거의 안 씀 → 슬라이드에선 inline code, `em`, 다이어그램 포인트 정도로만.

## 슬라이드 작성 패턴 (이 테마 전용)

**Eyebrow + 헤드라인** (모든 콘텐츠 슬라이드 권장):
```markdown
<p class="eyebrow">02 — 문제 정의</p>

# 왜 화성엔 TCP/IP를 못 쓰나
```

**CTA / 강조 표식 = pill** (예: 데모 안내, 링크 표식):
```markdown
<span class="pill">ION-DTN owltsim DEMO</span>
<span class="pill pill-fill">핵심 결론</span>
```

**카드 = blockquote**:
```markdown
> **Store-Carry-Forward** — 노드가 번들을 저장·보관하다 contact가 열리면 전달.
> 경로 전체가 동시에 살아있을 필요가 없다.
```

**제목 슬라이드**:
```markdown
<!-- _class: lead -->
<!-- _paginate: false -->
<p class="eyebrow">컴퓨터네트워크 · 2026</p>

# DTN
### 지연·단절 내성 네트워킹 — extreme/long delay 통신

<span class="pill">발표자 홍길동</span>
```

## 한글 처리 (중요)

발표는 한글이다. **한글 글리프는 Pretendard만 사용**한다(Latin은 Inter, 한글은 Pretendard 폴백). 테마 폰트 스택: `'Inter', 'Pretendard Variable', 'Pretendard', sans-serif`. 음수 트래킹이 한글에 과하게 적용되면 자간이 좁아 보일 수 있으니, 헤드라인은 em 단위(테마 기본 -0.025~-0.04em)로 자연스럽게 유지된다.

Pretendard 로딩:
- 기본은 jsDelivr CDN `@import`(테마 상단) — 빌드 시 네트워크가 있으면 동작.
- 오프라인/PDF 임베드 보장이 필요하면 `assets/fonts/`에 `PretendardVariable.woff2`를 넣고 테마의 `@font-face` 주석을 해제한다.

## 빌드

```bash
npx -y @marp-team/marp-cli presentation.md --pdf --no-stdin \
  --theme-set .claude/skills/marp-slide-builder/assets/theme.css --allow-local-files
```
`--no-stdin` 필수(없으면 marp-cli가 입력을 stdin으로 오인해 멈춘다). `@import` 웹폰트(Inter/JetBrains Mono/Pretendard)는 빌드 시 네트워크가 필요하다. 오프라인이면 로컬 `@font-face`(위)로 전환한다.
