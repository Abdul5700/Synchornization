# Experiment 5: Synchronization Mechanisms in Parallel Programs

## 1. Objective

To implement and study common synchronization mechanisms used in shared-memory parallel programs, including race conditions, critical sections, atomic operations, barriers, semaphores, and locks. The experiment evaluates correctness, execution time, and scalability as the number of threads increases.

## 2. Software Requirements

- **Operating System:** Ubuntu 24.04 LTS
- **Compiler:** GCC 13.3.0
- **Parallel Programming:** OpenMP
- **Thread Library:** POSIX Threads (pthreads)
- **Language:** C

## 3. Synchronization Mechanisms

### 3.1 Race Condition

A race condition occurs when multiple threads access and update shared data without proper synchronization. Concurrent updates can be lost, resulting in an incorrect counter value.

### 3.2 Critical Section

An OpenMP critical section allows only one thread at a time to execute a protected block of code.

Directive: `#pragma omp critical`

### 3.3 Atomic Operation

An OpenMP atomic operation protects a supported update to a shared memory location from interference by concurrent updates.

Directive: `#pragma omp atomic`

### 3.4 Barrier

A barrier synchronizes threads at a common execution point. Every thread must reach the barrier before any thread can continue beyond it.

Directive: `#pragma omp barrier`

### 3.5 Semaphore

A semaphore controls access to shared resources. This experiment uses a POSIX binary semaphore to protect a shared counter accessed by two threads.

Functions: `sem_wait()` and `sem_post()`

### 3.6 Lock

An OpenMP lock provides mutual exclusion. A thread acquires the lock before accessing shared data and releases it afterward.

Functions: `omp_set_lock()` and `omp_unset_lock()`

## 4. Project Files

| File | Description |
|---|---|
| `race.c` | Demonstrates a race condition using an unsynchronized shared counter. |
| `critical.c` | Uses an OpenMP critical section to protect counter increments. |
| `atomic.c` | Uses an OpenMP atomic operation to protect counter increments. |
| `barrier.c` | Demonstrates barrier synchronization between threads. |
| `semaphore.c` | Uses a POSIX semaphore to protect a shared counter. |
| `compare_sync.c` | Compares correctness and execution time for unsynchronized, critical-section, atomic, and lock-based updates. |

## 5. Installation and Compilation

Update the package list and install the required compiler tools:

```bash
sudo apt update
sudo apt install build-essential -y
```

### Program 1: Race Condition

```bash
gcc race.c -fopenmp -o race
./race
```

Run with different thread counts:

```bash
OMP_NUM_THREADS=2 ./race
OMP_NUM_THREADS=4 ./race
OMP_NUM_THREADS=8 ./race
```

### Program 2: Critical Section

```bash
gcc critical.c -fopenmp -o critical
./critical
```

### Program 3: Atomic Operation

```bash
gcc atomic.c -fopenmp -o atomic
./atomic
```

### Program 4: Barrier

```bash
gcc barrier.c -fopenmp -o barrier
OMP_NUM_THREADS=4 ./barrier
```

### Program 5: Semaphore

```bash
gcc semaphore.c -pthread -o semaphore
./semaphore
```

### Program 6: Synchronization Comparison

```bash
gcc compare_sync.c -fopenmp -o compare_sync
./compare_sync
```

## 6. Benchmark Configuration

The comparison program uses the following configuration:

- **Workload:** 10,000,000 shared-counter increments per benchmark run
- **Thread counts:** 1, 2, 4, and 8
- **Mechanisms compared:** None, Critical, Atomic, and Lock
- **Correct expected count:** 10,000,000
- **Timing function:** `omp_get_wtime()`

Execution times depend on the processor, virtual machine configuration, operating system, and runtime conditions.

## 7. Experimental Results

The following measurements were obtained in the Ubuntu VMware environment.

| Mechanism | Threads | Final Count | Correctness | Time (seconds) |
|---|---:|---:|---|---:|
| None | 1 | 10,000,000 | Correct | 0.0059 |
| Critical | 1 | 10,000,000 | Correct | 0.1201 |
| Atomic | 1 | 10,000,000 | Correct | 0.0683 |
| Lock | 1 | 10,000,000 | Correct | 0.1142 |
| None | 2 | 5,276,071 | Incorrect | 0.0095 |
| Critical | 2 | 10,000,000 | Correct | 0.2834 |
| Atomic | 2 | 10,000,000 | Correct | 0.1330 |
| Lock | 2 | 10,000,000 | Correct | 0.3408 |
| None | 4 | 5,593,297 | Incorrect | 0.0080 |
| Critical | 4 | 10,000,000 | Correct | 0.3306 |
| Atomic | 4 | 10,000,000 | Correct | 0.1469 |
| Lock | 4 | 10,000,000 | Correct | 0.3223 |
| None | 8 | 3,693,844 | Incorrect | 0.0093 |
| Critical | 8 | 10,000,000 | Correct | 0.2909 |
| Atomic | 8 | 10,000,000 | Correct | 0.1482 |
| Lock | 8 | 10,000,000 | Correct | 0.3503 |

**Note:** These are measurements from one benchmark run. Execution times and unsynchronized counter results can vary between runs.

## 8. Semaphore Program Result

The semaphore program creates two threads, each performing 100,000 increments.

Expected output:

```text
Counter = 200000
```

The observed output was:

```text
Counter = 200000
```

## 9. Observations

1. The unsynchronized program produced incorrect counter values with multiple threads because concurrent updates were not protected.
2. The single-thread unsynchronized run produced the expected count because there was no concurrent access by multiple threads.
3. The critical-section program produced the correct count for all tested thread counts.
4. The atomic-operation program produced the correct count and had lower measured execution times than the critical-section and lock-based programs in the multithreaded tests.
5. The lock-based program produced the correct count but incurred locking overhead.
6. The barrier program demonstrated synchronization at a common execution point. The order of thread output was not fixed.
7. The semaphore program produced the correct final count of 200,000.

## 10. Conclusion

The experiment demonstrated the importance of synchronization in shared-memory parallel programming. Unsynchronized access to a shared counter can produce incorrect results because of race conditions. Critical sections, atomic operations, locks, and semaphores provide mechanisms for protecting shared data when used correctly, while barriers coordinate the progress of threads.

The benchmark compared correctness and execution time using 1, 2, 4, and 8 threads. In the measured multithreaded runs, atomic operations were faster than critical sections and locks for the shared-counter workload. The results demonstrate that synchronization overhead and contention influence performance and scalability, and that the appropriate synchronization mechanism depends on the application's requirements.
