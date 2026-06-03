---
name: dtn-research
description: DTN(Delay/Disruption Tolerant Networking)과 extreme/long delay 통신을 조사·검증·설명할 때 사용하는 도메인 지식 스킬. Bundle Protocol(BPv7), LTP, store-carry-forward, custody transfer, Contact Graph Routing, 우주통신/재난망 사례, 최신 동향(Psyche DSOC·LunaNet·μD3TN·6G NTN), DTN 한계까지의 지식 맵과 권위 출처·검증 기준을 제공한다. DTN, 번들 프로토콜, 지연 내성 네트워크, 행성간 인터넷, ION-DTN, 우주 통신 지연을 조사·발표·검증·인용할 때 반드시 이 스킬을 사용할 것.
---

# DTN Research — 지연/단절 내성 네트워킹 조사 스킬

DTN을 조사·설명·검증하는 모든 작업의 공통 기준. curator·researcher·reviewer가 같은 지식 맵과 출처 기준을 공유하여, 누락 없는 커버리지와 정확한 인용을 보장한다.

## 왜 이 스킬이 필요한가

DTN은 "TCP/IP가 깨지는 곳에서 동작하는 네트워크"다. 발표가 흔히 빠지는 함정: (1) 문제의식 없이 기술부터 나열, (2) BPv6/BPv7·DINET 같은 옛 정보 답습, (3) 수치·RFC 번호 오류, (4) "우주에서만 쓴다"는 좁은 시야. 이 스킬은 그 함정을 막는 기준선이다.

## 조사 워크플로우

1. **문제부터.** 왜 기존 TCP/IP가 long/variable delay·intermittent connectivity에서 실패하는지 먼저 확립한다. 이게 발표의 동기다.
2. **지식 맵 7영역**을 기준으로 커버리지를 점검한다(아래). 각 영역에 권위 출처가 붙어야 한다.
3. **1차 출처 우선.** RFC 원문 > IETF/IRTF·CCSDS·NASA JPL 문서 > 학술 논문 > 보조(블로그/위키).
4. **수치·식별자 검증** 후 인용을 단다. 검증 불가는 추측 금지, "확인 필요"로 표시.
5. **최신 동향과 한계**를 반드시 포함한다(발표 차별화의 핵심).

상세 지식 맵·출처 목록·검증 체크리스트는 `references/dtn-knowledge-map.md`를 읽어라(이 스킬이 트리거되면 함께 로드).

## 지식 맵 7영역 (요약)

1. **문제 정의** — long/variable RTT, 단절(intermittent), 비대칭/저대역, 높은 손실. TCP의 가정(연속 end-to-end 경로, RTT 기반 타임아웃/혼잡제어, 핸드셰이크)이 왜 깨지는가. Bandwidth-Delay Product.
2. **핵심 아이디어** — Store-Carry-Forward, Custody Transfer, 오버레이 네트워크, "경로 전체가 동시에 살아있을 필요 없음".
3. **아키텍처 & 프로토콜** — RFC 4838(아키텍처), Bundle Protocol BPv7(RFC 9171, 2022)·BPv6(RFC 5050), 번들/EID, Convergence Layer Adapter(TCPCL/UDPCL/LTPCL), LTP(RFC 5326), BPSec(RFC 9172).
4. **라우팅** — 우주(결정적 contact): Contact Graph Routing(CGR). 기회적/모바일: Epidemic, PRoPHET, Spray-and-Wait.
5. **구현체** — ION(NASA JPL, 보유 중), DTN2, IBR-DTN, μD3TN, HDTN.
6. **사례** — 행성간 인터넷(IPN), DINET(2008), ISS·DTN, Psyche DSOC 광통신(2023~), LunaNet/Artemis, 재난·오지·수중·군사·VANET·센서망.
7. **한계·미해결 & 최신 동향** — 노드 저장 부담, 오버헤드, 혼잡·보안, 지상 채택 저조 이유. 6G 비지상망(NTN)·위성 메가컨스텔레이션 연계, Solar System Internet.

## 검증 기준 (핵심 수치)

- Earth–Mars **편도** 지연: 약 3분(최근접) ~ 22분(최원거리), RTT는 그 2배. "8광분"은 평균 근사치 — 발표엔 범위로 제시 권장.
- 빛의 속도 ≈ 3×10⁸ m/s. 지연 = 거리/광속.
- RFC 번호는 반드시 원문 대조: BPv7=**9171**, LTP=**5326**, 아키텍처=**4838**, BPSec=**9172**, BPv6=**5050**.

> 틀린 RFC 번호·연도·지연 수치는 발표 신뢰도를 즉시 무너뜨린다. references의 체크리스트로 교차 검증하라.

## 보유 자산 활용

- `~/2026_spring/ION-DTN/` — NASA JPL ION 레퍼런스 구현(본 프로젝트가 보유).
- `~/2026_spring/ION-DTN/doc/*.pdf` — ION-beginning, LTP configuration, BPSec 등 **1차 출처**로 인용 가능.
- `~/2026_spring/ION-DTN/owltsim` — **One-Way Light Time 시뮬레이터**. Earth–Mars 광속 지연을 실제로 흉내내는 데모 가능 → 발표의 강력한 "발전" 포인트.

## 인용 형식

본문 주장 옆에 `[RFC 9171 §4]`, `[NASA ION-beginning.pdf p.3]`, `[저자, 연도]` 식으로 표기하고, 문서 끝 출처 목록에 전체 참조를 모은다.
