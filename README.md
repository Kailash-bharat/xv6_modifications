# xv6 Extensions – IITH Course Project

## Features

- **Custom System Calls**
  - `hello()` → prints a kernel message
  - `getpid2()` → returns PID of the calling process
  - `getppid()` → returns parent PID (`-1` if none)
  - `getnumchild()` → counts alive child processes (zombies excluded)
  - `getsyscount()` → syscall count of the calling process
  - `getchildsyscount(pid)` → syscall count of a child process

- **System Call Aware MLFQ Scheduler**
  - Four priority queues (0–3) with time slices:  
    Q0 → 2 ticks, Q1 → 4 ticks, Q2 → 8 ticks, Q3 → 16 ticks
  - Demotion when full slice consumed
  - Priority boost every 128 ticks
  - Interactive processes (frequent syscalls/sleep) stay in higher queues
  - System calls:
    - `getlevel()` → current priority level
    - `mlfqinfo(pid, struct mlfqinfo *info)` → scheduling info (ticks, calls, runs)

- **Scheduler‑Aware Page Replacement**
  - Clock algorithm for page eviction
  - In‑memory swap space for evicted pages
  - Frame table tracking owners, addresses, reference bits
  - Lazy allocation (pages allocated on first access)
  - Process stats: faults, evictions, swaps, resident pages
  - Deep copy in `fork()` to avoid shared frames

- **Disk‑Backed Swap + RAID**
  - Pages swapped to disk blocks instead of RAM
  - Disk scheduling policies:
    - FCFS (First Come First Serve)
    - SSTF (Shortest Seek Time First)
    - `setdisksched(int policy)` system call
  - RAID support:
    - RAID 0 (Striping)
    - RAID 1 (Mirroring)
    - RAID 5 (Striping with Parity)
    - `setraid(int mode)` system call
  - Disk statistics:
    - `getdiskstats(long *stats)` → reads, writes, avg latency
  - Process‑level stats: `disk_reads`, `disk_writes`, `disk_latency_sum`

---

## Results

- **System Calls** → validated with parent/child relationships and syscall counters
- **MLFQ Scheduler** → correct demotion/promotion, starvation prevention
- **Page Replacement** → processes continue under memory pressure, correct eviction/swap‑in
- **Disk + RAID** → stable swapping, RAID correctness verified, SSTF latency lower than FCFS

---

## Design Decisions

- Swap eviction triggered in `kalloc()` when memory full
- Swap metadata tracked in PTE reserved bits
- Disk layout: swap area after filesystem (`FSSIZE`)
- RAID mapping: logical block → `(disk_id, block_index)`
- Synchronization: minimal locks during disk I/O, stats updated safely

---

## Conclusion

This project extends xv6 with:
- Rich system call support
- Syscall‑aware MLFQ scheduling
- Demand paging with Clock replacement
- Disk‑backed swap, RAID, and disk scheduling

Together, these features make xv6 more resilient and closer to real OS behavior under memory and I/O pressure.
