# GROMACS-2023.5-Plumed-2.9.2 Bunya H100 GPU Installation README

## Overview
This repository contains installation scripts and performance benchmarks for GROMACS-2023.5 patched with Plumed on Bunya H100 GPUs. Two installation approaches are provided: from-source build (Billy's version) and module-based installation (Marlies' version).

## Compatibility Notes

### GPU Compatibility (2023-2024)
Testing between 2023 and late 2024 demonstrates that GROMACS with Plumed cannot be compiled for AMD GPUs. This limitation affects:
- AMD Instinct MI series GPUs
- AMD Radeon Pro series GPUs  
- AMD GPU acceleration backends

Compilation issues stem from incompatibilities between Plumed's patching mechanism and AMD's ROCm/HIP frameworks. NVIDIA CUDA is the supported GPU acceleration platform for GROMACS-Plumed installations.

### Version Adaptability
Installation scripts can be adapted for GROMACS versions on Bunya H100 infrastructure:
- GROMACS 2024.x series: Modifications to version numbers and cmake flags
- Releases: Update download URLs, version dependencies, and test compatibility
- Plumed updates: Plumed versions may require patching procedures

Users should verify compatibility matrices between GROMACS, Plumed, and CUDA versions before attempting installations with software versions.

## Performance Comparison: Billy's vs Marlies' Installation

### Benchmark Overview
Performance comparison between Billy's from-source installation and Marlies' module-based installation using multiple walker metadynamics simulations on Bunya H100 GPUs. Speeds are normalised for 24 CPUs/replica and 2 GPUs per replica on the Gadi gpuvolta partition.

## Test System Configuration
- Simulation Type: Multiple walker metadynamics
- Hardware: Bunya H100 GPUs
- Replicates: 2 walkers per simulation
- GPU Configuration: 2 H100 GPUs (1 GPU per replicate)
- Variable Parameter: CPU allocation per replicate

## Performance Results

| CPUs | GPUs | Replicates | CPU/rep | GPU/rep | Billy's Speed (ns/day) | Billy's Normalised | Marlies' Speed (ns/day) | Marlies' Normalised |
|------|------|------------|---------|---------|------------------------|-------------------|-------------------------|-------------------|
| 8    | 2    | 2          | 4       | 1       | 24.20                  | 1.37              | 23.57                   | 1.34              |
| 12   | 2    | 2          | 6       | 1       | 27.16                  | 1.54              | 27.30                   | 1.55              |
| 16   | 2    | 2          | 8       | 1       | 30.11                  | 1.71              | 29.47                   | 1.67              |
| 24   | 2    | 2          | 12      | 1       | 30.78                  | 1.75              | 29.47                   | 1.67              |
| 36   | 2    | 2          | 18      | 1       | 24.28                  | 1.38              | 24.46                   | 1.39              |
| 48   | 2    | 2          | 24      | 1       | 20.43                  | 1.16              | 19.40                   | 1.10              |

## Performance Analysis

### Peak Performance
- Configuration: 24 CPUs (12 CPUs per replicate)
- Billy's Peak: 30.78 ns/day (normalised: 1.75)
- Marlies' Peak: 29.47 ns/day (normalised: 1.67)
- Performance Difference: Billy's version is 4.4% faster at peak performance

### Performance Scaling

#### CPU Range (8-24 CPUs)
- Both installations show scaling behaviour
- Performance increases from 8 to 24 CPUs
- Billy's version maintains 2-5% advantage

#### Point (24 CPUs)
- 12 CPUs per replicate provides performance
- Both versions achieve throughput at this configuration
- CPU:GPU ratio on H100 architecture

#### Over-allocation (36-48 CPUs)
- Performance degrades beyond 24 CPUs due to:
  - Communication overhead between processes
  - Resource contention
  - Returns from parallelisation
- Performance drop is consistent across both versions

### Version Performance Characteristics

#### Billy's Installation (From Source)
- Characteristics:
  - FFTW library compilation
  - Build flags for H100 architecture
  - Plumed-2.9.0 built with optimisations
- Performance Characteristics:
  - 2-5% speed advantage
  - Performance across CPU counts
  - Resource utilisation at CPU counts

#### Marlies' Installation (Module-Based)
- Characteristics:
  - Uses Plumed-2.9.2
  - Leverages RCC modules
  - Consistent with system-wide optimisations
- Performance Characteristics:
  - Peak performance
  - Scaling behaviour
  - Predictable across systems

## Resource Allocation Recommendations

### For Performance
- Configuration: 24 CPUs, 2 H100 GPUs
- Allocation: 12 CPUs per replicate, 1 GPU per replicate
- Version: Billy's installation for 4.4% speed boost

### For Performance/Efficiency Balance
- Configuration: 16 CPUs, 2 H100 GPUs  
- Allocation: 8 CPUs per replicate, 1 GPU per replicate
- Efficiency: 97% of peak performance with 33% fewer CPU cores

### For Resource-Constrained Environments
- Configuration: 12 CPUs, 2 H100 GPUs
- Allocation: 6 CPUs per replicate, 1 GPU per replicate
- Efficiency: 88% of peak performance with 50% fewer CPU cores

## Benchmark Methodology
- Normalisation: Performance normalised to baseline single-core performance
- Reference System: Speeds normalised for 24 CPUs/replica and 2 GPUs per replica on Gadi gpuvolta partition
- Metrics: Simulation speed measured in nanoseconds per day (ns/day)
- Reproducibility: Runs averaged for each configuration
- System Load: Allocation with no competing jobs

## Conclusions

1. Performance: Billy's from-source installation provides performance benefits (2-5%)

2. Scaling: Both versions peak at 24 CPUs with returns beyond this point

3. Resource Efficiency: 16-24 CPU range offers performance per resource unit

4. Installation Choice: 
   - Choose Billy's version for performance in production environments
   - Choose Marlies' version for maintenance and system consistency

5. Hardware Utilisation: H100 GPUs are utilised across tested configurations with 1 GPU per replicate being configuration
