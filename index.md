---
image: https://arewep2pyet.com/assets/images/logo.png
---
[![Matrix](/assets/images/matrix-logo-white.svg)](https://matrix.org){: .logo} _Last updated: 2023-05-01_

```
                                        ____                      _   ___
  __ _ _ __ ___   __      _____    _ __|___ \ _ __     _   _  ___| |_/ _ \
 / _` | '__/ _ \  \ \ /\ / / _ \  | '_ \ __) | '_ \   | | | |/ _ \ __\// /
| (_| | | |  __/   \ V  V /  __/  | |_) / __/| |_) |  | |_| |  __/ |_  \/
 \__,_|_|  \___|    \_/\_/ \___|  | .__/_____| .__/    \__, |\___|\__| ()``
                                  |_|        |_|       |___/
```

# Not yet, but as of 2026 we're making progress again.

## 2026 update

P2P Matrix work has resumed at Element as of July 2026 thanks to funding from the Dutch Government.

We'll update this site properly once we get a chance, but current status is:
 * We've put together a dedicated lightweight embedded homeserver in Rust called Neutrino: https://github.com/element-hq/neutrino
   * It is **not** intended for standalone use, and is not yet secure for use in untrusted networks (e.g. it does not sign/check event signatures yet)
   * It only implements the very latest experimental room versions needed for P2P
   * The intention is to share its Rust code with the Synapse and Synapse Pro in future (rather than being a divergent evolutionary branch like Dendrite was, which meant halving efforts rather than converging efforts).
   * See the [README](https://github.com/element-hq/element-x-android-neutrino/blob/develop/README.md) for more caveats and details.
 * Element X Android has a new P2P fork which embeds Neutrino: https://github.com/element-hq/element-x-android-neutrino
 * We're not planning to build an overlay network. Instead, Neutrino currently uses [Iroh](https://docs.iroh.computer/) for connecting over [BLE](https://docs.iroh.computer/transports/bluetooth) and is designed for smaller meshes, in future using the normal Matrix network to backhaul between local meshes.

For more info, see the slides from DWebCamp 2026: [P2P Matrix - A New Hope](/assets/pdf/2026-07-09%20DWebCamp%20P2P%20Matrix%20-%20A%20New%20Hope.pdf).
<!-- TODO: embed the recording once it's published! -->

## 2020-2023 details below:

Check out the [Introducing P2P Matrix](https://matrix.org/blog/2020/06/02/introducing-p2p-matrix/) and [Introducing Pinecone](https://matrix.org/blog/2021/05/06/introducing-the-pinecone-overlay-network) blog posts for why we are doing this.

Our aims are to:
 - Bootstrap a permanent public P2P Matrix network
 - Link it to the existing Matrix network to benefit from the existing content & users and make it actually usable.

Track the progress of P2P [Matrix](https://matrix.org) and join us at [#p2p:matrix.org](https://matrix.to/#/#p2p:matrix.org).

### Dendrite

We need a fully-featured production-ready homeserver which can be embedded into a range of clients, from mobile devices to web browsers.

<!-- TODO: Automatically generate -->
- Synapse parity: (as of [430932f](https://github.com/matrix-org/dendrite/commit/430932f0f161dd836c98082ff97b57beedec02e6))
    * ✅ Client-Server API: 93% (583/626), aim: >90%
    * ✅ Server-Server API: 100% (114/114), aim: 100%
- Embeddability:
    * ✅ Embeddable database (SQLite3)
    * ✅ WASM
    * ✅ Android
    * ✅ iOS
- Performance:
    * ✅ Ensure memory usage of embedded instances is bounded e.g cache sizes.
    * 🚧 Ensure CPU usage of embedded instances is bounded e.g # spawned goroutines.
- Stability and Maintenance:
    * 🚧 Test coverage >=80% for code used in embedded instances: 72.4% (as of [05607d6](https://github.com/matrix-org/dendrite/commit/05607d6b8734738bd5c32288e3d0ef8e827d11d0), sytest coverage only, excludes Complement and unit tests)
    * 🚧 Active and timely (<7d) triage over main issue tracker.
    * 🚧 dendrite.matrix.org metrics are healthy (Sentry, Prometheus)

### Pinecone

We need a production-ready [overlay network](https://en.wikipedia.org/wiki/Overlay_network) for P2P traffic. Testing pinecone is done primarily using the [simulator](https://pinecone.matrix.org) which allows for faster iteration times on protocol changes.

- Experiment with existing P2P network stacks for use with Matrix:
    * ✅ libp2p
    * ✅ yggdrasil
- Create a custom P2P network stack which works for Matrix:
    * ✅ Source-routed yggdrasil
    * ✅ SNEK for improved routing
    * ✅ Simulator to debug and stress-test the network
- Works on a range of transports:
    * ✅ Public internet
    * ✅ LANs
    * ✅ Bluetooth LE
- Resilience to adversarial attacks:
    * ✅ Sybil attacks
    * ✅ Eclipse attacks
    * ✅ Keyspace collision attacks
    * 🚧 Churn attacks
    * 🚧 Root hijacking attacks
    * ✅ Malicious packet drop attacks
- ✅ Good (>80% median) packet arrival performance in Mobility tests
    * Mobility is defined as 50 randomly placed nodes in a 1x1km square, each randomly moving 0-20m every 10s
    * Mobility testing is performed for 360 iterations of node movement
    * Packet arrival performance is measured using pings between a subset of randomly selected nodes at least 2 hops apart at each mobility step
- 🚧 Scales to meet global demand

### Matrix

We need to improve the Federation protocol to work with servers which frequently go offline and may have 1000s of servers (p2p nodes) in each room.

- 🚧 Implement comprehensive Store-and-Forward event routing capabilities in Matrix to allow nodes to talk to each other even if they aren't online at the same time. Matrix currently has limited support for this via backfilling events.
    * ✅ Initial design & implementation in Dendrite
    * ✅ Integrated into p2p demo apps
    * 🚧 Robust to differing levels of use (many user rooms, encrypted rooms, different scenarios of varying offline-ness, etc.)
- 🚧 Improve [event authentication rules](https://spec.matrix.org/unstable/server-server-api/#checks-performed-on-receipt-of-a-pdu) when the majority of nodes in the room are unreachable.
    * 🚧 Change the protocol to utilize a Power DAG, and ensure that the Power DAG is shared with all servers in the room. This allows servers to authenticate any incoming event without needing to make additional API calls.
    * ❌ Change the protocol to eagerly connect to servers to ensure we rapidly synchronise Power DAGs, rather than waiting until the user sends a message.
- ❌ Improve [semantic delivery of old events to clients](https://github.com/matrix-org/matrix-spec/issues/852) to ensure that when old nodes come online clients don't see lots of old messages.
- ❌ Support delivery of push notifications to P2P devices
- ❌ Extension: Improve delivery of media uploads (content ID based?)
- ❌ Extension: implement a better federation routing algorithm than full-mesh routing. This allows users to talk in very large rooms without O(n) scaling on the number of nodes in the room.

### Bridging between P2P and Normal Matrix

We need a way to bootstrap the system with content and users. We can use the existing Matrix ecosystem to do this.

- ❌ A server which can bridge traffic between P2P and normal matrix e.g `@ed25519-key:server-name.com` or a bridge.
- ❌ First-class support for P2P gateways in Matrix (e.g MSC for it) so any compatible room can use P2P rather than relying on a bridging server.

### Antigoals at this point

- Account portability or multihomed accounts are out of scope.  As a first cut, each P2P node will be its own user account (to avoid the initial release getting blocked on solving account portability)
- Avoid temptation to redesign all of Matrix; instead, do the minimal changes required to achieve the above goals.
- Avoid premature optimisation for metadata privacy.  As long as we don’t design it out, we can ratchet up our metadata privacy at the federation level (e.g. by adding in per-room user IDs, sealed sender, mixnets etc) in subsequent phases.
