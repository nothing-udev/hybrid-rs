<b>Hybrid</b> - high-performance, Rust-based unified trading infrastructure for cryptocurrency exchanges. It provides a clean, consistent, and type-safe interface for interacting with multiple crypto exchanges through a single abstraction layer.

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
             │                                                Order Manager
       OrderBook Engine (Sync)                                     ▲
             │                                                     │
             ▼                                                     │  
        DISPATCHER (Fan-out)                                       │
             │                                                     │
             ├─[ crossbeam::bounded ]─► Storage Engine [I/O]       │
             │                                                     │
             ├─[ crossbeam::bounded ]─► External API [Tokio]       │
             │                                                     │
             └─[ crossbeam::bounded ]─┐ (Low latency border)       │
                                      │                            │
                                      │                            │
                                      ▼                            ▼
   ┌──────────────────────────────────────────────────────────────────────────┐
   │                            WASM RUNTIME (Wasmtime)                       │
   │                                                                          │
   │   ┌────────────┐   ┌────────────┐   ┌────────────┐   ┌────────────┐      │
   │   │ Bot 1      │   │ Bot 2      │   │ Bot 3      │   │ Bot 5000   │      │
   │   │ (Memory)   │   │ (Memory)   │   │ (Memory)   │   │ (Memory)   │      │
   │   └─────┬──────┘   └─────┬──────┘   └─────┬──────┘   └─────┬──────┘      │
   └─────────┼────────────────┼────────────────┼────────────────┼─────────────┘
             └────────────────┴────────────────┴────────────────┘ 
                  (Shared Linear Memory + Atomic Host Functions)
  ```

</br>
<h2> Supported Exchanges </h2> 

| Exchange | Link | Market Data | Spot Trading | Futures Trading | Demo / Testnet | Progress |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| <img src="https://img.shields.io/badge/Binance-D4A017?style=flat-square&logo=binance&logoColor=black"/> | [API](https://binance.com) | `✓ Yes` | `✓ Yes` | `✓ Yes` | `✓ Yes` | `░░░░░░░░░░` 0% |
| <img src="https://img.shields.io/badge/Bybit-C9892A?style=flat-square"/> | [API](https://bybit.com) | `✓ Yes` | `✓ Yes` | `✓ Yes` | `✓ Yes` | `░░░░░░░░░░` 0% |
| <img src="https://img.shields.io/badge/OKX-18181B?style=flat-square"/> | [API](https://okx.com) | `✓ Yes` | `✓ Yes` | `✓ Yes` | `✓ Yes` | `██████████` 100% |
| <img src="https://img.shields.io/badge/MEXC-3B82F6?style=flat-square"/> | [API](https://mexc.com) | `✓ Yes` | `✓ Yes` | `⟳ WIP` | `✕ No` | `████████░░` 80% |
| <img src="https://img.shields.io/badge/BingX-3A8FD8?style=flat-square"/> | [API](https://bingx.com) | `✓ Yes` | `⟳ WIP` | `✕ No` | `✓ Yes` | `██████░░░░` 60% |
| <img src="https://img.shields.io/badge/Bitunix-2F9D7E?style=flat-square"/> | [API](https://bitunix.com) | `✓ Yes` | `✕ No` | `✕ No` | `✕ No` | `████░░░░░░` 40% |
| <img src="https://img.shields.io/badge/Hyperliquid-22A38B?style=flat-square"/> | [API](https://hyperliquid.xyz) | `○ Soon` | `✕ No` | `✕ No` | `✓ Yes` | `██░░░░░░░░` 20% |

</br>
*Work in pogress*
