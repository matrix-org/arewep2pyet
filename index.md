[![Matrix](/assets/images/matrix-logo-white.svg)](https://matrix.org){: .logo} _Last updated: 05/04/2022_

```
                                        ____                      _   ___ 
  __ _ _ __ ___   __      _____    _ __|___ \ _ __     _   _  ___| |_/ _ \
 / _` | '__/ _ \  \ \ /\ / / _ \  | '_ \ __) | '_ \   | | | |/ _ \ __\// /
| (_| | | |  __/   \ V  V /  __/  | |_) / __/| |_) |  | |_| |  __/ |_  \/ 
 \__,_|_|  \___|    \_/\_/ \___|  | .__/_____| .__/    \__, |\___|\__| () 
                                  |_|        |_|       |___/                
```

# Not Yet.

Track the progress of P2P [Matrix](https://matrix.org).

### Dendrite 

We need a fully-featured production-ready homeserver which can be embedded into a range of clients, from mobile devices to web browsers.

<!-- TODO: Automatically generate -->
- Synapse parity: (as of [cee12a7](https://github.com/matrix-org/dendrite/commit/cee12a7ab06c0b41499e13ccd8d4ea3cd4832ab0))
    * ❌ Client-Server API: 77%, aim: >90%
    {: .notdone}
    * ❌ Server-Server API: 95%, aim: 100%
    {: .notdone}
- Embeddability:
    * ✅ Embeddable database (SQLite3)
    * ✅ WASM
    * ✅ Android
    * ✅ iOS
