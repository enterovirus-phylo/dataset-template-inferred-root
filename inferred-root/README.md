# Nextclade Setup for getting the inferred root

This repository contains the workflow for generating an inferred root sequence for < your virus > to be used as a reference in Nextclade analyses. 

## Folder/Files Structure

This folder has the following structure:

- scripts
  - `generate_from_genbank.py`
- ingest
  - bin
  - config
  - source-data
  - workflow
  - `README.md`
  - `snakefile`
- resources
  - `accession_strains.tsv`
  - `auspice_config.json`
  - `clades.tsv`
  - `include.txt`
  - `exclude.txt`
- dataset
 - `CHANGELOG.md`
 - `pathogen.json`
 - `README.md`

---
## Steps to Set Up The Workflow

### 1. Run the `ingest` snakefile
The instructions are detailed in the README.md (located in `ingest/`).
This will create a folder named `data/` in the main directory, containing the sequences (`sequences.fasta`) and associated metadata fields (`metadata.tsv`), needed for obtaining the inferred root.

Check that the files obtained in `data/references` have the appropiate names in the fields `/product=` and `/locus_tag=`, keeping just the name of the protein, without extra words (for example, 'VP4 protein' should be kept as 'VP4').

---
### 2. Copy the references obtained through the `ingest` workflow

Once you have run the `ingest` workflow and are back at the `inferred-root` folder, run:

```bash
cp ./ingest/data/references/annotation.gff3 ./dataset/genome_annotation.gff3
cp ./ingest/data/references/reference.fasta ./dataset/reference.fasta
cp ./ingest/data/references/reference.gbk ./resources/reference.gbk
```

---
### 3. Update `pathogen.json`
Modify `dataset/pathogen.json` to:
- Ensure file names match the generated reference files.
- Update attributes as needed.
- Adjust the Quality Control (QC) settings if necessary. If QC is not configured, Nextclade will not perform any checks.

For more details on configuration, refer to the [Nextclade documentation](https://docs.nextstrain.org/projects/nextclade/en/latest/user/input-files/05-pathogen-config.html).

---
### 4. Prepare `resources/reference.gbk`
- Modify protein names as needed to match your requirements.

---
### 5. Update the `Snakefile`
- Modify lines 1-18 to adjust paths and parameters.
- Ensure all necessary files for the Augur pipeline are present, including:
  - `sequences.fasta` & `metadata.tsv` 
    - can be downloaded from NCBI Virus via ingest: `FETCH_SEQUENCES==True`
  - [`auspice_config.json`](resources/auspice_config.json)
- These files are essential for building the reference tree and running Nextclade.

---

## Runnning the `Snakefile`
To create the auspice JSON and a Nextclade example dataset:
```bash
snakemake --cores 9 all
```

## Getting the inferred root
To create the static inferred root, extract the sequence named `>NODE_0000000`, from `./results/ancestral_sequences.fasta`.

```bash
seqkit grep -p "NODE_0000000" ./results/ancestral_sequences.fasta > ./results/inferred-root.fasta
```

If there are gaps, you can infer their states visualizing the files auspice.json and auspice_root-sequence.json in [auspice](auspice.us/). Replace the gaps with the more common nucleotide of the dataset. 

If there is a gap caused by an insertion in the 'RefSeq' used for obtaining this first approach (i. e. the same gap is present in all the sequences except the 'RefSeq', as found for Coxsackievirus A10), you will need to adjust the `dataset/genome_annotation.gff3` of the final dataset to the new coordinates, creating a new file with `start=start-X` and `end=end-X`, where `X` is the number fo gaps before the starting point of the polyprotein. For example, if there is a 3-gap in the 5'UTR, not affecting the amino acid positions, you can adjust the coordinates of the file creating a new file with `start=start-3` and `end=end-3`.

Save the final annotation file with the adjusted coordinates (if needed) to `results/inferred-root-annotation.gff3`.
