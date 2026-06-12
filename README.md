Hybrid is a high-performance, Rust-based unified trading infrastructure for cryptocurrency exchanges. It provides a clean, consistent, and type-safe interface for interacting with multiple crypto exchanges through a single abstraction layer.

</br>
<h2> Architecture </h2>

```
                                CONTROL PLANE
                   (Subscriptions, API Keys, Routing Rules)
                                       │
             ┌─────────────────────────┴───────────────────────────┐
             ▼                                                     ▼
        DATA PLANE                                          EXECUTION PLANE
     [ Tokio Runtime ]                                     [ Tokio Runtime ]

       WS Adapters                                         REST/WS Trading API
             │                                                     ▲
       Decode Layer (Zero-copy)                                    │ 
             │                                         Rate Limiter (AtomicU64 Bucket)
       Normalize Layer                                             │
             │                                             Order Manager
       OrderBook Engine (Sync)                                     ▲
             │                                                     │
             ▼                                                Risk Engine
        DISPATCHER (Fan-out)                                       ▲
             │                                                     │
             ├─[ crossbeam::bounded ]─► Storage Engine [I/O]       │
             │                                                     │
             ├─[ crossbeam::bounded ]─► External API [Tokio]       │
             │                                                     │
             └─[ crossbeam::bounded ]─┐ (Low latency border)       │
                                      ▼                            │
                               STRATEGY ENGINE                     │
                                [ OS Threads ] ────────────────────┘
                                               └─[ crossbeam::bounded ]
                                                      (Fast Path)
  ```
