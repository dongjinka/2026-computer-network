# DTN 지식 맵 — 상세 참조

> curator(커버리지 기준), researcher(조사 체크리스트), reviewer(검증 기준)가 공유. 이 파일은 SKILL.md가 트리거될 때 함께 읽는다.

## 목차
1. 문제 정의 — 왜 TCP/IP가 깨지는가
2. 핵심 아이디어 — Store-Carry-Forward & Custody
3. 아키텍처 & 프로토콜 스택
4. 라우팅
5. 구현체
6. 응용 사례
7. 한계·미해결 과제 & 최신 동향
8. 권위 출처 목록
9. 검증 체크리스트
10. 발표 "발전" 포인트 후보

---

## 1. 문제 정의 — 왜 TCP/IP가 깨지는가

지상 인터넷의 암묵적 가정:
- **연속적 end-to-end 경로**가 전송 내내 존재한다.
- **RTT가 짧고 안정적**이다(타임아웃·혼잡제어가 RTT 기반).
- 손실률이 낮고, 양방향 대화(핸드셰이크, ACK)가 빠르게 왕복한다.

extreme/long delay 환경에서 깨지는 지점:
- **TCP 3-way handshake**: 화성까지 편도 ~3–22분이면 연결 수립에만 수십 분. 타임아웃으로 실패.
- **혼잡제어/재전송**: RTT 추정·ACK 클로킹이 분 단위 RTT에서 사실상 마비.
- **경로 단절(intermittent connectivity)**: 위성 가시성, 행성 자전/공전 차폐, 모바일 노드 이동으로 경로가 주기적/비주기적으로 끊김 → "동시에 살아있는 경로"가 아예 없을 수 있음.
- **비대칭·저대역폭, 높은 BER**: 심우주 링크 특성.
- **Bandwidth-Delay Product(BDP)** 폭증: 큰 지연 × 대역 → 거대한 "관 속 데이터", 윈도우 기반 흐름제어 비효율.

발표 후킹: "지구–화성 편도 지연이 평균 ~12.5분(범위 3–22분)일 때, TCP는 연결조차 못 맺는다. 그러면 어떻게 보내지?"

## 2. 핵심 아이디어 — Store-Carry-Forward & Custody

- **Store-Carry-Forward(SCF)**: 노드가 번들을 받아 **저장(store)** 하고, 다음 contact가 생길 때까지 **보관(carry)**, 기회가 오면 **전달(forward)**. 경로 전체가 동시에 연결될 필요가 없다. (비유: 우체국 릴레이/페리. 단, 단순 캐싱과 달리 책임 이전·재전송 의미 포함 — 비유 한계 명시.)
- **Custody Transfer**: 번들 전달 책임을 다음 노드가 인수. 인수한 노드가 재전송 책임을 짐(등기우편 책임 인계 비유). BPv7에서는 BPv6의 custody 개념이 단순화·재정의됨 — 버전 차이 주의.
- **오버레이 구조**: Bundle layer가 기존 transport 위에 얹히는 overlay. 이질적 하위 네트워크(인터넷, 우주 링크)를 번들로 묶음.
- **지연 내성 vs 단절 내성**: Delay-Tolerant(긴 지연 견딤) + Disruption-Tolerant(끊김 견딤). DTN은 둘 다.

## 3. 아키텍처 & 프로토콜 스택

| 계층 | 내용 | 출처 |
|------|------|------|
| 아키텍처 | DTN Architecture, EID, region 개념 | **RFC 4838** |
| Bundle Protocol | 번들 구조, 처리, BPv7 | **RFC 9171**(BPv7, 2022) / RFC 5050(BPv6, deprecated 방향) |
| 보안 | Bundle Security Protocol | **RFC 9172**(BPSec) |
| Convergence Layer | 하위 전송과 번들층 접합 | TCPCL(RFC 9174), UDPCL, LTPCL |
| 신뢰 전송(장지연) | Licklider Transmission Protocol | **RFC 5326**(LTP), RFC 5325(동기) |

핵심 개념:
- **Bundle**: DTN의 PDU. 가변 크기, 메타데이터(EID, 수명 lifetime, 우선순위) 포함.
- **EID(Endpoint ID)**: `ipn:` 또는 `dtn:` 스킴.
- **LTP**: 장지연·단방향 우세 링크용. 데이터를 **red part(신뢰, 재전송)** / **green part(비신뢰)** 로 구분. ARQ를 지연에 맞춰 비동기 처리(deferred). 심우주 링크에서 BP의 CLA로 자주 사용.
- **CLA**: 인터넷 구간은 TCPCL, 우주 구간은 LTPCL처럼 구간별로 다른 CL을 씀.

## 4. 라우팅

- **결정적/스케줄 기반(우주)**: **Contact Graph Routing(CGR)**. 미래 contact(언제 어느 링크가 열리는지)가 예측 가능 → contact plan을 그래프로 만들어 최단 도착 시간 경로 계산. ION이 사용.
- **기회적/모바일(지상·재난·센서)**:
  - **Epidemic**: 만나는 모든 노드에 복제 전파(전달률↑, 오버헤드↑).
  - **PRoPHET**: 만남 이력 기반 전달 확률로 선택적 복제.
  - **Spray-and-Wait**: 복제 수 상한을 두고 뿌린 뒤 대기(오버헤드 제어).
