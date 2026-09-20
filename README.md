# iEco-GRO-MEmodels
Public repository for Genomically Recoded Organism ME-models of *Escherichia coli* strains.

Pre-processing steps:
=====================

(Optional) Generate genomic sequence (fna) and protein sequences (faa) from GenBank file:

Skip this step if data downloaded from NCBI is complete or contains at least an annotated GenBank and a protein.faa files.

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace preprocess-step0.extract-fna-and-faa-from-genbank.ipynb`

(Optional) Generate GenBank and protein.faa files from a sequence.fna file using PGAP:

Skip this step if GenBank is fully annotated, or if tRNAscan output is enough to complete annotation (use the tRNAscan_output option)

`python3 pgap.ncbi/scripts/pgap.py -r -o pgap -g Ochre.fna -s 'Escherichia coli' --ignore-all-errors`

To install PGAP, download the software from https://github.com/ncbi/pgap/releases/tag/2026-06-18.build8602 and follow instructions here https://github.com/ncbi/pgap/wiki/Installation

Other software that annotates genes and produce GenBank files are suggested.

PGAP ignore all errors option is used in the case of rE.coli-57 because the genome size is smaller than expected.

1. Execute BLASTp on both ways:

`blastp -query ASM584v2\ \(wildtype\)/protein.faa -subject ASM5499916v1\ \(Ochre\)/protein.faa -out blastp-org_as_db.txt -outfmt 6`
`blastp -query ASM5499916v1\ \(Ochre\)/protein.faa -subject ASM584v2\ \(wildtype\)/protein.faa -out blastp-ref_as_db.txt -outfmt 6`

2. Generate a best-bidirectional hit mapping dictionary and a new GenBank file compatible with the M-model:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace preprocess-step1.process-bbh-results.ipynb`

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace preprocess-step2.change-locus-tags-and-check-pseudogenes.ipynb`

Reconstruction steps:
=====================

1. Modify and execute for the first time step1.generate_files.ipynb with organism-specific information about tRNA-initiator Met(CAU) genes, and ffs and rnpB genes:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace step1.generate_files.ipynb`

2. Copy and paste all 16 files from manual-curation into each building_data subdirectories, and re-run step1.generate_files.ipynb. This will correct automated-org-with-refs-tRNAs.xlsx and coralme_config.yaml files.

3. Reconstruct ME-models using step2.build-me-model.ipynb:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace step2.build-me-model.ipynb`

4. Check models properties and simulate maximum growth rates using step3.check-ecolime-no-bbh-me-model.ipynb:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace step3.check-ecolime-no-bbh-me-model.ipynb`

Additional analysis:
====================

- helper.check-gene-overlap-and-essentiality.ipynb:

Determine the number of pseudogenes in comparison with a reference sequence (ASM584v2)

Extract annotation from reference GenBank file for pseudogenes in an organism.

Create and simulate M-models by knocking out pseudogenes. If growth rate is zero, it determines essential gene(s).
