# DNA to Vector Conversion

A C++ CLI tool that converts DNA sequences to random-projected vectors for comparative genomics and sequence similarity analysis.

## Pipeline

The tool integrates three steps:

1. **Sketching**: Generates k-mer signatures using sourmash
   - Default: k=31, scaled=1000
   
2. **Hashing**: Extracts min-hashes from the signature
   
3. **Projection**: Applies random projection to reduce to fixed-dimensional vectors
   - Default dimension: 2048
   - Output format: Byte-packed binary (.bin)

## Requirements

- C++ compiler (g++ 7+)
- sourmash (with Python/conda environment)
- Make

## Installation

### Install Dependencies

**Ubuntu/Debian:**
```bash
sudo apt-get install build-essential
```

**macOS:**
```bash
brew install make
```

### Install sourmash
```bash
conda install -c bioconda sourmash
# or
pip install sourmash
```

## Building

```bash
make
```

Executables will be created in `bin/` directory.

## Usage

### Basic Usage
```bash
./bin/dna_to_vector <input.fa> <output.bin>
```

### With Options
```bash
./bin/dna_to_vector <input.fa> <output.bin> [options]
```

### Options
- `-d, --dim <N>` — Target dimension for random projection (default: 2048)
- `-k <N>` — K-mer size for sourmash (default: 31)
- `-s <N>` — Scaled factor for sourmash (default: 1000)
- `-h, --help` — Show help message

### Examples

Default parameters:
```bash
./bin/dna_to_vector dataset.unitigs.fa output_vector.bin
```

Custom dimension:
```bash
./bin/dna_to_vector genome.fa output.bin -d 4096
```

Different sourmash parameters:
```bash
./bin/dna_to_vector genome.fa output.bin -k 21 -s 500
```

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

## Development

### Building from Source
```bash
make clean
make
```

### Cleaning Build Artifacts
```bash
make clean
```

### Help
```bash
make help
```

## License

MIT License - See [LICENSE](LICENSE) for details
