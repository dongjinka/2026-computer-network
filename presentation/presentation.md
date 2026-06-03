---
marp: true
theme: dtn
paginate: true
header: 'DTN · Delay/Disruption Tolerant Networking'
footer: 'Computer Networks · 2026'
---

<!-- _class: lead -->
<!-- _paginate: false -->
<p class="eyebrow">Computer Networks · 2026</p>

# DTN
### Delay/Disruption Tolerant Networking — A network built to survive delay and disruption

<span class="pill">Team presentation</span> &nbsp; <span class="pill">15 min</span>

<!--
Speaker notes: Hi everyone. Before I even tell you our topic, a quick question. Have you ever tried to send a text in an elevator, watched it fail, and then the moment your signal came back, it suddenly sent? Most of us have. Hold onto that — that tiny everyday glitch is the doorway into everything we'll talk about today. So let's start there.
-->

---

<!-- _class: lead -->
<p class="eyebrow">00 — Framing the problem</p>

# When your text won't send in an elevator

### Does that message disappear, or does it wait?

<!--
Speaker notes: So why does that happen? The message was never actually lost. The instant you lost signal, your phone just held onto it and fired it off the moment the connection came back. You've probably seen the dramatic version after a disaster — a whole stack of texts landing at once, hours late. Same mechanism: store it, carry it, send it when you can. That simple instinct — if you're cut off, don't give up, just hold it and forward it later — has a name, and a whole field of networking is built on it. That's our topic: DTN, Delay-Tolerant Networking. Our team dug into how you make this work not just across an elevator, but across delays and outages so severe the ordinary internet flat-out quits. And to feel how severe that gets, let's push the idea to its limit.
-->

---

<!-- _class: lead -->
<p class="eyebrow">00 — Framing the problem</p>

## And the extreme case — why is there no "internet" for a Mars rover?

### Why space? It's the most extreme case — crack it and disaster zones, deep sea, and remote areas follow. And it's where DTN is already built and flown (NASA JPL · CCSDS).

<!--
Speaker notes: An elevator cuts you off for a few seconds. Now crank that same situation all the way up: a rover on Mars. The signal one-way takes minutes, and the link stays broken for hours at a time as the planets turn and orbits drift. And space isn't a random pick — it's the hardest case there is, and it's exactly where DTN is already built, standardized, and flying today, run by NASA and the CCSDS. So if we can crack the Mars gap, easier cases like the deep sea or a disaster zone come almost for free. Here's the catch that makes Mars so brutal: the TCP/IP we use every day quietly assumes there's an unbroken path between sender and receiver right now. On Mars, that assumption just falls apart. So today's talk is really that elevator glitch — blown up to the size of the solar system. Let me show you how we'll get there.
-->

---

<p class="eyebrow">Roadmap — the journey of a single piece of data</p>

## Today's map: how one bundle travels from Earth to Mars

| Stage | Question | The answering technology |
|---|---|---|
| ① Problem | Why does TCP/IP break | long delay · disruption |
| ② Store & carry | What if the path won't open | `Store-Carry-Forward` |
| ③ Responsibility | Who is responsible end to end | `Custody` |
| ④ Transport | What carries the space hop | `LTP` |
| ⑤ Path | Which route, and when | `CGR` / opportunistic routing |

> The stage number at the top of each slide marks "you are here." At the end, our own demo confirms ①–⑤ all at once.

<!--
Speaker notes: Here's the map. We split the journey of one piece of data — one "bundle" — from Earth to Mars into five stages: the problem, store-and-carry, responsibility, transport, and the path. For each stage we paired the question it asks with the technology that answers it. From here on, the number at the top of each slide tells you which stage we're in, and at the very end we'll confirm all five at once with a demo we ran ourselves. So let's start at stage one — why TCP/IP breaks out here in the first place.
-->

---

<p class="eyebrow">01 · Stage ① Problem — background</p>

## Extreme / long delay & disruption

