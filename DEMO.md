# DEMO — Reproducing "a message survives a broken link" with ION-DTN

This demo confirms the five stages of the talk (the problem, store-carry-forward, custody,
transport, routing) in running code. We build NASA JPL's ION-DTN and use its built-in `owltsim`
(one-way light-time simulator) to fake the Earth-Mars delay, then watch one bundle (`"Hello Mars"`)
go stored -> held -> delivered across a link that is down.

Note on owltsim: it models only timing (the delay length, and the link going down then back up).
It does not model the physics (RF channel, antenna power, radiation bit-errors). Store-carry-forward
and custody are not simulated; they run in ION's real code. owltsim only supplies the delay.

## 0. Environment
- Linux or WSL2
- build-essential, autoconf, automake, libtool, gcc, make

## 1. Fetch the ION source (submodule)
```bash
git submodule update --init --recursive   # checks out ION-DTN (commit e9863023e) into src/
cd src
```

## 2. Build
```bash
autoreconf -fi          # only if the configure script is missing
./configure
make
sudo make install
sudo ldconfig
```
See `src/README.md` and `src/doc/` for build options and platform notes.

## 3. Run the demo: two nodes + a 600 s light-time delay
Conceptual recipe (as shown on the slides):
```bash
ionstart -I host1.rc            # sending node (Earth)
ionstart -I host2.rc            # receiving node (Mars)
owltsim  owlt.config            # inject one-way light-time delay (owlt = 600 s)
bpsink   ipn:2.1 &              # receiver first (order matters)
bpsource ipn:2.1 "Hello Mars"   # send the bundle
```
`host1.rc`, `host2.rc`, and `owlt.config` were composed ad hoc for the live talk and are not
checked into this repo. Rebuild the same topology (node 1 = Earth, node 2 = Mars, owlt = 600 s)
from ION's example configs (`src/configs/`, `src/demos/`) and the `owltsim` usage.

## 4. Expected result
| Time | TCP/IP | DTN (ION) |
|------|--------|-----------|
| t = 0 (link down) | times out immediately, delivery fails | bundle is stored at the node |
| t = 600 s (delay elapsed) | — | forwarded; "Hello Mars" arrives at bpsink, receipt confirmed (custody OK) |

Under identical disruption, TCP fails and DTN delivers after the delay.

## References
- ION-DTN: https://github.com/nasa-jpl/ION-DTN (Bundle Protocol v7, RFC 9171)
- Slides 07-10 and 15-18 contain the demo topology, execution log, and numeric comparison.
- Q&A preparation: `presentation/_workspace/06_qa_prep.md`
