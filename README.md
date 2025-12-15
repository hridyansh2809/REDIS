# REDIS — Custom In-Memory Key-Value Store in C++

A lightweight Redis-like in-memory data store and TCP server built from scratch in C++, featuring a non-blocking `poll()` event loop, progressive hash table rehashing, AVL-backed sorted sets, min-heap TTL expiration, idle connection timeouts, and a POSIX thread pool for asynchronous cleanup.

---

## ✨ Features

- **Non-Blocking Event Loop**: Single-threaded `poll()`-based TCP server listening on `0.0.0.0:1234` with buffered I/O.
- **Progressive Rehashing Hash Table**: Dual-table `HMap` (`newer` / `older`) that migrates buckets incrementally to avoid latency spikes.
- **Sorted Sets (`ZSet`)**: Dual-indexed by name (hash table) and `(score, name)` (AVL tree) for fast point lookups and range/offset queries.
- **TTL Expiration (Min-Heap)**: Key expiration timers managed with an indexed min-heap (`pexpire`, `pttl`).
- **Idle Connection Eviction**: Intrusive doubly-linked list (`DList`) tracking per-connection activity timestamps.
- **Thread Pool**: `pthread`-based 4-worker thread pool (`thread_pool.cpp`) for offloading heavy destruction work (such as clearing large sorted sets).

---

## 🛠️ Supported Commands

| Command | Arguments | Description |
| --- | --- | --- |
| `get` | `key` | Retrieve string value by key |
| `set` | `key val` | Set string value for key |
| `del` | `key` | Delete a key |
| `pexpire` | `key ttl_ms` | Set key expiration in milliseconds |
| `pttl` | `key` | Get remaining TTL in milliseconds |
| `keys` | — | List all keys in the store |
| `zadd` | `key score name` | Add or update a member in a sorted set |
| `zrem` | `key name` | Remove a member from a sorted set |
| `zscore` | `key name` | Get the score of a sorted set member |
| `zquery` | `key score name offset limit` | Range query sorted set members by `(score, name)` and offset |

---

## 📁 Project Structure

```text
REDIS/
├── 14/
│   ├── 14_server.cpp      # Event-loop TCP server, protocol parser, command handlers
│   ├── common.h           # Shared helpers (container_of, FNV hash)
│   ├── list.h             # Intrusive doubly-linked list for idle timers
│   ├── hashtable.h/.cpp   # Progressive-rehashing hash map
│   ├── avl.h/.cpp         # Self-balancing AVL tree
│   ├── zset.h/.cpp        # Sorted set (HMap + AVL)
│   ├── heap.h/.cpp        # Min-heap for TTL timers
│   └── thread_pool.h/.cpp # POSIX thread pool
└── README.md
```

---

## ⚡ Build & Run (Linux / WSL / macOS)

```bash
git clone https://github.com/hridyansh2809/REDIS.git
cd REDIS/14
g++ -O2 -Wall -Wextra -pthread \
  14_server.cpp avl.cpp hashtable.cpp heap.cpp thread_pool.cpp zset.cpp \
  -o redis_server
./redis_server
```

The server binds to `0.0.0.0:1234`.

---

## 👨‍💻 Author

**Hridyansh** — [github.com/hridyansh2809](https://github.com/hridyansh2809)
