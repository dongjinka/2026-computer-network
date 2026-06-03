# DTN Presentation — English Script & Flow

> Per-slide spoken script — one continuous talk. In sync with `web/DTN.html` (the HTML deck).

> ~15 min · 23 slides · 6 cited sources (RFC standards + NASA implementations/missions). Solo presenter.

---

## Flow at a glance

| # | Slide | Beat |
|---|-------|------|
| 1 | **Communication Under Extreme Delay & Disruption** | Hook: the elevator text |
| 2 | **What we'll cover** | Roadmap in six steps |
| 3 | **When your text won't send in an elevator** | Why it happens → name it: DTN |
| 4 | **Now scale that glitch to Mars** | The extreme case · why Mars |
| 5 | **One bundle. Earth → Mars. Five stages** | The map (5 stages) |
| 6 | **It's not slow internet — it's broken physics** | Stage ① — four physical realities |
| 7 | **Every TCP assumption breaks out here** | Stage ① — assumptions collapse |
| 8 | **Cut off? Hold it. Then forward it** | Stage ②③ — store-carry-forward + custody |
| 9 | **DTN adds one layer: the bundle layer** | Stage ②③ — architecture (BPv7) |
| 10 | **TCP can't ride the space link. LTP can** | Stage ④ — LTP |
| 11 | **It arrives — even with no end-to-end path** | Mid recap — so what |
| 12 | **Know the future, or bet on who you meet** | Stage ⑤ — routing |
| 13 | **This isn't theory. It's flying** | Stage ⑤ — in space + Earth payoff |
| 14 | **We didn't just read about it. We ran it** | Differentiator ① — the plan |
| 15 | **Two nodes. One fake Mars delay** | Differentiator ① — test in one picture |
| 16 | **owltsim: honest about what it fakes** | Differentiator ① — bound the simulator |
| 17 | **Sent into a dead link — it arrived 600 s later** | Differentiator ① — the run |
| 18 | **TCP vs DTN — by the numbers** | Differentiator ② — quantified |
| 19 | **BDP: too much data "in flight"** | The number underneath |
| 20 | **When NOT to use DTN** | Differentiator ③ — limits & decision rule |
| 21 | **What we proved — and where it stops** | Our results & our demo's limits |
| 22 | **Internet assumes a path open now / DTN someday** | One-sentence wrap |
| 23 | **Sources** | References (skim / Q&A backup) |

---

## Slide-by-slide script

### 1. Communication Under Extreme Delay & Disruption

Hi everyone. Before I even tell you our topic, a quick question. Have you ever tried to send a text in an elevator, watched it fail, and then the moment your signal came back, it suddenly sent? Most of us have. Hold onto that — that tiny everyday glitch is the doorway into everything we'll talk about today. Here's where we're going to take it.

### 2. What we'll cover

Here's what we're going to cover today. We start from that elevator glitch and the problem behind it — why the regular internet, TCP/IP, breaks. Then the core idea that fixes it: store, carry, forward. From there we build up the architecture — the Bundle Protocol and LTP — see how it's routed and where it's already flying, and then the part we're proudest of: a demo we ran ourselves on NASA's DTN software. We close with the numbers, the limits, and a one-sentence takeaway. So let's go back to that elevator.

### 3. When your text won't send in an elevator

So why does that happen? The message was never actually lost. The instant you lost signal, your phone just held onto it and fired it off the moment the connection came back. You've probably seen the dramatic version after a disaster — a whole stack of texts landing at once, hours late. Same mechanism: store it, carry it, send it when you can. That simple instinct — if you're cut off, don't give up, just hold it and forward it later — has a name, and a whole field of networking is built on it. That's our topic: DTN, Delay-Tolerant Networking. Our team dug into how you make this work not just across an elevator, but across delays and outages so severe the ordinary internet flat-out quits. And to feel how severe that gets, let's push the idea to its limit.

### 4. Now scale that glitch to Mars