- **Long, variable delay** — Earth–Mars one-way ~`3–22 min` (RTT ~`6–44 min`); the short, stable RTT TCP expects is simply impossible [1]
- **Intermittent connectivity → network partitions** — orbiting/moving nodes drop links often; a full end-to-end path may never exist at any single instant
- **Link asymmetry** — uplink and downlink capacities differ sharply
- **High physical loss** — bit errors from cosmic radiation / RF interference — *not* congestion

> Key point: no guarantee that "the entire path is alive at the same time." And it's not just space — disaster zones, deep sea, and remote areas share the same constraints.

<!--
Speaker notes: So why does it break? Space isn't just "slow internet" — it breaks the physical assumptions TCP was built on. Four things are going on. First, long and variable delay: Earth to Mars one-way is about 3 to 22 minutes, so round-trip is 6 to 44 minutes. The short, stable RTT TCP counts on just doesn't exist. Second, links drop constantly as nodes orbit, so a full end-to-end path might never exist at any single moment — that's a network partition. Third, uplink and downlink differ wildly in capacity. And fourth — keep this one in mind — packet loss out here is physical: cosmic radiation, interference. It's not congestion. And these same four conditions are exactly what you find in disaster zones, the deep sea, and remote areas — so anything that survives space carries straight over to them. Now the key move: each of these four realities collides head-on with something TCP takes for granted. Let's line them up.
-->

---

<p class="eyebrow">02 · Stage ① Problem — problem definition</p>

## Where TCP/IP breaks — its 4 assumptions collapse

| TCP/IP assumes… | …but on Mars |
|---|---|
| ① A continuous end-to-end path | Paths never open all at once → even the 3-way handshake can't complete |
| ② Short, stable RTT (timely ACK) | Minute-scale RTT → ACKs too late to grow the congestion window (BDP balloons) → poor utilization |
| ③ Timeout means loss → retransmit | Late ACKs re-send data that was actually fine → wastes scarce link bandwidth |
| ④ Loss means congestion → slow down | A radiation bit-flip is misread as congestion → TCP backs off exactly when it shouldn't |

> The irony: in space TCP slows itself down for the wrong reasons → throughput collapses. We don't need a *faster* TCP — we need a *different premise*.

<!--
Speaker notes: So here's the line-up. TCP rests on four assumptions, and in space every one collapses — each against one of the realities we just listed. One: it assumes a continuous end-to-end path, but paths never open all at once, so even the 3-way handshake can't finish — the connection never forms. Two: it assumes a short, stable RTT so ACKs come back fast, but at minute-scale the ACKs arrive far too late, the congestion window can't grow, and utilization stays terrible with so much data left in flight un-acknowledged. Three: it treats a timeout as loss and retransmits, but here the data was fine, just slow — so TCP re-sends perfectly good data and burns scarce bandwidth. And four, the deep one: TCP treats any loss as congestion and slows down. But in space a bit just got flipped by radiation; nothing is congested. So TCP backs off at exactly the wrong moment — and throughput collapses. The takeaway isn't "build a faster TCP" — we need a fundamentally different premise. And the simplest version of that premise is something you already do every day.
-->

---

<p class="eyebrow">03 · Stage ②③ Store, carry, responsibility — core idea</p>

## "Why not just store it and send later?" — fixing that naive answer step by step

> The naive answer — if the path won't open, a node holds the data and sends it when it does → this is `Store-Carry-Forward`. The whole path doesn't have to open at once.

> But the gap: "If the node holding the data dies, who is responsible?" → `Custody Transfer` — the receiving node assumes delivery responsibility and guarantees retransmission (the handoff of responsibility in registered mail).

- Everyday analogy: a courier depot relay — once the next depot *signs off* on the parcel, responsibility is theirs from that point. The key that separates this from plain storage (caching) is this "sign-off"
- The remaining gap, "which route? when does it open?", is answered in Stage ⑤ Routing

