# Lemur

Lemur is a tool for rapid and accurate taxonomic profiling on long-read metagenomic datasets 

## Installation

### Obtaining the CLI
`lemur` can be installed via 
```
conda install -c bioconda lemur
```

#### Alternative option
`lemur` can also be installed by copying the `./lemur` file  to anywhere on your system's path.


### Obtaining the database
The current database (RefSeq v221 bacterial and archaeal genes, and RefSeq v222 fungal genes) is available at [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.10802546.svg)](https://doi.org/10.5281/zenodo.10802546)

## Usage

### Basic usage

For minimal example you will need to specify the following parameters: the input FASTQ file containing the reads (`-i/--input` flag), a directory to store the Lemur output (`-o/--output` flag), path to the database directory (`-d/--db-prefix` flag), path to the taxonomy file in the TSV format (`--tax-path` flag), and desired taxonomic aggregation rank (`-r/--rank` flag).

```
lemur -i examples/example-data/example.fastq \
      -o example-output \
      -d examples/example-db \
      --tax-path examples/example-db/taxnomy.tsv \
      -r species
```

The output in the `example-output` folder will consist of raw `relative_abundance.tsv` file with taxonomic IDs, lineage information, and inferred relative abundance (`F` column). There will also be a `relative_abundance-[rank].tsv` where the rank is specified by the `-r/--rank` flag (e.g. in the above example it will be `species`). The `*P_rgs_df*` files capture individual inferred probabilities of a given read comign from a particular taxon. 

### Parameter descriptions

Main arguments:
```
  -i INPUT, --input INPUT
                        Input FASTQ file for the analysis
  -o OUTPUT, --output OUTPUT
                        Folder where the Lemur output will be stored
  -d DB_PREFIX, --db-prefix DB_PREFIX
                        Path to the folder with marker gene DB for each marker gene
  --tax-path TAX_PATH   Path to the taxonomy.tsv file 
  -t NUM_THREADS, --num-threads NUM_THREADS
                        Number of threads you want to use
  --aln-score {AS,edit,markov}, --aln-score {AS,edit,markov}
                        AS: Use SAM AS tag for score, edit: Use edit-type distribution for score, markov: Score CIGAR as Markov chain
  -r RANK, --rank RANK  Taxonomic rank used for final aggregation
```

minimap2 arguments:
```
  --mm2-N MM2_N         minimap max number of secondary alignments per read [50]
  --mm2-K MM2_K         minibatch size for minimap2 mapping [500M]
  --mm2-type {map-ont,map-hifi,map-pb,sr}
                        ONT: map-ont [map-ont], PacBio (hifi): map-hifi, PacBio (CLR): map-pb, short-read: sr
```

Miscellaneous arguments:
```
  --keep-alignments     Keep SAM files after the mapping (might require a lot of disk space)
  -e LOG_FILE, --log-file LOG_FILE
                        File for logging [default: stdout]
  --sam-input SAM_INPUT Use a SAM file as input and skip read mapping step
  --verbose             Enable DEBUG level logging
  --save-intermediate-profile
                        Will save abundance profile at every EM step
  --width-filter        Apply uniform coverage filter
```

Additional flags:
```
  -h, --help            show usage help message and exit
  -v, --version         show program's version number and exit
```
