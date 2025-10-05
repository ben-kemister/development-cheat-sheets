---
title: "CUDA"
tags:
- driver
- nvidia
- ai
---

CUDA, or Compute Unified Device Architecture, is a parallel computing platform and programming model developed by NVIDIA. 
<!--more-->
It allows developers to use NVIDIA GPUs for general-purpose computing, significantly accelerating applications by 
offloading computationally intensive tasks to the GPU's many cores.
While the CPU handles sequential parts of the workload, the GPU efficiently executes the parallelizable portions.

## CUDA GPU Compute Capability

Compute capability (CC) defines the hardware features and supported instructions for each NVIDIA GPU architecture.

Nvidia maintains a list of the compute capability for their GPU [here](https://developer.nvidia.com/cuda-gpus).
For legacy GPUs, refer to [Legacy CUDA GPU Compute Capability](https://developer.nvidia.com/cuda-legacy-gpus).

For example a _GeForce RTX 3060_ has a compute capability of **8.6**.

## CUDA Toolkit compatibility

Wikipedia has a handle table of supported CUDA compute capabilities based on the CUDA SDK version and microarchitecture, 
listed by code name [here](https://en.wikipedia.org/wiki/CUDA#GPUs_supported).
