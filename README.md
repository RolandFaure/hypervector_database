# 🧬 Hypervectors database

This repository provides the hypervector database and the code needed to interact with it.

The database is downloadable [here](https://drive.proton.me/urls/M9SG6CJ37W#IZz0B0LoLvX8)
```
wget https://drive.proton.me/urls/M9SG6CJ37W#IZz0B0LoLvX8
```

In the database, you will find four files.
- dimension.txt contains a single number, the dimension of the hypervectors (should be 2048)
- metadata.txt contains the list of all accessions with their corresponding metadata
- vectors.bin contains all the hypervectors. The hypervectors are stored in a byte format: each vector is a concatenation of 2048 int32. Each value is stored as an int for convenience but should be divided by sqrt(2048) when used. The file is the concatenation of the hypervectors. 

[NOTE: this is a temporary small database for testing]

A C++ CLI tool that converts DNA sequences to random-projected vectors for comparative genomics and sequence similarity analysis.

## Requirements

- C++ compiler (g++ 7+)
- sourmash (with Python/conda environment)
- Make

## Installation

### Dependencies
```bash
conda install -c bioconda sourmash
# or
pip install sourmash
```

## Download and Build

Clone the repository and build the project:
```bash
git clone https://github.com/RolandFaure/DNA_to_vector.git
cd DNA_to_vector
make
```

Executables will be created in the `DNA_to_vector/bin/` directory.

## Usage

### Basic Usage
```bash
./bin/dna_to_vector <input.fa> <output.bin>
```

### Options
- `-m, --mode <M>` — Processing mode: `reads` or `assembly` (default: `assembly`)
  - `assembly`: Use all k-mers from the sketch
  - `reads`: Keep only k-mers with abundance ≥ 2 (filters out single-occurrence k-mers, useful for noisy read data)
- `-d, --dim <N>` — Target dimension for random projection (default: 2048)
- `-k <N>` — K-mer size for sourmash (default: 31)
- `-s <N>` — Scaled factor for sourmash (default: 1000)
- `-h, --help` — Show help message


## Modes Explained

- `assembly`: Uses all k-mers detected in the input file.
- `reads`: Filters k-mers by abundance, keeping only those seen 2 or more times. This is useful for sequencing reads, which often contain errors that manifest as unique k-mers (seen only once). By filtering out these singletons, noise is reduced.

## Output Format

The `.bin` file contains a byte-packed vector where:
- First 4 bytes: vector size (int32)
- Following bytes: each vector component (0-255)

## Project Structure

```
.
├── src/
│   ├── main.cpp                # CLI entry point & pipeline orchestration
│   └── random_projection.cpp   # Random projection implementation
├── include/
│   └── random_projection.h     # Random projection interface
├── Makefile                    # Build configuration
└── README.md                   # This file
```

## Pipeline

The tool integrates three steps:

1. **Sketching**: Generates k-mer signatures using sourmash
   - Default: k=31, scaled=1000
   
2. **Hashing**: Extracts min-hashes from the signature
   
3. **Projection**: Applies random projection to reduce to fixed-dimensional vectors
   - Default dimension: 2048
   - Output format: Byte-packed binary (.bin)

## License

MIT License - See [LICENSE](LICENSE) for details