- 비교 축: 전달률, 지연, 복제 오버헤드, 버퍼 사용량.

## 5. 구현체

| 구현 | 주체 | 특징 |
|------|------|------|
| **ION** | NASA JPL | 행성간용 운영급, CGR·LTP·BP, owltsim 포함 (본 프로젝트 보유) |
| DTN2 | DTNRG 레퍼런스 | 초기 레퍼런스 구현 |
| IBR-DTN | TU Braunschweig | 임베디드/모바일 경량 |
| μD3TN | D3TN GmbH | BPv7, 임베디드·우주 |
| HDTN | NASA GRC | 고성능(High-rate) DTN |

## 6. 응용 사례

- **행성간 인터넷(IPN)** / Solar System Internet(SSI) — Vint Cerf 주도 비전.
- **DINET(2008)**: Deep Impact 우주선 활용 첫 우주 DTN 실증.
- **ISS DTN**: 국제우주정거장 운영 DTN 노드.
- **Psyche / DSOC(2023~)**: 심우주 광통신(Deep Space Optical Communications) 실증 — 최신.
- **LunaNet / Artemis**: 달 통신 인프라 표준화에 DTN 적용.
- **지상**: 재난·긴급망, 오지/도서 연결, 수중 음향망, 군사 전술망, VANET(차량), 야생동물/센서 모니터링(ZebraNet 등).

## 7. 한계·미해결 과제 & 최신 동향

한계(비판적 분석 — 발표 깊이를 더함):
- 중간 노드의 **저장 부담**(대용량·장기 보관), 버퍼 관리·폐기 정책.
- 번들 **오버헤드**, 단편화/재조립 복잡성.
- **혼잡 제어**가 미성숙(전통 혼잡제어 가정 부재).
- **보안·키 관리**가 단절·장지연 환경에서 어려움.
- 지상 인터넷에서 채택 저조: 기존 TCP/IP로 충분한 환경에선 이득이 적음.

최신 동향:
- **BPv7(RFC 9171, 2022)** 표준화 안정화.
- **6G 비지상망(NTN)**·LEO 메가컨스텔레이션과 DTN 연계 연구.
- 광통신(DSOC)·LunaNet으로 우주 인터넷 인프라화 가속.
- μD3TN/HDTN 등 새 구현으로 임베디드·고속화.

## 8. 권위 출처 목록

- IETF RFC: 4838, 9171, 5050, 9172, 5326, 5325, 9174 (datatracker.ietf.org / rfc-editor.org).
- IRTF DTNRG / IETF DTN WG 문서.
- CCSDS(우주데이터시스템자문위) 표준 (ccsds.org).
- NASA: JPL ION 문서, NASA DTN 페이지, Psyche/DSOC·LunaNet 보도.
- 보유 PDF: `~/2026_spring/ION-DTN/doc/ION-beginning.pdf`, `ION-LTP-configuration-4.1.3.pdf`, `IOS_4.1.2_BPSec_release_notes.pdf` 등.
- 학술: Fall(2003) "A Delay-Tolerant Network Architecture for Challenged Internets"; Burleigh 등 ION/LTP 논문.

## 9. 검증 체크리스트 (reviewer 필수)

- [ ] RFC 번호: BPv7=9171, LTP=5326, 아키텍처=4838, BPSec=9172, BPv6=5050 — 슬라이드와 일치?
- [ ] Earth–Mars 편도 지연 범위(약 3–22분)로 표기? 단일 숫자면 "평균/근사" 명시?
- [ ] 사례 연도: DINET 2008, BPv7 2022, Psyche 발사 2023 — 정확?
- [ ] BPv6 vs BPv7 custody 차이를 혼동하지 않았는가?
- [ ] CGR(결정적) vs Epidemic/PRoPHET(기회적) 구분이 정확한가?
- [ ] store-carry-forward 비유가 단순 캐싱으로 오도되지 않는가?
- [ ] 모든 핵심 주장에 출처가 있는가?

## 10. 발표 "발전" 포인트 후보 (사용자 핵심 요구)

기존 조사 대비 발전시킬 수 있는 차별화 요소:
1. **TCP vs DTN 정량 비교** — BDP·핸드셰이크 소요시간·타임아웃 실패를 수치/표로.
2. **ION-DTN owltsim 실제 데모** — 보유 자산으로 Earth–Mars 지연을 직접 시뮬레이션해 번들 전달을 보여줌. (가장 강력한 차별점)
3. **최신 동향 보강** — Psyche DSOC, LunaNet/Artemis, 6G NTN 연계, BPv7.
4. **라우팅 정량 비교** — CGR vs Epidemic vs Spray-and-Wait 전달률/오버헤드.
5. **비판적 한계 분석** — 저장 부담·혼잡·보안·지상 채택 저조 이유. (단순 소개를 넘는 깊이)
6. **계층 대응 그림** — TCP/IP 스택 옆에 DTN 스택(번들층+CLA+LTP)을 나란히 시각화.
