# DTN Presentation — English Script & Flow

> Per-slide spoken script — one continuous talk. In sync with `presentation.md`.

> ~15 min · 21 slides · 11 cited sources (RFC standards [1-7] + team-verified [8-11]).


---

## Flow at a glance

1. `Computer Networks · 2026` **DTN**
2. `00 — Framing the problem` **When your text won't send in an elevator**
3. `00 — Framing the problem` **And the extreme case — why is there no "internet" for a Mars rover?**
4. `Roadmap — the journey of a single piece of data` **Today's map: how one bundle travels from Earth to Mars**
5. `01 · Stage ① Problem — background` **Extreme / long delay & disruption**
6. `02 · Stage ① Problem — problem definition` **Where TCP/IP breaks — its 4 assumptions collapse**
7. `03 · Stage ②③ Store, carry, responsibility — core idea` **"Why not just store it and send later?" — fixing that naive answer step by step**
8. `04 · Stage ②③ Store, carry, responsibility — architecture` **Bundle Protocol — one overlay layer**
9. `05 · Stage ④ Transport — reliable transport` **LTP — transport built for long-delay links**
10. `Mid-point recap — so what` **So far: filling the gaps gives us a network that "arrives even when disconnected"**
11. `06 · Stage ⑤ Path — routing` **Routing that knows the future vs. routing that bets on encounters**
12. `07 · Stage ⑤ Path — implementations & cases` **It's already running in space**
13. `08 — Differentiator ① (here we leave the textbook path)` **We didn't just read about it — we ran it ourselves**
14. `08 · Differentiator ① — the setup in one picture` **Our whole test, drawn: two nodes, one fake Mars delay**
15. `08 · Differentiator ① — what owltsim is (and what it is not)` **owltsim — the "Mars delay" knob, honestly bounded**
16. `08 · Stage ②③ Store, carry, responsibility — Differentiator ①, run & results` **The run: send into a dead link, and it still arrives — 600 s later**
17. `09 · Stage ①④ — Differentiator ②, quantitative comparison` **TCP vs DTN, by the numbers**
18. `09 — Aside: why BDP explodes` **Bandwidth-delay product (BDP) — too much data "in flight"**
19. `10 — Differentiator ③, decision tree` **Critically — limitations and "when not to use it"**
20. `11 — Wrap-up (recovering Stages ①–⑤)` **In one sentence**
21. `References` **Sources**

---

## Slide-by-slide script


### 1. DTN
*Computer Networks · 2026*

Hi everyone. Before I even tell you our topic, a quick question. Have you ever tried to send a text in an elevator, watched it fail, and then the moment your signal came back, it suddenly sent? Most of us have. Hold onto that — that tiny everyday glitch is the doorway into everything we'll talk about today. So let's start there.


### 2. When your text won't send in an elevator
*00 — Framing the problem*

So why does that happen? The message was never actually lost. The instant you lost signal, your phone just held onto it and fired it off the moment the connection came back. You've probably seen the dramatic version after a disaster — a whole stack of texts landing at once, hours late. Same mechanism: store it, carry it, send it when you can. That simple instinct — if you're cut off, don't give up, just hold it and forward it later — has a name, and a whole field of networking is built on it. That's our topic: DTN, Delay-Tolerant Networking. Our team dug into how you make this work not just across an elevator, but across delays and outages so severe the ordinary internet flat-out quits. And to feel how severe that gets, let's push the idea to its limit.


### 3. And the extreme case — why is there no "internet" for a Mars rover?
*00 — Framing the problem*

An elevator cuts you off for a few seconds. Now crank that same situation all the way up: a rover on Mars. The signal one-way takes minutes, and the link stays broken for hours at a time as the planets turn and orbits drift. And space isn't a random pick — it's the hardest case there is, and it's exactly where DTN is already built, standardized, and flying today, run by NASA and the CCSDS. So if we can crack the Mars gap, easier cases like the deep sea or a disaster zone come almost for free. Here's the catch that makes Mars so brutal: the TCP/IP we use every day quietly assumes there's an unbroken path between sender and receiver right now. On Mars, that assumption just falls apart. So today's talk is really that elevator glitch — blown up to the size of the solar system. Let me show you how we'll get there.


### 4. Today's map: how one bundle travels from Earth to Mars
*Roadmap — the journey of a single piece of data*