<!--
Speaker notes: That different premise starts from the naive answer, and I'll say it out loud on purpose. "If you're cut off, just hold it and send it later, right?" And yeah — that's basically store-carry-forward. It's exactly what your phone did in the elevator: held the message, then forwarded it the moment the link came back — or as NASA puts it, only the next hop needs to be available. But a gap shows up right away. If the node holding the data powers off or dies, who's responsible now? The classic answer is custody transfer: a node formally takes on delivery responsibility — including retransmission — and hands it forward, node by node. Think of a courier depot relay: once the next depot signs off on the parcel, it's their responsibility from that point on. That sign-off is exactly what separates this from plain caching. One honest footnote: this custody mechanism was a core feature in the older Bundle Protocol v6; the current v7 moved it out of the core and leaves reliable handoff to a separate extension. That's a spec-level change, though — not "custody is gone": ION still implements custody transfer as an option — in BPv7 that's the Bundle-in-Bundle Encapsulation, or BIBE, extension — so that's exactly why you'll see custody OK in our demo log later. The idea — responsibility carries forward — is what matters. There's still one gap left, which route and when it opens, and we'll fill that in at routing. Filling these gaps one at a time is really what DTN design is — so let's see where they live in the stack.
-->

---

<p class="eyebrow">04 · Stage ②③ Store, carry, responsibility — architecture</p>

## Bundle Protocol — one overlay layer

```
  Sender                                        Receiver
  [App]                                         [App]
    │ Bundle                                       ▲
    ▼                                              │
  [Bundle Layer] ── store-carry-forward ─────▶ [Bundle Layer]
    │ CLA = LTP                                    ▲ CLA = TCP
    ▼     ~ light-minutes · disrupted segment ~    │
  [deep-space link] ··· (delivered when a contact opens) ··· [terrestrial internet]
```

- Bundle Protocol v7 = `RFC 9171` (2022) [2] · architecture `RFC 4838` [3]
- Heterogeneous underlying networks are joined via a Convergence Layer Adapter (CLA) — TCP / LTP per segment

<!--
Speaker notes: So where do these ideas actually live? DTN doesn't rip out the network you already have — it lays one overlay layer on top, the bundle layer. On the left the sender, on the right the receiver. The unit of data is a bundle, not a packet, and because each segment behaves differently, we swap out the convergence layer per segment — TCP on the terrestrial side, LTP on the space side. The standard here is BPv7, RFC 9171, formalized in 2022. I just said LTP on the space side, so let's look at why that one exists.
-->

---

<p class="eyebrow">05 · Stage ④ Transport — reliable transport</p>

## LTP — transport built for long-delay links

- Licklider Transmission Protocol = `RFC 5326` [4]
- Splits data into red (reliable · retransmitted) / green (unreliable)
- Defers ACK handling to match RTT → works even under minute-scale delay
- Used as the CLA for Bundle Protocol over deep-space links

> If TCP is a "conversation," LTP is "fire a long stream in one direction and settle up later."

<!--
Speaker notes: Since we can't use TCP on the space segment, that swap on the right needs a real alternative — and that's LTP. The name comes from Licklider, one of the pioneers of the internet. Two key ideas: it splits data into a red part that has to get through and a green part that doesn't, and instead of waiting on ACKs right away, it defers them to match the RTT. That's what makes reliable transport possible even under minute-scale delay. That's four stages down, so let me pause and pull them together before the last one.
-->

---

<p class="eyebrow">Mid-point recap — so what</p>

## So far: filling the gaps gives us a network that "arrives even when disconnected"

- Stage ① Problem — the continuous-path, short-RTT assumptions collapse
- Stage ② `Store-Carry-Forward` — don't wait for the whole path to open
- Stage ③ `Custody` — responsibility carries on even if the holding node dies
- Stage ④ `LTP` — reliable transport on the space segment by deferring ACKs

> So what's the big deal: data eventually arrives *even if an end-to-end connection is never open all at once*. Only one question remains — "which route, and when?"

<!--
Speaker notes: Quick recap before routing. We've been filling the gaps one at a time: the continuous-path assumption collapses, so store-and-carry means we don't have to wait, custody means responsibility carries forward, and LTP gives reliable transport on the space segment. And so what? The big deal is that the data eventually arrives even if the path is never fully open all at once. That leaves just one question — which route, and when do I send — and that's the last stage, routing.
-->

---

<p class="eyebrow">06 · Stage ⑤ Path — routing</p>

