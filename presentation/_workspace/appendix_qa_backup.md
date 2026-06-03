# Appendix — Q&A backup (NOT in the presentation deck)

> 발표 슬라이드에서 제외한 Q&A 대비 백업. 필요 시 별도 참고. (원래 부록 A~E)

---

<!-- _class: lead -->
<p class="eyebrow">Appendix — Q&A preparation</p>

## Appendix

<span class="pill">A · SCF≠caching</span> <span class="pill">B · CGR</span> <span class="pill">C · custody vs ACK</span> <span class="pill">D · BPv6 vs v7</span> <span class="pill">E · Epidemic cost</span>

<!--
Speaker notes: From here is the appendix for Q&A preparation. If a question comes up, I'll jump to the relevant slide.
-->

---

<p class="eyebrow">Appendix A</p>

## store-carry-forward ≠ plain caching

- Caching — temporarily holds a copy for performance, with no transfer of responsibility
- SCF — delivery responsibility (custody) itself transfers to the next node + retransmission is guaranteed
- In other words, it's part of routing/reliability, not an accelerator

<!--
Speaker notes: The question "isn't that just caching?" comes up a lot. It isn't. Caching holds a copy briefly with no transfer of responsibility. SCF transfers delivery responsibility itself and even guarantees retransmission.
-->

---

<p class="eyebrow">Appendix B</p>

## How CGR knows the future

- Satellite and planetary orbits are predictable → when each link opens is predefined in a contact plan
- This contact is turned into a graph to compute the path that minimizes arrival time
- So it's "deterministic" routing, not "opportunistic"
- What if orbit prediction has errors? → handled by updating/re-planning the contact plan (periodic operational updates)

<!--
Speaker notes: To the question of how CGR knows the future — because orbits are computable. We build a table in advance of when each link opens, then solve it as a graph to find the shortest-arrival path.
-->

---

<p class="eyebrow">Appendix C</p>

## custody transfer vs TCP ACK

- TCP ACK — end-to-end receipt confirmation; the source is responsible all the way through
- Custody — hands off delivery responsibility per hop; the node that takes custody is responsible for retransmission
- Under long delay/disruption, end-to-end confirmation is unrealistic → split responsibility and hand it off

<!--
Speaker notes: The question of how custody differs from ACK. ACK is end-to-end confirmation, so the source is responsible all the way through, but under minute-scale delay that's unrealistic. Custody splits responsibility per hop and hands it off, so the next node takes on retransmission.
-->

---

<p class="eyebrow">Appendix D</p>

## BPv6 vs BPv7

- `RFC 5050` (BPv6) — custody transfer is a core feature
- `RFC 9171` (BPv7, 2022) — simplified structure; custody is separated out of the core and reliability is delegated to LTP, BPSec, etc.
- Everything in this talk follows the latest standard, BPv7

<!--
Speaker notes: The question of the difference between BPv6 and v7. In v6, custody was a core feature, but v7 simplifies the structure by removing custody from the core and delegating reliability and security to lower/separate layers like LTP or BPSec. Our entire explanation follows the latest BPv7.
-->

---

<p class="eyebrow">Appendix E</p>

## Epidemic's overhead

- Replica count ∝ number of nodes met → high delivery, but wasted buffer and bandwidth
- PRoPHET — selectively replicates based on encounter probability
- Spray-and-Wait — trades off overhead with a replica-count cap (L)

<!--
Speaker notes: The question of Epidemic's overhead. Since it replicates to every node it meets, delivery is high but it uses a lot of buffer and bandwidth. So PRoPHET selects by probability, and Spray-and-Wait caps the replica count to compromise.
-->
