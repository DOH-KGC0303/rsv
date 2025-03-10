# RSV Washington Focused Build

## Build overview 
- **Build name:** RSV Washington Focused Build
- **Pathogen/Strain:** RSV
- **Scope:** Whole genome, F gene and G gene builds for both RSV A and RSV B
- **Purpose:** This repository contains the Nextstrain build for Washington State genomic surveillance of RSV.
- **Nextstrain Build Location:**

## Table of Contents  

## Getting Started 
- This repository will generate six views, three each for RSV A and RSV B
    - Genome: Shows evolution of the entire genome
    - G Gene: Focuses on only the G gene which is highly variable and for which there are more available sequences
    - F Gene: Focuses on only the F gene, which currently does not have any clade annotations available.
 
-This build also features washington focused tierd subsmapling 

## Data Sources and Inputs

This build pulls all RSV sequences from GenBank when running the ingest pipeline. 

Input metadata and sequences for RSV-A and RSV-B are also available via <https://data.nextstrain.org>

- [RSV-A sequences](https://data.nextstrain.org/files/workflows/rsv/a/sequences.fasta.xz)
- [RSV-A metadata](https://data.nextstrain.org/files/workflows/rsv/a/metadata.tsv.gz)

- [RSV-B sequences](https://data.nextstrain.org/files/workflows/rsv/b/sequences.fasta.xz)
- [RSV-B metadata](https://data.nextstrain.org/files/workflows/rsv/b/metadata.tsv.gz)

These data are generously shared by labs around the world and deposited in NCBI genbank by the authors.
Please contact these labs first if you plan to publish using these data.
RSV sequences and metadata can be downloaded in the `/ingest` folder using
`nextstrain build --cpus 1 ingest` or `nextstrain build --cpus 1 .` if running directly from the `/ingest` directory.

## Setup & Dependencies 

### Installation

Follow the standard [installation instructions](https://docs.nextstrain.org/en/latest/install.html) for Nextstrain's suite of software tools.

To check that Nextstrain is installed:
```
nextstrain check-setup
```
### Clone the repository:

```
git clone https://github.com/DOH-KGC0303/rsv
cd rsv
```

## Running the Build 
From within the repository the build can be run with: 
```
nextstrain build . 
```

### Run the Build with Test Data
This build does not currently have test data available. This feature is coming soon. 

## Repository File Structure Overview
The file structure of the repository is as follows with `*`" folders denoting folders that are the build's expected outputs.

```
.
├── README.md
├── Snakefile
├── auspice*
├── ingest
├── config
    ── auspice_config.json
    ── configfile.yaml
    ── description.md
    ── outliers.txt
├── nextclade
├── results*
├── workflow
    ── snakemake_rules
        ── core.smk
└── scripts
```

- `Snakefile`: ?
- `ingest`: see following section
- `config/`: In addition to the files listed the config folder contains the sequence data for the A and B referecnce sequences, clade information, and coloring data.
     - `auspice_config.json`: Config file pertaining to how data is displayed in Auspice
    - `configfile.yaml`: Config file used in creating the build, including filtering and subsampling 
    - `description.md`: Markdown file for the description displayed under the visualizations in Auspice
    - `outliers.txt`: Text file of sequences to be excluded from the build
- `nextclade`: ??? 
- `workflow/`:
    - `snakemake_rules/`: contains a variety of snakemake rules. Most notably:
        - `core.smk`: The sequence of rules that will get followed in running the build, this includes indexing and aligning sequences, and filtering based on the criteria found in the config files.     
- `scripts/`: A set of neccessary python scripts. 
  
### `ingest/vendored`

The ingest pipeline is based on the Nextstrain mpox ingest workflow (<https://github.com/nextstrain/mpox/tree/master/ingest>).
Running the ingest pipeline produces `ingest/data/{a,b}/metadata.tsv` and `ingest/data/{a,b}/sequences.fasta`.

This repository uses [`git subrepo`](https://github.com/ingydotnet/git-subrepo) to manage copies of ingest scripts in [`ingest/vendored`](./ingest/vendored), from [nextstrain/ingest](https://github.com/nextstrain/ingest). To pull new changes from the central ingest repository, first install `git subrepo`, then run:

See [ingest/vendored/README.md](./ingest/vendored/README.md#vendoring) for instructions on how to update the vendored scripts.

## Expected Outputs and Interpretation
After successfully running the build there will be two output folders containing the build results.

- `auspice/` folder contains two jsons each for all six of the builds. One contains the root sequence used in building the tree. The second all of the visualization information that will be used by Auspice in displaying the data, including gene annotaions and coloring information. 
- `results/` folder contains nested folders for each of the six trees. These folders contain any other outputs created in the running of the build, including sequence alignments and the tree.json. 

## Scientific Decisions 
- **Tiered subsampling**: Includes all Washington sequences while maintaining national/global context. Outside of Washington sequences are sampled evenly across countries and years of collection. 
- **Other adjustments**:
  - `config/includes.txt`: These sequences are always included into our sampling strategy as they are relevant to our epidemiological investigations.
  - `config/excludes.txt`: These sequences are always excluded from our subsampling and filtering due to duplication, known data errors or based on epidemiological linkage knowledge.

## Adapting for Another State
 - **Tiered subsampling**: To change the focus of the tiered subsampling the subsampling section needs to be updated in configfile.yaml found in the config folder. 

## Data use

We gratefully acknowledge the authors, originating and submitting laboratories of the genetic sequences and metadata for sharing their work. Please note that although data generators have generously shared data in an open fashion, that does not mean there should be free license to publish on this data. Data generators should be cited where possible and collaborations should be sought in some circumstances.

## Update example data

[Example data](./example_data/) is used by [CI](https://github.com/nextstrain/rsv/actions/workflows/ci.yaml). It can also be used as a small subset of real-world data.

Example data should be updated every time metadata schema is changed. To update, run:

```sh
nextstrain build --docker . update_example_data -F
```


## Sending data to the `nextclade_data` repo

From within the destination directory, run
```
rsync -a <path-to>/rsv/nextclade/datasets/ .
```
