# idunn-dht

## Plan

- DHT for discovery *only*
    - So really a DRT (distributed routing table)
- Nodes are devices
    - Device keys are signed by identity keys
- Single location per node
- No data entries in DHT – only nodes
- TODO: cryptographic protocol for peer joining

## Design notes

- Routing table, per bit:
    - Primary contact bucket
    - Agreed no “replacement cache”
    – Mark failed nodes as stale; replace with new when seen
- One message/response for DRT
    - Message: `Lookup(node)`
    - No need for `PING`: just mark failing nodes as “stale” and replace with
      new when observed.
    - No need for DHT data-related messages
- On receipt of `Lookup(node)`:
    - Build and reply with set of `k` nodes
        - Fill initially from associated bucket
        - If fewer than `k` in bucket:
            - Fill first from higher buckets
            - Then fill from lower
- To perform a node lookup for `node`:
    1. From routing table, build set of `k` closest nodes
    2. Send `Lookup(node)` messages to the `α` closest we have not yet queried.
    3. As responses arrive, update set of `k` closest nodes as necessary.
    4. Repeat at (2) until either `node` is found or all nodes in `k`-set have
       been queried.
