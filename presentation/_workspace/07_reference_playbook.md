# 레퍼런스 내러티브 플레이북 — "Understanding Google Search Algorithms"

> 출처: 알고리즘 팀 발표자료_최종.pdf (32p, 고려대 알고리즘 팀플). 똑같이 베끼지 않고, **흐름/내러티브 기법**만 추출해 DTN 발표에 적용한다.

## 추출한 10가지 기법

1. **일상 공감 질문으로 시작** — "What happens when we search on Google?" 누구나 매일 하는 행동. 추상이 아니라 청중의 경험에서 출발.
2. **파이프라인 로드맵을 초반에 제시(스파인)** — Crawling→Processing→Indexing→Ranking→Serving 5단계를 아이콘과 함께 한 장에. 이게 발표 전체를 관통하는 지도가 되어, 청중이 "지금 어디"인지 항상 안다.
3. **섹션 라벨을 로드맵 단계에 묶음** — "Crawling: Gathering Information", "Indexing: ...", "Ranking: ..." 일관된 접두 라벨로 위치 신호.
4. **문제→결함→개선 아크** — TF-IDF 소개 → "Problems of TF-IDF"(포화/길이편향/튜닝불가) → "BM25 improves on TF-IDF". 긴장과 해소. 가장 몰입되는 구간.
5. **"So, what does X mean?" 종합 슬라이드** — 수식 나열 후 멈춰서 의미를 한 번 정리해줌.
6. **추상 개념에 구체적 공감 예시** — RankBrain "black hot drink → coffee", BERT "brazil traveler to usa need a visa". 한 줄 예시로 개념이 꽂힘.
7. **"Whole Process so far" recap/전환 슬라이드** — 번호 매긴 1~5 여정을 다시 보여주고 다음 단계로 전환. 방향 재정렬.
8. **데모 인터리빙 + 정직한 비교** — 각 단계마다 데모를 보여주고, "Google vs Our Demo" 표로 우리가 무엇을 단순화했는지 솔직히. 비판적 사고 어필.
9. **Technical Challenges & Solutions** — 외국어 페이지/중복 URL 같은 실제 삽질을 그 해결과 함께. 진정성·신뢰감.
10. **명확한 3단 결론** — Accomplishments / Key Insights / Future Directions.

## DTN 발표에 줄 핵심 교훈 (현재 v1 덱 진단)

현재 DTN 덱(v1)의 강점: 정확성, 차별화 레버(owltsim 데모/정량/한계), xAI 디자인.
약한 곳(흐름/공감):
- **공감 시작이 약하다** — "화성에 인터넷 안 되는 이유"는 흥미롭지만 청중이 *직접 겪은* 경험은 아니다. 먼저 "끊긴 터널/재난으로 문자 실패" 같은 체험을 거쳐 화성으로 확장하면 공감이 커진다.
- **관통하는 지도(스파인)가 없다** — 개념이 슬라이드마다 점프. "데이터 한 조각이 지구→화성까지 가는 여정" 또는 "DTN 4요소 지도"를 초반에 심고, 섹션마다 "지금 여기" 신호를 주면 따라오기 쉽다.
- **문제→개선 아크를 더 쓸 수 있다** — "그냥 저장했다 보내면 되잖아?" → 문제(누가 책임지나/어느 길로/링크가 언제 열리나) → custody·LTP·CGR이 각 문제의 답. 현재는 기능 나열에 가깝다.
- **recap/전환 부재** — 사례 직전 또는 차별화 직전에 "여기까지 정리" 슬라이드로 방향을 잡아주면 좋다.
- **"so what" 종합 부족** — Bundle/SCF 설명 후 "그래서 이게 왜 대단한가" 한 장.
- **구체 공감 예시 부족** — SCF·custody·CGR에 한 줄 일상 비유 예시.

## 반드시 보존할 것 (재설계 시 사실/자산 불변)
- 모든 RFC 번호(BPv7=9171, LTP=5326, 4838, BPSec=9172, BPv6=5050), 지연 수치(편도 3~22분), 연도(DINET 2008, BPv7 2022, Psyche 2023), 1.5 RTT≈36분.
- owltsim 데모 슬라이드(명령 순서 bpsink& 먼저 + 예상출력), TCP vs DTN 정량표, 한계 결정트리, 부록 A~E.
- xAI 디자인 마크업(eyebrow/pill/blockquote/code), CJK-bold 금지(한글 바로 뒤 `**` 금지), 15분, Pretendard.