## Routing that knows the future vs. routing that bets on encounters

| Approach | Environment | Idea | Cost |
|---|---|---|---|
| `CGR` (Contact Graph Routing) | Space (deterministic) | Precompute when each link will open | High compute · needs a contact plan |
| `Epidemic` | Opportunistic / mobile | Replicate to everyone you meet | High delivery · high overhead |
| `PRoPHET` | Opportunistic | Probabilistic delivery from encounter history | Medium |
| `Spray-and-Wait` | Opportunistic | Bound the replica count to stay in control | Low overhead |

<!--
Speaker notes: So which route, and when? It splits into two branches. Somewhere like space, where orbits are predictable, we use CGR — it precomputes when each link will open, using a contact plan. On the flip side, somewhere like a disaster network, where you have no idea who you'll meet or when, we use opportunistic routing that sprays copies onto whoever you run into. Epidemic sprays to everyone, so delivery is high but overhead is huge, while Spray-and-Wait caps the number of copies to find a balance. And that predictable, contact-plan side isn't hypothetical — it's already flying.
-->

---

<p class="eyebrow">07 · Stage ⑤ Path — implementations & cases</p>

## It's already running in space

- `ION` (NASA JPL) — operational-grade implementation with built-in CGR · LTP · BP [5] · plus DTN2 · μD3TN · HDTN [10]
- `DINET` (2008) — first in-space DTN demonstration, using the Deep Impact spacecraft
- `ISS` — runs a DTN node on the International Space Station [11]
- `Psyche / DSOC` (2023~) — deep-space *optical communications* (physical layer) demonstration; complementary to DTN (upper = BP, lower = optical link) [6]
- `LunaNet / Artemis` — DTN in the lunar communications infrastructure standards
- Terrestrial — flood-prevention IoT via LEO constellations [8], remote/rural internet via train data-mules [9], underwater acoustic, sensor nets (ZebraNet), military tactical

<!--
Speaker notes: And it really is flying — DTN isn't just theory. NASA JPL's ION is the flagship implementation, first proven in space with the 2008 DINET experiment, and today it runs on the ISS. One thing I want to clear up about Psyche's DSOC, launched in 2023: that's an optical-communications demo, so it's physical-layer — it isn't DTN itself. But the two are complementary: bundle protocol on top, optical link underneath. The lunar infrastructure, LunaNet, also builds in DTN. And the same machinery already reaches back down to Earth — disaster networks, underwater links, sensor networks like ZebraNet — which is exactly the payoff of solving the hardest case first. I'll keep that to one line and save the rest of our time for what comes next.
-->

---

<!-- _class: lead -->
<p class="eyebrow">08 — Differentiator ① (here we leave the textbook path)</p>

## We didn't just read about it — we ran it ourselves

### The plan: install the *same software NASA flies*, fake the Mars delay, and watch one message survive a dead link

<span class="pill pill-fill">DEMO (recording secured)</span> &nbsp; <span class="pill">ION-DTN · NASA JPL</span>

> **What is ION?** ION-DTN is NASA JPL's open-source DTN implementation — the same lineage that flies in space — published at `github.com/nasa-jpl/ION-DTN`. Open source means *anyone can reproduce this* — including us, on a laptop.

<!--
Speaker notes: And this is where our team breaks away from the other presentations. Most teams would wrap up right where that case list ended. We went one step further and actually verified those five stages in running code. Here's the plan in plain terms for the next three slides. One: we install ION-DTN, NASA JPL's open-source implementation — the same software family that flies in space — right there on GitHub at nasa-jpl slash ion-dtn. The open-source part is the whole point: this isn't a black box we're asking you to take on faith; anyone can download it and reproduce exactly what we did, and we did it on a regular laptop. Two: we use its built-in tool, owltsim, to fake the Earth-Mars light-speed delay. Three: we send one message across a link that's down and watch it survive and arrive. And one more thing — even if the live demo blows up in front of you, we've dropped an actual execution-log capture on a later slide, so the evidence holds no matter what. Let me show you the shape of the test first.
-->

---