An elevator cuts you off for a few seconds. Now crank that same situation all the way up: a rover on Mars. The signal one-way takes minutes, and the link stays broken for hours at a time as the planets turn and orbits drift. And space isn't a random pick — it's the hardest case there is, and it's exactly where DTN is already built, standardized, and flying today, run by NASA. So if we can crack the Mars gap, easier cases like the deep sea or a disaster zone come almost for free. Here's the catch that makes Mars so brutal: the TCP/IP we use every day quietly assumes there's an unbroken path between sender and receiver right now. On Mars, that assumption just falls apart. So today's talk is really that elevator glitch — blown up to the size of the solar system. Let me show you how we'll get there.

### 5. One bundle. Earth → Mars. Five stages

Here's the map. We split the journey of one piece of data — one bundle — from Earth to Mars into five stages: the problem, store-and-carry, responsibility, transport, and the path. For each stage we paired the question it asks with the technology that answers it. These five stages are the backbone of the rest of the talk, and at the very end we'll confirm all five at once with a demo we ran ourselves. So let's start at stage one — why TCP/IP breaks out here in the first place.

### 6. It's not slow internet — it's broken physics

So why does it break? Space isn't just slow internet — it breaks the physical assumptions TCP was built on. Four things are going on. First, long and variable delay: Earth to Mars one-way is about 3 to 22 minutes, so round-trip is 6 to 44 minutes. The short, stable RTT TCP counts on just doesn't exist. Second, links drop constantly as nodes orbit, so a full end-to-end path might never exist at any single moment — that's a network partition. Third, uplink and downlink differ wildly in capacity. And fourth — keep this one in mind — packet loss out here is physical: cosmic radiation, interference. It's not congestion. And these same four conditions are exactly what you find in disaster zones, the deep sea, and remote areas — so anything that survives space carries straight over to them. Now the key move: each of these four realities collides head-on with something TCP takes for granted. Let's line them up.

### 7. Every TCP assumption breaks out here

So here's the line-up. TCP rests on four assumptions, and in space every one collapses — each against one of the realities we just listed. One: it assumes a continuous end-to-end path, but paths never open all at once, so even the 3-way handshake can't finish — the connection never forms. Two: it assumes a short, stable RTT so ACKs come back fast, but at minute-scale the ACKs arrive far too late, the congestion window can't grow, and utilization stays terrible with so much data left in flight un-acknowledged. Three: it treats a timeout as loss and retransmits, but here the data was fine, just slow — so TCP re-sends perfectly good data and burns scarce bandwidth. And four, the deep one: TCP treats any loss as congestion and slows down. But in space a bit just got flipped by radiation; nothing is congested. So TCP backs off at exactly the wrong moment — and throughput collapses. The takeaway isn't build a faster TCP — we need a fundamentally different premise. And the simplest version of that premise is something you already do every day.

### 8. Cut off? Hold it. Then forward it

That different premise starts from the naive answer, and I'll say it out loud on purpose. If you're cut off, just hold it and send it later, right? And yeah — that's basically store-carry-forward. It's exactly what your phone did in the elevator: held the message, then forwarded it the moment the link came back. But a gap shows up right away. If the node holding the data powers off or dies, who's responsible now? The classic answer is custody transfer: a node formally takes on delivery responsibility — including retransmission — and hands it forward, node by node. Think of a courier depot relay: once the next depot signs off on the parcel, it's their responsibility from that point on, and the previous node can then free its own copy. That sign-off is exactly what separates this from plain caching. One honest footnote: this custody mechanism was core in the older Bundle Protocol v6; v7 moved it out of the core and leaves reliable handoff to a separate extension — but custody isn't gone, ION still implements it as an option, which is exactly why you'll see custody OK in our demo log later. The idea — responsibility carries forward — is what matters. There's still the question of which route and when, and we'll get to that at routing. But first: where do all these ideas actually live in the stack?

### 9. DTN adds one layer: the bundle layer

