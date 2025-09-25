# Inferred Ancestral Root Sequence for &lt;your virus&gt;

This repository provides a reproducible workflow for generating an inferred ancestral ("root") sequence for &lt;your virus&gt;, designed for use as a custom reference in [Nextclade](https://clades.nextstrain.org/) analyses.


## Overview

Phylogenetic analyses, such as those performed by Nextclade and Augur, benefit from a high-quality, dataset-representative reference sequence. This workflow infers such a root by reconstructing the ancestral sequence from your entire  dataset, allowing for more accurate mutation and clade assignments.

**Workflow summary:**
1. **Phylogenetic Tree Construction:** All sequences in the provided dataset are aligned and a maximum-likelihood tree is built.
2. **Ancestral Sequence Inference:** The [Augur](https://github.com/nextstrain/augur) toolkit is used to infer the ancestral (root) sequence, labeled `NODE_0000000` in the output FASTA.
3. **Gap Correction:** The script [`fix_root_gaps.py`](scripts/fix_root_gaps.py) replaces any gaps (`-`) or ambiguous bases (`N`) in the inferred root with the corresponding reference nucleotides, ensuring a contiguous and biologically plausible root sequence. *For some viruses, it may make more sense to use the most common nucleotide in the alignment — please adapt the script for your needs.*
4. **Export:** The cleaned ancestral root FASTA is available for use as a custom reference in Nextclade. The corresponding metadata is also provided and should be kept in sync.


## Getting Started
### Running the Workflow

To generate the inferred ancestral root:

```bash
snakemake --cores 9 all
```

This will:
- Build a phylogenetic tree and infer the ancestral root
- Extract and gap-correct the root sequence (`NODE_0000000`)
- Output a finalized FASTA and updated metadata

### Output Files

- `results/ancestral_sequences.fasta`: All inferred ancestral sequences (from Augur)
- `resources/inferred_root.fasta`: Gap-corrected ancestral root sequence


## Updating the Ancestral Root or Metadata

If you update your dataset or rerun the workflow:
- **Be sure to also update the corresponding [metadata file](../resources/static_inferred_root_metadata.tsv)**  to match the new root sequence.
- Record the date of the latest update (see below).

**Latest root sequence generated:**  
📅 ...


---
## FAQ

**Q: Why use a static inferred root for Nextclade?**  
A: Using an inferred ancestral root sequence representative of your dataset improves mutation calling and clade assignment, especially for highly variable viruses like Enteroviruses.

**Q: How is the root sequence cleaned?**  
A: Gaps or ambiguous bases in the inferred root are replaced positionally with nucleotides from the reference sequence using [`fix_root_gaps.py`](scripts/fix_root_gaps.py). For some viruses (e.g., CVA10), it may be preferable to use the most common nucleotide at each position — please adapt the script as needed!

**Q: Can I use this approach for other enteroviruses?**  
A: Yes! For a ready-to-use template, see [enterovirus-phylo/dataset-template-inferred-root](https://github.com/enterovirus-phylo/dataset-template-inferred-root).

## Author & Contact

- Maintainers: Nadia Neuner-Jehle, Alejandra Gonzalez Sanchez and Emma B. Hodcroft ([hodcroftlab](https://github.com/hodcroftlab))
- For questions or suggestions, please write an email: eve-group[at]swisstph.ch
