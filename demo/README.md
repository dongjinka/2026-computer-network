# Demo configuration — two nodes, one 600 s Mars delay

These files reproduce the topology shown in the talk: node 1 = Earth (sender, `ipn:1`),
node 2 = Mars (receiver, `ipn:2`), joined by a single LTP-over-UDP link with a 600 s
one-way light time injected by ION's `owltsim`. They are modeled directly on ION's own
two-node IPN/LTP owltsim tests under `../src/tests/` (e.g. `issue-364-dtpc.ipn2`), so the
syntax matches the pinned ION 4.2.0-a.1 source.

| File | Role |
|------|------|
| `host1.rc` | Earth node (`ipn:1`) — combined `ionstart -I` config |
| `host2.rc` | Mars node (`ipn:2`) — combined `ionstart -I` config |
| `node1.ionconfig` / `node2.ionconfig` | per-node SDR/shared-memory keys (distinct, so both nodes coexist on one host) |
| `owlt.config` | `owltsim` delay table — 600 s each way |

## Port map

```
Earth (node 1)  udplsi 127.0.0.1:1113   udplso -> 127.0.0.1:9113 (owltsim in)
Mars  (node 2)  udplsi 127.0.0.1:2113   udplso -> 127.0.0.1:9112 (owltsim in)
owltsim  9113 --600 s--> 2113  (Earth -> Mars)
owltsim  9112 --600 s--> 1113  (Mars  -> Earth)
```

## Running it

Two ION nodes on one host need separate working directories and `ION_NODE_LIST_DIR` set,
exactly as ION's own tests do. From a fresh shell, after building and installing ION
(see [../DEMO.md](../DEMO.md)):

```bash
export ION_NODE_LIST_DIR=$PWD
mkdir -p n1 n2
cp host1.rc node1.ionconfig n1/   &&  cp host2.rc node2.ionconfig n2/
cp owlt.config .

( cd n1 && ionstart -I host1.rc )    # Earth
( cd n2 && ionstart -I host2.rc )    # Mars
owltsim owlt.config &                # inject the 600 s one-way delay
bpsink  ipn:2.1 &                    # receiver first — order matters
bpsource ipn:2.1 "Hello Mars"        # send the bundle into the (delayed) link
```

Expected: `bpsource` returns immediately; `bpsink` prints `Hello Mars` about 600 s later,
after `owltsim` releases the segments. Tear down with `ionstop` in each node directory.

> Scope: `127.0.0.1` and the port numbers assume a single-host loopback run. For two real
> hosts, replace `127.0.0.1` in `owlt.config` and the `udplso` lines with the peer IPs.
> `owltsim` models the delay only; store-carry-forward and custody run in ION's real code.