So where do these ideas actually live? DTN doesn't rip out the network you already have — it lays one overlay layer on top, the bundle layer. On the left the sender, on the right the receiver. The unit of data is a bundle, not a packet — and a bundle carries its own metadata, where it's headed and when it expires, so any node can handle it on its own without a live session. Nodes are named with URI-like Endpoint IDs, not IP addresses, so a node keeps its identity even as it disconnects and moves. And because each segment of the journey behaves differently, a thin adapter — the convergence layer adapter, or CLA — bridges the bundle layer to whatever runs on each segment: a TCP convergence layer on the terrestrial side, LTP on the space side. The standard here is BPv7, RFC 9171, formalized in 2022. I just said LTP on the space side, so let's look at why that one exists.

### 10. TCP can't ride the space link. LTP can

Since we can't use TCP on the space segment, that swap on the right needs a real alternative — and that's LTP. The name comes from Licklider, one of the pioneers of the internet. Two key ideas. First, it splits data into a red part that must arrive and a green part that can drop — and for the red part, retransmission is selective: only the segments actually lost are sent again, not the whole stream. Second, instead of waiting on ACKs immediately, it defers them to match the RTT — and it sends a large block as segments, reassembling the reliable part at the far end. Together, that's what makes reliable transport possible even under minute-scale delay. That's four stages down, so let me pause and pull them together before the last one.

### 11. It arrives — even with no end-to-end path

Quick recap before routing. We've been filling the gaps one at a time: the continuous-path assumption collapses, so store-and-carry means we don't have to wait, custody means responsibility carries forward, and LTP gives reliable transport on the space segment. And so what? The big deal is that the data eventually arrives even if the path is never fully open all at once. That leaves just one question — which route, and when do I send — and that's the last stage, routing.

### 12. Know the future, or bet on who you meet

So which route, and when? It splits into two branches. Somewhere like space, where orbits are predictable, we use CGR, Contact Graph Routing — from the known schedule it builds a contact graph and runs a Dijkstra-like search over it, precomputing when each link opens and picking a route that still meets the deadline. On the flip side, somewhere like a disaster network, where you have no idea who you'll meet or when, we use opportunistic routing that sprays copies onto whoever you run into. Epidemic sprays to everyone, so delivery is high but overhead is huge, while Spray-and-Wait caps the number of copies to find a balance. And that predictable, contact-plan side isn't hypothetical — it's already flying.

### 13. This isn't theory. It's flying

And it really is flying — DTN isn't just theory. NASA JPL's ION is the flagship implementation: first proven in space with the 2008 DINET experiment, and today it runs on the ISS. Looking ahead, the Moon's planned network — LunaNet — builds DTN in from the start. And the same machinery already reaches back down to Earth: disaster networks, underwater links, sensor networks. That's exactly the payoff of solving the hardest case first. That's the textbook path — and this is where our team goes one step further.

### 14. We didn't just read about it. We ran it

And this is where our team breaks away from the other presentations. Most teams would wrap up right where that case list ended. We went one step further and actually verified those five stages in running code. Here's the plan in plain terms for the next three slides. One: we install ION-DTN, NASA JPL's open-source implementation — the same software family that flies in space — right there on GitHub at nasa-jpl slash ion-dtn. The open-source part is the whole point: this isn't a black box we're asking you to take on faith; anyone can download it and reproduce exactly what we did, and we did it on a regular laptop. Two: we use its built-in tool, owltsim, to fake the Earth-Mars light-speed delay. Three: we send one message across a link that's down and watch it survive and arrive. And one more thing — even if the live demo blows up in front of you, we've dropped an actual execution-log capture on a later slide, so the evidence holds no matter what. Let me show you the shape of the test first.

### 15. Two nodes. One fake Mars delay

So here's the whole experiment in one picture before we touch any commands. On the left, one node we're calling Earth, the sender. On the right, one node we're calling Mars, the receiver. Two nodes is the smallest setup that still shows a real end-to-end hop. Between them, owltsim injects a 600-second one-way delay — that's the orange arrow — so the Mars side physically can't hear a thing until 600 seconds have gone by. The bottom path is what matters: at time zero we hand a bundle called Hello Mars to the Earth node while the link is basically down, ION stores it, holds it, and the moment delivery becomes possible, forwards it on to Mars. That bottom loop — store, hold, forward — is the core idea happening in real code instead of on a slide. Keep this picture in mind for the next two slides: one explains what owltsim is, the other shows the actual run. So what exactly is that orange arrow?