<p class="eyebrow">08 · Differentiator ① — the setup in one picture</p>

## Our whole test, drawn: two nodes, one fake Mars delay

```
  ┌──────────────┐                                   ┌──────────────┐
  │  EARTH node   │ ── owltsim: +600 s one-way ──▶   │   MARS node   │
  │ (ipn:1, sender)│      link DOWN ··· then UP        │(ipn:2, receiver)│
  └──────────────┘                                   └──────────────┘
        │                                                    ▲
        │ t=0  store the bundle ("Hello Mars")               │ t=600 s  forward
        └──────────  ION holds it while the link is down  ───┘
```

- **Two nodes** — smallest setup that still shows an end-to-end hop
- **One injected delay** — owltsim adds a `600 s` one-way light-time (more on it next)
- **Store → forward** — link down, bundle not lost: ION holds it, forwards when it can = Stages ②③ live

<!--
Speaker notes: So here's the whole experiment in one picture before we touch any commands. On the left, one node we're calling "Earth," the sender. On the right, one node we're calling "Mars," the receiver. Two nodes is the smallest setup that still shows a real end-to-end hop. Between them, owltsim injects a 600-second one-way delay — that's the orange arrow — so the Mars side physically can't hear a thing until 600 seconds have gone by. The bottom path is what matters: at time zero we hand a bundle called "Hello Mars" to the Earth node while the link is basically down, ION stores it, holds it, and the moment delivery becomes possible, forwards it on to Mars. That bottom loop — store, hold, forward — is Stages two and three, store-carry-forward and custody, happening in real code instead of on a slide. Keep this picture in your head for the next two slides: one explains what owltsim is, the other shows the actual run. So what exactly is that orange arrow?
-->

---

<p class="eyebrow">08 · Differentiator ① — what owltsim is (and what it is not)</p>

## owltsim — the "Mars delay" knob, honestly bounded

- **What it is** — ION's *One-Way Light Time* simulator: it holds every signal between the two nodes for the configured delay (600 s) before letting it through. "Mars is light-minutes away" becomes a number we dial in on a laptop.
- **Models timing, not physics** — the delay length and the link going down → back up are real; the RF channel, antenna power, and radiation bit-errors are *not* modeled.
- **Not simulated at all** — `Store-Carry-Forward` and `custody` run in ION's *real code*; owltsim only supplies the delay, while ION genuinely stores, holds, and hands off the bundle.

> **The honest line:** the *timing* is real, the *channel* is faked — and we tell you exactly which is which.

<!--
Speaker notes: That orange arrow is owltsim, and before the demo I want to draw its boundary honestly — because the obvious question is coming, "does owltsim really mimic Mars, or is it just calling sleep?", and we'd rather answer it before someone asks. So here's the straight version. What owltsim is: a one-way light-time simulator whose whole job is to take the 600-second delay we set and hold every signal between our two nodes for exactly that long before letting it through. It turns "Mars is light-minutes away" into a single number we dial in on a laptop. What it genuinely models is the timing — how long the delay is, and the link dropping then coming back. What it does NOT model is the radio physics — the real RF channel, the antenna, bit errors from radiation. It's not a physics simulator, and we're not pretending it is. And here's the crucial distinction: the part that actually matters — store-carry-forward and the responsibility handoff — isn't simulated at all. That runs in ION's real code. owltsim just feeds in the delay; ION itself does the real storing, holding, and handing off. Drawing that line is exactly what makes the demo credible: the timing is real, the channel is faked, and we're telling you which is which. So let's run it.
-->

---

<p class="eyebrow">08 · Stage ②③ Store, carry, responsibility — Differentiator ①, run & results</p>

## The run: send into a dead link, and it still arrives — 600 s later

**Setup** — two nodes up, 600 s delay on, receiver (`bpsink`) **first**, then send:

```bash
ionstart -I host1.rc            # sending node
ionstart -I host2.rc            # receiving node
owltsim  owlt.config            # inject one-way light-time delay (owlt=600s)
bpsink   ipn:2.1 &              # receiver first! (order matters)
bpsource ipn:2.1 "Hello Mars"   # send the bundle
```

