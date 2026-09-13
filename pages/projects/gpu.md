---
layout: page
title: GPU & CUDA Programming
subtitle: High-performance parallel computing, graphics rendering pipelines, and heterogeneous computing with CUDA, OpenMP, and OpenGL
---

This page highlights various GPU, parallel computing, and graphics projects that I have developed through coursework and independent exploration. My work in this area bridges my electrical and computer engineering background with high-performance computing (HPC)—focusing on maximizing hardware utilization, managing complex memory hierarchies, and writing optimized parallel kernels.

---

## High-Performance CUDA Tiled Matrix Multiplication & Parallel Algorithms

To explore GPU architecture and massively parallel execution, I implemented high-throughput linear algebra kernels and stencil computations in CUDA C++. Standard naive matrix multiplication on GPUs frequently stalls on global memory bandwidth because each thread repeatedly reads data from high-latency global memory. To overcome this memory wall, I developed a tiled matrix multiplication kernel utilizing GPU shared memory as a programmer-managed cache.

In this implementation, thread blocks collaboratively load sub-matrices (tiles) from global memory into high-speed on-chip shared memory, synchronize via `__syncthreads()`, and compute partial inner products before loading the next tile. Key optimizations included:
- **Memory Coalescing:** Aligning thread index strides with matrix memory layouts so warps access contiguous 128-byte memory segments, maximizing bus transaction efficiency.
- **Shared Memory Bank Conflict Elimination:** Padding shared memory arrays to prevent multiple threads within a warp from accessing the same shared memory bank simultaneously.
- **Warp Divergence Avoidance:** Designing branch-free execution paths inside the compute loops to keep all 32 threads of each warp synchronized and executing in lockstep.
- **Parallel Reduction:** Implementing tree-based parallel reductions using warp shuffle intrinsics (`__shfl_down_sync`) to compute vector norms and scalar reductions without requiring shared memory or global atomic contention.

Using NVIDIA Nsight Compute and `nvprof`, I profiled kernel execution across square and rectangular matrices up to $4096 \times 4096$. The tiled shared-memory approach achieved near-peak compute throughput and delivered orders-of-magnitude speedups over optimized single-threaded CPU baselines.

---

## Multi-Core CPU Parallelization and Scalability with OpenMP

Alongside GPU computing, I implemented parallel shared-memory algorithms on multi-core CPUs using OpenMP to study the performance trade-offs between latency-oriented multi-core CPUs and throughput-oriented GPUs.

I applied OpenMP directives (`#pragma omp parallel for`) to compute-intensive algorithms including dense matrix operations, numerical integration, and grid-based heat diffusion simulations. Crucial architectural factors explored in this project included:
- **Loop Scheduling Strategies:** Benchmarking `static`, `dynamic`, and `guided` scheduling policies. While static scheduling minimized loop overhead for balanced workloads, dynamic and guided chunk scheduling prevented worker starvation in workloads with non-uniform iteration costs.
- **False Sharing Mitigation:** Identifying and eliminating false sharing where threads on adjacent CPU cores invalidated each other's L1/L2 cache lines when modifying neighboring elements in a shared array. I introduced cache-line padding and thread-local accumulators combined with OpenMP `reduction` clauses to preserve cache locality.
- **Scaling & Amdahl's Law:** Evaluating speedup curves across varying thread counts (from 1 to 32 threads) on multi-socket server nodes, measuring both strong scaling (fixed problem size) and weak scaling (problem size scaled proportionally with thread count).

This project provided deep insight into the transition point where CPU multithreading reaches memory saturation and when offloading to heterogeneous accelerators like GPUs becomes advantageous.

---

## Interactive 3D Graphics Engine & Shaders (Modern OpenGL, GLFW, GLSL)

To understand graphics hardware from the pipeline level, I built an interactive 3D rendering application in modern C++ utilizing modern OpenGL (core profile), GLFW for windowing and context management, and GLSL for custom shader development.

The rendering engine abstracts the graphics hardware pipeline into clean, modular C++ subsystems:
- **Buffer & Mesh Management:** Generating and configuring Vertex Array Objects (VAOs), Vertex Buffer Objects (VBOs), and Element Buffer Objects (EBOs) to batch vertex attributes (positions, normals, texture coordinates) into GPU memory for efficient drawing calls (`glDrawElements`).
- **Programmable Shaders:** Developing custom vertex and fragment shaders in GLSL. The vertex stage applies transformation matrices (Model, View, Projection) computed via GLM, while the fragment stage implements the Blinn-Phong illumination model. This accounts for ambient lighting, diffuse Lambertian reflectance, and specular highlights calculated from the half-vector between view and light directions.
- **Lighting & Camera Systems:** Supporting multiple simultaneous light sources, including directional lights (simulating sun/sky), point lights with quadratic distance attenuation, and spotlights. An interactive 6-degree-of-freedom FPS camera system translates mouse and keyboard inputs into pitch, yaw, and position vectors.
- **Texture Mapping & Filtering:** Applying diffuse and specular texture maps with mipmapping and anisotropic filtering to eliminate aliasing artifacts at steep viewing angles.

---

## Real-Time Parallel Particle Simulation (CUDA & OpenGL Interoperability)

Combining parallel computing with computer graphics, I developed a real-time particle simulation capable of simulating tens of thousands of interacting physical particles influenced by gravity, turbulence, and collision boundaries.

Instead of computing particle physics on the GPU, transferring coordinates back across the PCIe bus to system RAM, and then re-uploading them to OpenGL for rendering, I utilized **CUDA-OpenGL Interoperability** (`cudaGraphicsGLRegisterBufferObject`):
- A shared vertex buffer object is allocated in OpenGL and registered directly with the CUDA runtime.
- During each simulation tick, CUDA maps the OpenGL buffer, runs compute kernels that update position and velocity vectors in parallel across thousands of GPU threads, and unmaps the resource.
- OpenGL immediately renders the updated particle buffer without a single byte crossing the PCIe host interface.

This zero-copy pipeline maintained smooth 60+ FPS performance even under heavy particle counts, demonstrating the power of tightly coupled heterogeneous compute and visualization pipelines.

---