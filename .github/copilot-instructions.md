# DNA to Vector Conversion - Project Instructions

## Project Overview
This C++ project converts DNA sequences to random-projected vectors for similarity analysis. It integrates sourmash sketching with random projection dimensionality reduction.

## Architecture
- **sourmash sketching**: Generates k-mer signatures (k=31, scaled=1000)
- **Random projection**: Reduces signature to fixed-dimensional vectors (default: 2048)
- **Output format**: Byte-packed binary (.bin) files

## Build System
- Build tool: Makefile
- Compiler: g++ (C++11 or later)
- Dependencies: Eigen3, sourmash (installed system-wide)

## Development Notes
- Random projection header expects Eigen VectorXi output
- Binary output packs floats as bytes per vector component
- Sourmash must be pre-installed and in PATH
