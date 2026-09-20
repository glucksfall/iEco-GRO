# iEco-GRO-MEmodels
Public repository of Genomically Recoded Organism ME-models for *Escherichia coli* strains.

Pre-processing steps:
=====================

0. (Optional) Generate genomic sequence (fna) and protein sequences (faa) from GenBank:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace preprocess-step0.extract-fna-and-faa-from-genbank.ipynb`

1. Generate GenBank and protein.faa files from a sequence.fna file using PGAP:

`python3 pgap.ncbi/scripts/pgap.py -r -o pgap -g Ochre.fna -s 'Escherichia coli' --ignore-all-errors`

2. Execute BLASTp on both ways:

`blastp -query ASM584v2\ \(wildtype\)/protein.faa -subject ASM5499916v1\ \(Ochre\)/protein.faa -out blastp-org_as_db.txt -outfmt 6`
`blastp -query ASM5499916v1\ \(Ochre\)/protein.faa -subject ASM584v2\ \(wildtype\)/protein.faa -out blastp-org_as_db.txt -outfmt 6`

3. Generate a best-bidirectional hit mapping and new GenBank files:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace preprocess-step1.process-bbh-results.ipynb`

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace preprocess-step2.change-locus-tags-and-check-pseudogenes.ipynb`

Reconstruction steps:
=====================

1. Modify and execute for the first time step1.generate_files.ipynb with organism-specific information about tRNA-initiator Met(CAU), and ffs and rnpB genes:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace step1.generate_files.ipynb`

2. Copy and paste all 16 files from manual-curation into building_data, and re-run step1.generate_files.ipynb. This will correct automated-org-with-refs-tRNAs.xlsx and coralme_config.yaml files.

3. Reconstruct ME-models using step2.build-me-model.ipynb:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace step2.build-me-model.ipynb`

4. Check properties and simulate maximum growth rates using step3.check-ecolime-no-bbh-me-model.ipynb:

`jupyter nbconvert --ClearOutputPreprocessor.enabled=True --ExecutePreprocessor.timeout=None --allow-errors --to notebook --execute --inplace step3.check-ecolime-no-bbh-me-model.ipynb`

Additional analysis:
====================
- helper.check-gene-overlap-and-essentiality.ipynb:

Determine the number of pseudo genes in comparison with a reference sequence (ASM584v2)

Extract annotation from reference GenBank file for pseudo genes in organism.

Create and simulate M-models by knocking out pseudogenes. If growth rate is zero, it determines essential gene(s).