**Result** — under TCP this dies at `t=0`; under DTN it's stored, then delivered at 600 s:

```
[t=0.0s]    "Hello Mars" stored at intermediate node (link down)
[t=600.1s]  light-time elapsed → forwarded → received "Hello Mars" (custody OK)
```

`custody OK` = the receiver **signed for it** — delivered intact across a dead link.

<!--
Speaker notes: So here's the actual run — expectation versus reality. What we did: bring up the two nodes, switch on the 600-second one-way delay through owlt.config, and — order matters — start the receiver, bpsink, in the background first, then send with bpsource. Receiver-first, because the bundle will be waiting to deliver the moment the delay elapses, so the listener has to already be sitting there. What we expected: ordinary TCP, sent into a down link, dies right away and times out; DTN's promise is "stored now, delivered later." What we actually saw in the log: at time zero, "Hello Mars" is stored at the node because the link is down — the exact moment TCP would give up — and 600 seconds later, when the light-time elapses, ION forwards it and "Hello Mars" lands at the receiver, with a confirmation that the receiver took it. That's the headline: store-carry-forward in real code — stored while disconnected, delivered intact the instant the link returned. Stages two and three, off the slide and into running software. Two quick honesty notes. One: this is a representative run, not a cherry-picked best case, and the log is right there as a static capture — so even if the live demo dies on us, the evidence stays put, and a presenter with a fresh log can just swap it in. Two: under identical disruption, TCP fails and DTN delivers — and that's the living proof I promised earlier. Now let's put exact numbers on that gap.
-->

---

<p class="eyebrow">09 · Stage ①④ — Differentiator ②, quantitative comparison</p>

## TCP vs DTN, by the numbers

| Earth→Mars (one-way 12 min `assumed`) | TCP/IP | DTN (BP+LTP) |
|---|---|---|
| Connection setup | 1.5 RTT ≈ 36 min (12 min × 3 one-way trips); if disrupted in between, session dies | No connection needed |
| On link disruption | Session drops · restart from scratch | Store and resume |
| Transport model | End-to-end, real time | Per-hop, asynchronous |
| Demo result (owltsim) | Timeout — delivery fails | 100% delivery after delay |

> Assumptions: RTT = 2 × 12 min = 24 min; 3-way handshake = 1.5 RTT ≈ 36 min (when one-way is 12 min).
> Conclusion: not "it's slow," but "the design premise is different."

<!--
Speaker notes: So the second differentiator turns that one log line into a full table of numbers. First, to be clear, 12 minutes is an assumed value — it ranges from 3 to 22 minutes depending on distance, so we just took a middle figure. At 12 minutes one-way, the round-trip RTT is 24 minutes, and a 3-way handshake is 1.5 RTTs — three one-way trips — about 36 minutes. And if the link breaks anywhere in there, TCP's session dies, starts over from scratch, and eventually just times out. One row flags the bandwidth-delay product blowing up — I've got a backup slide that breaks that number down if it comes up. But the bottom row is the key one: the very owltsim demo we just saw — under the same disruption, TCP fails to deliver while DTN delivers 100% after the delay. So the demo and this table lock together on one screen. That's why "DTN is faster" is the wrong framing — "the design premise is different" is the accurate one. And if that BDP row caught your eye, I've got a quick breakdown ready.
-->

---

<p class="eyebrow">09 — Aside: why BDP explodes</p>

## Bandwidth-delay product (BDP) — too much data "in flight"

- BDP = bandwidth × RTT. e.g. `1 Mbps × 1440 s (24 min round trip) ≈ 180 MB` simultaneously "in transit"
- TCP's window can't keep this much un-ACKed in flight → window-based flow & congestion control becomes meaningless
- DTN stores per hop, so it never puts everything "in flight" at once

<!--
Speaker notes: So this is that backup card. BDP — bandwidth-delay product — is just bandwidth times round-trip delay. At 1 Mbps with a 24-minute round trip, you've got about 180 MB flying across the line at any given instant. TCP can't keep that much un-acknowledged sitting in its window, so flow control is basically paralyzed. DTN stores per hop, so it never gets into that "everything in flight at once" state. Again, I'll only pull this up if a BDP question comes up — otherwise we go straight to where you should not use DTN.
-->