### 16. owltsim: honest about what it fakes

That orange arrow is owltsim, and before the demo I want to draw its boundary honestly — because the obvious question is coming, does owltsim really mimic Mars, or is it just calling sleep, and we'd rather answer it before someone asks. So here's the straight version. What owltsim is: a one-way light-time simulator whose whole job is to take the 600-second delay we set and hold every signal between our two nodes for exactly that long before letting it through. It turns Mars is light-minutes away into a single number we dial in on a laptop. What it genuinely models is the timing — how long the delay is, and the link dropping then coming back. What it does NOT model is the radio physics — the real RF channel, the antenna, bit errors from radiation. It's not a physics simulator, and we're not pretending it is. And here's the crucial distinction: the part that actually matters — store-carry-forward and the responsibility handoff — isn't simulated at all. That runs in ION's real code. owltsim just feeds in the delay; ION itself does the real storing, holding, and handing off. Drawing that line is exactly what makes the demo credible: the timing is real, the channel is faked, and we're telling you which is which. So let's run it.

### 17. Sent into a dead link — it arrived 600 s later

So here's the actual run — expectation versus reality. What we did: bring up the two nodes, switch on the 600-second one-way delay, and — order matters — start the receiver, bpsink, in the background first, then send with bpsource. Receiver-first, because the bundle will be waiting to deliver the moment the delay elapses, so the listener has to already be sitting there. What we expected: ordinary TCP, sent into a down link, dies right away and times out; DTN's promise is stored now, delivered later. And that's exactly what the log shows. At time zero, Hello Mars is stored because the link is down — the exact moment TCP would give up — and 600 seconds later, when the light-time elapses, ION forwards it, Hello Mars lands at the receiver, and custody is confirmed. That's the headline: store-carry-forward in real code — stored while disconnected, delivered intact the instant the link returned. Same disruption: TCP fails, DTN delivers — the living proof I promised earlier. Now let's put exact numbers on that gap.

### 18. TCP vs DTN — by the numbers

So the second differentiator turns that one log line into a full table of numbers. First, to be clear, 12 minutes is an assumed value — it ranges from 3 to 22 minutes depending on distance, so we just took a middle figure. At 12 minutes one-way, the round-trip is 24 minutes, and a 3-way handshake is 1.5 round-trips — three one-way trips — about 36 minutes. And if the link breaks anywhere in there, TCP's session dies, starts over from scratch, and eventually just times out. But the bottom row is the key one: the very owltsim demo we just saw — under the same disruption, TCP fails to deliver while DTN delivers 100% after the delay. So the demo and this table lock together on one screen. That's why DTN is faster is the wrong framing — the design premise is different is the accurate one. And there's one number underneath all this worth a quick look — why TCP can't even keep the pipe full across that delay.

### 19. BDP: too much data "in flight"

This is that number. BDP — bandwidth-delay product — is just bandwidth times round-trip delay. At 1 Mbps across a 24-minute round trip, you've got about 180 MB in flight at any given instant. TCP can't keep that much un-acknowledged sitting in its window, so flow control is basically paralyzed. DTN sidesteps it entirely: it stores per hop, so it's never in that everything-in-flight-at-once state. So even the throughput math points the same way. Now — just as important as where DTN wins is knowing where you should not use it.

### 20. When NOT to use DTN

So, honestly: DTN is not a cure-all. Intermediate nodes hold data for a long time, so the storage burden is heavy, and congestion control is still pretty immature. The rule of thumb is simple — if there's no guaranteed continuous path and the delay or disruption is large, use DTN; if it's stable and low-latency like the regular internet, just use TCP/IP, where DTN would be a net loss. Knowing where to use it and, just as importantly, where not to — that's what real understanding looks like. That's DTN in general — so now let me be just as honest about our own work specifically.