Here's the map. We split the journey of one piece of data — one "bundle" — from Earth to Mars into five stages: the problem, store-and-carry, responsibility, transport, and the path. For each stage we paired the question it asks with the technology that answers it. From here on, the number at the top of each slide tells you which stage we're in, and at the very end we'll confirm all five at once with a demo we ran ourselves. So let's start at stage one — why TCP/IP breaks out here in the first place.


### 5. Extreme / long delay & disruption
*01 · Stage ① Problem — background*

So why does it break? Space isn't just "slow internet" — it breaks the physical assumptions TCP was built on. Four things are going on. First, long and variable delay: Earth to Mars one-way is about 3 to 22 minutes, so round-trip is 6 to 44 minutes. The short, stable RTT TCP counts on just doesn't exist. Second, links drop constantly as nodes orbit, so a full end-to-end path might never exist at any single moment — that's a network partition. Third, uplink and downlink differ wildly in capacity. And fourth — keep this one in mind — packet loss out here is physical: cosmic radiation, interference. It's not congestion. And these same four conditions are exactly what you find in disaster zones, the deep sea, and remote areas — so anything that survives space carries straight over to them. Now the key move: each of these four realities collides head-on with something TCP takes for granted. Let's line them up.


### 6. Where TCP/IP breaks — its 4 assumptions collapse
*02 · Stage ① Problem — problem definition*

So here's the line-up. TCP rests on four assumptions, and in space every one collapses — each against one of the realities we just listed. One: it assumes a continuous end-to-end path, but paths never open all at once, so even the 3-way handshake can't finish — the connection never forms. Two: it assumes a short, stable RTT so ACKs come back fast, but at minute-scale the ACKs arrive far too late, the congestion window can't grow, and utilization stays terrible with so much data left in flight un-acknowledged. Three: it treats a timeout as loss and retransmits, but here the data was fine, just slow — so TCP re-sends perfectly good data and burns scarce bandwidth. And four, the deep one: TCP treats any loss as congestion and slows down. But in space a bit just got flipped by radiation; nothing is congested. So TCP backs off at exactly the wrong moment — and throughput collapses. The takeaway isn't "build a faster TCP" — we need a fundamentally different premise. And the simplest version of that premise is something you already do every day.


### 7. "Why not just store it and send later?" — fixing that naive answer step by step
*03 · Stage ②③ Store, carry, responsibility — core idea*

That different premise starts from the naive answer, and I'll say it out loud on purpose. "If you're cut off, just hold it and send it later, right?" And yeah — that's basically store-carry-forward. It's exactly what your phone did in the elevator: held the message, then forwarded it the moment the link came back — or as NASA puts it, only the next hop needs to be available. But a gap shows up right away. If the node holding the data powers off or dies, who's responsible now? The classic answer is custody transfer: a node formally takes on delivery responsibility — including retransmission — and hands it forward, node by node. Think of a courier depot relay: once the next depot signs off on the parcel, it's their responsibility from that point on. That sign-off is exactly what separates this from plain caching. One honest footnote: this custody mechanism was a core feature in the older Bundle Protocol v6; the current v7 moved it out of the core and leaves reliable handoff to a separate extension. That's a spec-level change, though — not "custody is gone": ION still implements custody transfer as an option — in BPv7 that's the Bundle-in-Bundle Encapsulation, or BIBE, extension — so that's exactly why you'll see custody OK in our demo log later. The idea — responsibility carries forward — is what matters. There's still one gap left, which route and when it opens, and we'll fill that in at routing. Filling these gaps one at a time is really what DTN design is — so let's see where they live in the stack.


### 8. Bundle Protocol — one overlay layer
*04 · Stage ②③ Store, carry, responsibility — architecture*

So where do these ideas actually live? DTN doesn't rip out the network you already have — it lays one overlay layer on top, the bundle layer. On the left the sender, on the right the receiver. The unit of data is a bundle, not a packet, and because each segment behaves differently, we swap out the convergence layer per segment — TCP on the terrestrial side, LTP on the space side. The standard here is BPv7, RFC 9171, formalized in 2022. I just said LTP on the space side, so let's look at why that one exists.


### 9. LTP — transport built for long-delay links
*05 · Stage ④ Transport — reliable transport*

Since we can't use TCP on the space segment, that swap on the right needs a real alternative — and that's LTP. The name comes from Licklider, one of the pioneers of the internet. Two key ideas: it splits data into a red part that has to get through and a green part that doesn't, and instead of waiting on ACKs right away, it defers them to match the RTT. That's what makes reliable transport possible even under minute-scale delay. That's four stages down, so let me pause and pull them together before the last one.


