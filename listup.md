# SYSTEMS VISION NOTES LISTUP

## 1️⃣ C++ Core

C++ language fundamentals

- [ ] value category (lvalue / rvalue)
- [ ] move semantics
- [ ] copy vs move constructor
- [ ] rule of 3 / 5 / 0
- [x] [RAII](./cpp/raii.md)
- [x] [smart pointers (unique_ptr / shared_ptr / weak_ptr)](./cpp/smart_pointers.md)
- [x] [ownership semantics](./cpp/ownership_semantics.md)
- [x] [object lifetime](./cpp/object_lifetime.md)
- [ ] stack vs heap allocation
- [ ] placement new
- [ ] alignment
- [ ] custom allocator
- [ ] std::optional
- [ ] std::variant
- [ ] std::any
- [ ] template basics
- [ ] template specialization
- [ ] SFINAE
- [ ] concepts (C++20)
- [ ] constexpr
- [ ] inline functions
- [ ] lambda capture semantics
- [ ] function objects
- [ ] CRTP
- [ ] type traits
- [ ] static polymorphism
- [ ] dynamic polymorphism
- [ ] virtual table
- [ ] ABI stability
- [ ] exception safety
- [ ] noexcept
- [ ] memory layout of objects
- [ ] POD / trivial types
- [ ] type erasure

## 2️⃣ C++ System Design

Core concepts of C++ system architecture

- [ ] memory ownership design
- [ ] resource lifetime design
- [ ] API design principles
- [ ] zero copy design
- [ ] data oriented design
- [ ] object pool
- [ ] arena allocator
- [ ] intrusive containers
- [ ] memory reuse
- [ ] large buffer management
- [ ] lock granularity design
- [ ] cache friendly structures
- [ ] data locality
- [ ] false sharing avoidance
- [ ] modular architecture
- [ ] plugin architecture
- [ ] component based design
- [ ] dependency injection
- [ ] factory pattern
- [ ] builder pattern
- [ ] observer pattern
- [ ] strategy pattern
- [ ] message passing architecture
- [ ] event driven architecture
- [ ] state machine design
- [ ] finite state machine
- [ ] pipeline architecture
- [ ] streaming architecture

## 3️⃣ C++ Concurrency

Multi-thread / Concurrency

- [ ] std::thread
- [ ] std::async
- [ ] thread pool
- [ ] task scheduler
- [ ] futures / promises
- [ ] condition variable
- [ ] mutex
- [ ] recursive mutex
- [ ] shared mutex
- [ ] spinlock
- [ ] read write lock
- [ ] lock ordering
- [ ] deadlock
- [ ] livelock
- [ ] starvation
- [ ] producer consumer pattern
- [ ] bounded queue
- [ ] blocking queue
- [ ] thread safe queue
- [ ] lock free queue
- [ ] ring buffer
- [ ] double buffering
- [ ] triple buffering
- [ ] back pressure
- [ ] sentinel values
- [ ] pipeline parallelism
- [ ] work stealing scheduler
- [ ] task graph execution
- [ ] thread affinity
- [ ] NUMA awareness
- [ ] memory ordering
- [ ] acquire release semantics
- [ ] sequential consistency
- [ ] atomic operations
- [ ] compare exchange
- [ ] ABA problem
- [ ] hazard pointers
- [ ] RCU

## 4️⃣ Operating System

- [ ] process vs thread
- [ ] context switching
- [ ] scheduling algorithms
- [ ] priority scheduling
- [ ] real time scheduling
- [ ] interrupt handling
- [ ] syscall overhead
- [ ] virtual memory
- [ ] paging
- [ ] page fault
- [ ] TLB
- [ ] memory mapping
- [ ] mmap
- [ ] shared memory
- [ ] IPC mechanisms
- [ ] pipes
- [ ] message queues
- [ ] sockets
- [ ] epoll
- [ ] select / poll
- [ ] event loops
- [ ] async IO
- [ ] IO multiplexing
- [ ] DMA
- [ ] zero copy IO
- [ ] kernel vs user space
- [ ] CPU cache hierarchy
- [ ] NUMA
- [ ] huge pages

## 5️⃣ Computer Architecture

- [ ] CPU pipeline
- [ ] instruction level parallelism
- [ ] branch prediction
- [ ] cache hierarchy (L1, L2, L3)
- [ ] cache line
- [ ] cache coherence
- [ ] MESI protocol
- [ ] memory latency
- [ ] memory bandwidth
- [ ] prefetching
- [ ] vectorization
- [ ] SIMD
- [ ] AVX
- [ ] instruction throughput
- [ ] out of order execution
- [ ] register renaming
- [ ] pipeline stalls
- [ ] memory wall

## 6️⃣ CUDA / GPU