### 21. What we proved — and where it stops

Quickly, then — what did we actually prove, and where does it stop? On the wins: we walked a single bundle from Earth to Mars through all five stages; we ran NASA's ION-DTN ourselves and watched it deliver intact across a dead link; and we quantified the gap — TCP fails under disruption, DTN delivers 100% once the delay passes. On the limits, just as plainly: this was a small, representative demo — two nodes and one injected delay, not a real RF link; owltsim models the timing, not the radio physics; and we didn't test it at scale, or for security, or for routing at scale. In short, we proved the mechanism — not the whole system. So let me bring the whole thing home in one sentence.

### 22. Internet assumes a path open now / DTN assumes one someday

So, one sentence: the internet assumes a path that's open now, and DTN assumes a path that'll open someday. What we did today was start from that elevator glitch, walk through the five stages, and then verify them by running ION-DTN ourselves. The core insight is that thanks to store-carry-forward and the responsibility handoff, data arrives even when the path is never open all at once — so it's not that it's slow, it's that the premise is different. And looking ahead, it's already operational in space, with research pushing it into terrestrial and 6G non-terrestrial networks too. So the next time your text fails in an elevator and lands a second later, you'll know there's a whole interplanetary network built on that same instinct. Thank you — happy to take questions.

### 23. Sources

(Breeze through the references here, or pull them up as backup during Q&A.)

---

## Q&A armor (not spoken — pull up only if asked)

Brief, accurate answers held in reserve so the slides stay clean and the talk stays on time:

- **Endpoint ID (EID) / addressing** — DTN names endpoints with URI-like EIDs (schemes `ipn:` and `dtn://…`), not IP addresses (RFC 9171). This separates logical identity from the physical path, so a node keeps its identity as it disconnects, moves, and reconnects.
- **Convergence Layer Adapter (CLA)** — the adapter between the bundle layer and each underlying protocol (a TCP convergence layer, TCPCL / RFC 9174, on the ground; LTP / RFC 5326 on the space link). RFC 9171 frames the underlying layer as "transport and/or network protocols"; LTP is itself a convergence-layer protocol (it typically runs over UDP/datagram links), not a TCP-style transport.
- **Bundle metadata** — the primary block carries destination / source / report-to EIDs, a creation timestamp, and a lifetime — a millisecond duration after which the bundle is deleted ("Lifetime expired"). (Class-of-service / priority was an explicit 2-bit field in BPv6; BPv7 dropped it and leaves CoS to extension blocks.)
- **CGR internals** — builds a contact graph from the contact plan and runs a modified, earliest-arrival-time Dijkstra; the route loop suppresses the limiting contact and re-searches (Yen's-style k-shortest paths, anchoring). CGR is specified in CCSDS SABR (734.3-B-1), not an IETF RFC. (Buffer/capacity-aware refinements such as *contact splitting* and *edge pruning* are recent research — Berthelot et al., 2024 — not canonical CGR.)
- **Custody / BPv6 vs BPv7** — custody transfer was a core feature of BPv6 (RFC 5050); it was *removed* from the BPv7 core (RFC 9171), which leaves reliability to extensions. One proposed extension, Bundle-in-Bundle Encapsulation (BIBE), offered custody-style hop-by-hop recovery, but it is an expired Internet-Draft; a separate BPv7 custody-transfer spec is being standardized (CCSDS, experimental). NASA JPL ION implements custody transfer as an option — that's the `custody OK` line in our log.
- **Persistent storage** — "store" means to disk (SSD/HDD), not RAM, so a bundle survives a node power-cycle while it waits for the next contact. That's also why the storage burden (slide 20) is real.
- **DSOC / Psyche (2023)** — an optical-comms (physical-layer) demo, *not* DTN itself; the two are complementary — bundle protocol on top, optical link underneath.
- **"Why not just a bigger TCP window or QUIC?"** — tuning helps throughput on a *connected* link, but none of them deliver across a link that is *disconnected for minutes to hours* with no end-to-end path. That's a premise difference, not a tuning gap.