### 10. So far: filling the gaps gives us a network that "arrives even when disconnected"
*Mid-point recap — so what*

Quick recap before routing. We've been filling the gaps one at a time: the continuous-path assumption collapses, so store-and-carry means we don't have to wait, custody means responsibility carries forward, and LTP gives reliable transport on the space segment. And so what? The big deal is that the data eventually arrives even if the path is never fully open all at once. That leaves just one question — which route, and when do I send — and that's the last stage, routing.


### 11. Routing that knows the future vs. routing that bets on encounters
*06 · Stage ⑤ Path — routing*

So which route, and when? It splits into two branches. Somewhere like space, where orbits are predictable, we use CGR — it precomputes when each link will open, using a contact plan. On the flip side, somewhere like a disaster network, where you have no idea who you'll meet or when, we use opportunistic routing that sprays copies onto whoever you run into. Epidemic sprays to everyone, so delivery is high but overhead is huge, while Spray-and-Wait caps the number of copies to find a balance. And that predictable, contact-plan side isn't hypothetical — it's already flying.


### 12. It's already running in space
*07 · Stage ⑤ Path — implementations & cases*

And it really is flying — DTN isn't just theory. NASA JPL's ION is the flagship implementation, first proven in space with the 2008 DINET experiment, and today it runs on the ISS. One thing I want to clear up about Psyche's DSOC, launched in 2023: that's an optical-communications demo, so it's physical-layer — it isn't DTN itself. But the two are complementary: bundle protocol on top, optical link underneath. The lunar infrastructure, LunaNet, also builds in DTN. And the same machinery already reaches back down to Earth — disaster networks, underwater links, sensor networks like ZebraNet — which is exactly the payoff of solving the hardest case first. I'll keep that to one line and save the rest of our time for what comes next.


### 13. We didn't just read about it — we ran it ourselves
*08 — Differentiator ① (here we leave the textbook path)*

And this is where our team breaks away from the other presentations. Most teams would wrap up right where that case list ended. We went one step further and actually verified those five stages in running code. Here's the plan in plain terms for the next three slides. One: we install ION-DTN, NASA JPL's open-source implementation — the same software family that flies in space — right there on GitHub at nasa-jpl slash ion-dtn. The open-source part is the whole point: this isn't a black box we're asking you to take on faith; anyone can download it and reproduce exactly what we did, and we did it on a regular laptop. Two: we use its built-in tool, owltsim, to fake the Earth-Mars light-speed delay. Three: we send one message across a link that's down and watch it survive and arrive. And one more thing — even if the live demo blows up in front of you, we've dropped an actual execution-log capture on a later slide, so the evidence holds no matter what. Let me show you the shape of the test first.


### 14. Our whole test, drawn: two nodes, one fake Mars delay
*08 · Differentiator ① — the setup in one picture*

So here's the whole experiment in one picture before we touch any commands. On the left, one node we're calling "Earth," the sender. On the right, one node we're calling "Mars," the receiver. Two nodes is the smallest setup that still shows a real end-to-end hop. Between them, owltsim injects a 600-second one-way delay — that's the orange arrow — so the Mars side physically can't hear a thing until 600 seconds have gone by. The bottom path is what matters: at time zero we hand a bundle called "Hello Mars" to the Earth node while the link is basically down, ION stores it, holds it, and the moment delivery becomes possible, forwards it on to Mars. That bottom loop — store, hold, forward — is Stages two and three, store-carry-forward and custody, happening in real code instead of on a slide. Keep this picture in your head for the next two slides: one explains what owltsim is, the other shows the actual run. So what exactly is that orange arrow?


### 15. owltsim — the "Mars delay" knob, honestly bounded
*08 · Differentiator ① — what owltsim is (and what it is not)*

That orange arrow is owltsim, and before the demo I want to draw its boundary honestly — because the obvious question is coming, "does owltsim really mimic Mars, or is it just calling sleep?", and we'd rather answer it before someone asks. So here's the straight version. What owltsim is: a one-way light-time simulator whose whole job is to take the 600-second delay we set and hold every signal between our two nodes for exactly that long before letting it through. It turns "Mars is light-minutes away" into a single number we dial in on a laptop. What it genuinely models is the timing — how long the delay is, and the link dropping then coming back. What it does NOT model is the radio physics — the real RF channel, the antenna, bit errors from radiation. It's not a physics simulator, and we're not pretending it is. And here's the crucial distinction: the part that actually matters — store-carry-forward and the responsibility handoff — isn't simulated at all. That runs in ION's real code. owltsim just feeds in the delay; ION itself does the real storing, holding, and handing off. Drawing that line is exactly what makes the demo credible: the timing is real, the channel is faked, and we're telling you which is which. So let's run it.