- [ ] CUDA execution model
- [ ] grid / block / thread
- [ ] warp
- [ ] warp scheduler
- [ ] warp divergence
- [ ] occupancy
- [ ] SM architecture
- [ ] memory hierarchy (global memory, shared memory, constant memory, texture memory)
- [ ] register pressure
- [ ] memory coalescing
- [ ] bank conflicts
- [ ] shared memory tiling
- [ ] CUDA streams
- [ ] stream concurrency
- [ ] kernel concurrency
- [ ] async memcpy
- [ ] pinned memory
- [ ] pageable memory
- [ ] unified memory
- [ ] H2D / D2H transfer
- [ ] overlapping copy and compute
- [ ] CUDA events
- [ ] CUDA graphs
- [ ] cooperative groups
- [ ] dynamic parallelism
- [ ] warp shuffle
- [ ] warp primitives
- [ ] tensor cores
- [ ] occupancy calculator
- [ ] launch configuration tuning
- [ ] persistent kernels
- [ ] kernel fusion
- [ ] kernel batching
- [ ] multi GPU programming
- [ ] NVLink
- [ ] peer to peer copy

## 7️⃣ GPU Performance Engineering

- [ ] Nsight Systems
- [ ] Nsight Compute
- [ ] roofline model
- [ ] memory bound kernels
- [ ] compute bound kernels
- [ ] warp stall reasons
- [ ] long scoreboard stalls
- [ ] instruction dependency
- [ ] dispatch stalls
- [ ] execution dependency stalls
- [ ] occupancy limits
- [ ] register spilling
- [ ] instruction throughput
- [ ] memory throughput
- [ ] DRAM utilization
- [ ] L2 cache utilization
- [ ] shared memory utilization
- [ ] kernel launch overhead
- [ ] stream synchronization cost
- [ ] PCIe bandwidth limits

## 8️⃣ Vision Systems

- [ ] image representation
- [ ] color spaces
- [ ] RGB / BGR
- [ ] YUV
- [ ] Bayer pattern
- [ ] camera calibration
- [ ] intrinsic parameters
- [ ] extrinsic parameters
- [ ] distortion models
- [ ] stereo vision
- [ ] epipolar geometry
- [ ] depth estimation
- [ ] optical flow
- [ ] feature detection
- [ ] feature matching
- [ ] RANSAC
- [ ] homography
- [ ] perspective transform
- [ ] image filtering
- [ ] convolution
- [ ] gaussian blur
- [ ] edge detection
- [ ] canny edge
- [ ] hough transform
- [ ] morphological operations
- [ ] image pyramids
- [ ] object detection pipelines
- [ ] tracking
- [ ] Kalman filter
- [ ] multi object tracking
- [ ] sensor fusion
- [ ] latency vs throughput tradeoff
- [ ] frame dropping strategies

## 9️⃣ Real-time Vision Pipeline Architecture

- [ ] capture pipeline
- [ ] camera driver interaction
- [ ] frame buffering
- [ ] frame queues
- [ ] back pressure control
- [ ] frame drop policy
- [ ] timestamp synchronization
- [ ] multi camera synchronization
- [ ] frame batching
- [ ] GPU inference pipeline
- [ ] preprocess pipelines
- [ ] postprocess pipelines
- [ ] asynchronous pipeline stages
- [ ] pipeline parallelism
- [ ] latency budgeting
- [ ] throughput optimization
- [ ] load shedding
- [ ] watchdog systems
- [ ] failure recovery
- [ ] pipeline monitoring
- [ ] metrics collection

## 🔟 Production System Design

- [ ] logging systems
- [ ] metrics systems
- [ ] tracing systems
- [ ] observability
- [ ] configuration management
- [ ] feature flags
- [ ] health checks
- [ ] watchdogs
- [ ] graceful shutdown
- [ ] crash recovery
- [ ] resource limits
- [ ] memory leak detection
- [ ] runtime diagnostics
- [ ] error propagation
- [ ] fault tolerance
- [ ] retry strategies

## 1️⃣1️⃣ Networking / Streaming

- [ ] TCP vs UDP
- [ ] RTP
- [ ] RTSP
- [ ] WebRTC
- [ ] video streaming pipelines
- [ ] packet loss handling
- [ ] jitter buffering
- [ ] latency optimization
- [ ] compression
- [ ] video encoding
- [ ] H264 / H265
- [ ] streaming buffers

## 1️⃣2️⃣ Debugging / Profiling

- [ ] gdb
- [ ] lldb
- [ ] valgrind
- [ ] address sanitizer
- [ ] thread sanitizer
- [ ] perf
- [ ] flame graphs
- [ ] CPU profiling
- [ ] memory profiling
- [ ] GPU profiling
- [ ] Nsight tools

## 1️⃣3️⃣ Software Engineering Practices

- [ ] code review practices
- [ ] testing strategies
- [ ] unit tests
- [ ] integration tests
- [ ] fuzz testing
- [ ] CI / CD
- [ ] reproducible builds
- [ ] CMake best practices
- [ ] dependency management
- [ ] versioning
- [ ] backward compatibility

## 1️⃣4️⃣ Algorithms

- [ ] arrays
- [ ] hash maps
- [ ] stacks
- [ ] queues
- [ ] linked lists
- [ ] binary trees
- [ ] heaps
- [ ] BFS
- [ ] DFS
- [ ] topological sort
- [ ] union find
- [ ] sliding window
- [ ] two pointers
- [ ] prefix sum
- [ ] binary search
- [ ] dynamic programming
- [ ] graph traversal