---

<p class="eyebrow">10 — Differentiator ③, decision tree</p>

## Critically — limitations and "when not to use it"

- Storage burden — intermediate nodes hold bundles for long periods and in bulk → buffer and discard-policy problems
- Overhead — bundle headers · fragmentation/reassembly complexity
- Immature congestion control · difficult security key management (BPSec `RFC 9172`) [7]

```
No continuous-path guarantee  +  large delay/disruption   →   DTN
Otherwise (stable, low-latency like the terrestrial internet)  →   TCP/IP (DTN is a net loss)
```

> A good presentation doesn't claim it's "a cure-all" — it knows when not to use it.

<!--
Speaker notes: So the third differentiator is being honest about the limitations. DTN is not a cure-all. Intermediate nodes hold data for a long time, so the storage burden is heavy, and congestion control is still pretty immature. The rule of thumb is simple: if there's no guaranteed continuous path and the delay or disruption is large, use DTN; if it's stable and low-latency like the regular internet, just use TCP/IP — DTN there is a net loss. Knowing where to use it and, just as importantly, where not to — that's what real understanding looks like. So let me bring the whole thing home in one sentence.
-->

---

<p class="eyebrow">11 — Wrap-up (recovering Stages ①–⑤)</p>

## In one sentence

> If the internet assumes "a path that is open now,"
> DTN assumes "a path that will open someday."

- What we did — started from the elevator scene and followed ① Problem → ② Store & carry → ③ Responsibility → ④ Transport → ⑤ Path, then verified it by running ION-DTN ourselves
- Core insight — thanks to `Store-Carry-Forward` + `Custody`, data arrives even when the path is never open all at once; not "it's slow" but "the premise is different"
- Looking ahead — operational in space (ION · ISS · LunaNet), with ongoing research extending to terrestrial and 6G non-terrestrial networks

<span class="pill">Thank you — questions welcome</span>

<!--
Speaker notes: So, one sentence: the internet assumes a path that's open now, and DTN assumes a path that'll open someday. What we did today was start from that elevator glitch, walk through the five stages, and then verify them by running ION-DTN ourselves. The core insight is that thanks to store-carry-forward and the responsibility handoff, data arrives even when the path is never open all at once — so it's not that it's slow, it's that the premise is different. And looking ahead, it's already operational in space, with research pushing it into terrestrial and 6G non-terrestrial networks too. So the next time your text fails in an elevator and lands a second later, you'll know there's a whole interplanetary network built on that same instinct. Thank you — happy to take questions.
-->

---

<p class="eyebrow">References</p>

## Sources

<style scoped>section{font-size:20px} li{margin:.15em 0}</style>

- [1] NASA Mars Fact Sheet / JPL — Earth–Mars one-way light-time (~3–22 min)
- [2] `RFC 9171` — Bundle Protocol Version 7 (IETF, 2022)
- [3] `RFC 4838` — Delay-Tolerant Networking Architecture (IRTF)
- [4] `RFC 5326` — Licklider Transmission Protocol (LTP)
- [5] NASA JPL — Interplanetary Overlay Network (ION-DTN), open-source · github.com/nasa-jpl/ION-DTN
- [6] NASA — Psyche / Deep Space Optical Communications (DSOC), 2023~
- [7] `RFC 9172` — Bundle Protocol Security (BPSec)
- [8] Marin-de-Yzaguirre et al. — DTN for IoT LEO constellations (flood prevention) · *Acta Astronautica* 225 (2024)
- [9] E. M. Husni — DTN internet for remote areas via train systems · *Proc. IEEE ICON* (2011)
- [10] Douglass et al. — Fountain Code for High-Rate DTN (HDTN) · *IEEE Access* 11 (2023)
- [11] K. Schauer — Delay/Disruption Tolerant Networking Overview · NASA (2023)

<!--
Speaker notes: (Breeze through the references, or pull them up as backup during Q&A.)
-->