### 16. The run: send into a dead link, and it still arrives — 600 s later
*08 · Stage ②③ Store, carry, responsibility — Differentiator ①, run & results*

So here's the actual run — expectation versus reality. What we did: bring up the two nodes, switch on the 600-second one-way delay through owlt.config, and — order matters — start the receiver, bpsink, in the background first, then send with bpsource. Receiver-first, because the bundle will be waiting to deliver the moment the delay elapses, so the listener has to already be sitting there. What we expected: ordinary TCP, sent into a down link, dies right away and times out; DTN's promise is "stored now, delivered later." What we actually saw in the log: at time zero, "Hello Mars" is stored at the node because the link is down — the exact moment TCP would give up — and 600 seconds later, when the light-time elapses, ION forwards it and "Hello Mars" lands at the receiver, with a confirmation that the receiver took it. That's the headline: store-carry-forward in real code — stored while disconnected, delivered intact the instant the link returned. Stages two and three, off the slide and into running software. Two quick honesty notes. One: this is a representative run, not a cherry-picked best case, and the log is right there as a static capture — so even if the live demo dies on us, the evidence stays put, and a presenter with a fresh log can just swap it in. Two: under identical disruption, TCP fails and DTN delivers — and that's the living proof I promised earlier. Now let's put exact numbers on that gap.


### 17. TCP vs DTN, by the numbers
*09 · Stage ①④ — Differentiator ②, quantitative comparison*

So the second differentiator turns that one log line into a full table of numbers. First, to be clear, 12 minutes is an assumed value — it ranges from 3 to 22 minutes depending on distance, so we just took a middle figure. At 12 minutes one-way, the round-trip RTT is 24 minutes, and a 3-way handshake is 1.5 RTTs — three one-way trips — about 36 minutes. And if the link breaks anywhere in there, TCP's session dies, starts over from scratch, and eventually just times out. One row flags the bandwidth-delay product blowing up — I've got a backup slide that breaks that number down if it comes up. But the bottom row is the key one: the very owltsim demo we just saw — under the same disruption, TCP fails to deliver while DTN delivers 100% after the delay. So the demo and this table lock together on one screen. That's why "DTN is faster" is the wrong framing — "the design premise is different" is the accurate one. And if that BDP row caught your eye, I've got a quick breakdown ready.


### 18. Bandwidth-delay product (BDP) — too much data "in flight"
*09 — Aside: why BDP explodes*

So this is that backup card. BDP — bandwidth-delay product — is just bandwidth times round-trip delay. At 1 Mbps with a 24-minute round trip, you've got about 180 MB flying across the line at any given instant. TCP can't keep that much un-acknowledged sitting in its window, so flow control is basically paralyzed. DTN stores per hop, so it never gets into that "everything in flight at once" state. Again, I'll only pull this up if a BDP question comes up — otherwise we go straight to where you should not use DTN.


### 19. Critically — limitations and "when not to use it"
*10 — Differentiator ③, decision tree*

So the third differentiator is being honest about the limitations. DTN is not a cure-all. Intermediate nodes hold data for a long time, so the storage burden is heavy, and congestion control is still pretty immature. The rule of thumb is simple: if there's no guaranteed continuous path and the delay or disruption is large, use DTN; if it's stable and low-latency like the regular internet, just use TCP/IP — DTN there is a net loss. Knowing where to use it and, just as importantly, where not to — that's what real understanding looks like. So let me bring the whole thing home in one sentence.


### 20. In one sentence
*11 — Wrap-up (recovering Stages ①–⑤)*

So, one sentence: the internet assumes a path that's open now, and DTN assumes a path that'll open someday. What we did today was start from that elevator glitch, walk through the five stages, and then verify them by running ION-DTN ourselves. The core insight is that thanks to store-carry-forward and the responsibility handoff, data arrives even when the path is never open all at once — so it's not that it's slow, it's that the premise is different. And looking ahead, it's already operational in space, with research pushing it into terrestrial and 6G non-terrestrial networks too. So the next time your text fails in an elevator and lands a second later, you'll know there's a whole interplanetary network built on that same instinct. Thank you — happy to take questions.


### 21. Sources
*References*

(Breeze through the references, or pull them up as backup during Q&A.)
