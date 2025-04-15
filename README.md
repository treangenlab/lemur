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

---

### FAQ 

**Issue:** I run my analysis on a long-read metagenome, but it crashes with the following error:
```
Traceback (most recent call last):
  File "/Users/nsapoval/miniconda3/envs/lemur-test-env/bin/lemur", line 901, in <module>
    main()
  File "/Users/nsapoval/miniconda3/envs/lemur-test-env/bin/lemur", line 887, in main
    run.EM_complete()
  File "/Users/nsapoval/miniconda3/envs/lemur-test-env/bin/lemur", line 672, in EM_complete
    self.low_abundance_threshold = 1. / n_reads
                                   ~~~^~~~~~~~~
ZeroDivisionError: float division by zero
```

**Solutions:** Most likely this happens due to the filtering step which by default removes all alignments shorter than 75% of the corresponding marker gene length (see `--min-aln-len-ratio` flag description in the section below).

1. Produce a histogram of read lengths in your FASTQ file, if there is a significant portion of the sample of length below 400-500 bps, it is very likely that the above filter removes all alignments.
2. In the output folder, you can find a file called `P_rgs_df_raw.tsv`. It contains raw information about the alignments prior to the above filters. Verify the `aln_len` column of this file, if you see all values below 200-300 bps it means that there are no long alignments to marker genes.
3. If either of the above holds true, the analysis results might be unreliable. However, if you wish to proceed, you can add the `--min-aln-len-ratio 0.10` flag to the run retaining all alignments of length >=10% of the target marker gene length.

---

If you discover any additional issues while running the tool, please use [GitHub Issues](https://github.com/treangenlab/lemur/issues) interface to report it. Common issues and solution will be added to this FAQ.

---

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
  --aln-score {AS,edit,markov}
                        AS: Use SAM AS tag for score, edit: Use edit-type distribution for score, markov: Score CIGAR as Markov chain
  -r RANK, --rank RANK  Taxonomic rank used for final aggregation
  --min-aln-len-ratio MIN_ALN_LEN_RATIO
                        Minimum ratio of alignment length to marker gene length [default: 0.75]
  --min-fidelity MIN_FIDELITY
                        Minimum acceptable log(P)/aln_length [deafult: log(0.5)]
  --ref-weight REF_WEIGHT
                        Scale factor for log(P) dependent on alignment length: log(P) <- log(P) + REF_WEIGHT * log(aln_length_ratio) [default: 1.0]
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

## References

Sapoval, Nicolae, Yunxi Liu, Kristen D. Curry, Bryce Kille, Wenyu Huang, Natalie Kokroko, Michael G. Nute et al. "Lightweight taxonomic profiling of long-read metagenomic datasets with Lemur and Magnet." *bioRxiv* (2024). [DOI:[10.1101/2024.06.01.596961](https://doi.org/10.1101/2024.06.01.596961)]