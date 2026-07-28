# 🧬 Hypervectors database

This repository provides the hypervector database and the code needed to interact with it. The fundamental property of hypervectors is that the dot product between the hypervectors of datasets A and B is the size of their intersection.

## Database Download

The database is downloadable [here](https://drive.proton.me/urls/M9SG6CJ37W#IZz0B0LoLvX8):

```
wget https://drive.proton.me/urls/M9SG6CJ37W#IZz0B0LoLvX8
```

### Database Contents

In the database, you will find four files:

- **dimension.txt** — Contains a single number representing the dimension of the hypervectors (should be 2048)
- **metadata.txt** — Contains the list of all accessions with their corresponding metadata
- **vectors.bin** — Contains all the hypervectors in byte format. Each vector is a concatenation of 2048 int32 values. Each value is stored as an int for convenience but should be divided by sqrt(2048) when used. The file is the concatenation of the hypervectors in the order described in metadata.txt
- **vector_norms.txt** — A TSV file containing the norm of all the hypervectors

**Note:** This is a temporary small database for testing purposes.

## Interacting with the Database

This repository contains scripts and tutorials for interacting with the database. Specifically, it includes:

- (Scripts to sketch your own datasets)[#creating-hypervectors-of-your-datasets]
- (Tools to compare sketched datasets against all SRA datasets published before December 2023)[#comparing-datasets-against-the-database]

### Installation

#### Download and Build

Install the required dependencies:

```bash
conda install -c bioconda sourmash
# or
pip install sourmash
```

Clone the repository and build the project:

```bash
git clone https://github.com/RolandFaure/hypervector_database.git
cd hypervector_database
make
```

Executables will be created in the `hypervector_database/bin/` directory.

### Creating Hypervectors of Your Datasets

#### Basic Usage

```bash
./bin/hypervector_database <input.fa> <output.bin>
```

#### Options

- `-m, --mode <M>` — Processing mode: `reads` or `assembly` (default: `assembly`)
  - `assembly`: Use all k-mers from the dataset
  - `reads`: Keep only k-mers with abundance ≥ 2 (filters out single-occurrence k-mers)
- `-d, --dim <N>` — Target dimension for random projection (default: 2048)
- `-k <N>` — K-mer size for sourmash (default: 31)
- `-s <N>` — Scaled factor for sourmash (default: 1000)
- `-h, --help` — Show help message

**Note:** Do not change `-d`, `-k`, or `-s` if you want to interact with the downloadable database.

#### Modes Explained

- **assembly**: Uses all k-mers detected in the input file.
- **reads**: Filters k-mers by abundance, keeping only those seen 2 or more times. This is useful for sequencing reads, which often contain errors that manifest as unique k-mers (seen only once). Filtering out these singletons reduces noise.

#### Output Format

The `.bin` file contains a byte-packed vector where each value is stored as an int32. Values should be divided by sqrt(d) before local usage or uploaded as such in other commands of this tool suite.

### Comparing Datasets Against the Database

To find SRA accessions with the highest Jaccard similarity to your dataset(s):

1. Sketch your dataset(s) using the script above
2. If you have multiple hypervectors, concatenate them into a single bin file
3. Run the `query` script:

```bash
./bin/query --query <file> --db <folder> --output <file> \
            [--num_threads <int>] [--top_k <int>] [--help]
```

This returns a TSV file with the top `k` most similar accessions in the SRA (up to 2023) ranked by Jaccard similarity. The three columns are:
- Index of your query (if multiple hypervectors were queried)
- Accession hit
- Jaccard similarity

## Tutorial

Let's find the most similar SRA datasets to DRR018843. In practice, this will work with any dataset.
First, download the DRR018843 reads:

```bash
fastq-dump DRR018843
#or go on https://trace.ncbi.nlm.nih.gov/Traces/?view=run_browser&acc=DRR018843 and manually download
```

Then, sketch the dataset to create a hypervector. Use the `reads` mode since this is a sequencing dataset, in order to filter out single-occurrence k-mers, which likely represent sequencing errors:

```bash
path/to/hypervector_database/bin/hypervector_database DRR018843.fastq DRR018843.bin -m reads
```

Finally, query the hypervector against the database to obtain the top 100 most similar SRA datasets:

```bash
path/to/hypervector_database/bin/query --query DRR018843.bin --db path/to/hypervector_database --output DRR018843_results.tsv --top_k 100
```


## License

MIT License - See [LICENSE](LICENSE) for details