```errors
(base) aygera@aygera-HP-Z6-G4-Workstation:~/Downloads/RN_ONT$ cp -r 15022024/ ~/biostar/RN/
cp: error reading '15022024/150224_RK_fasta/barcode64/FAS90246_pass_barcode64_061fc2eb_41692f9c_35_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode24/FAS90246_pass_barcode24_061fc2eb_41692f9c_16_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode36/FAS90246_pass_barcode36_061fc2eb_41692f9c_0_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode19/FAS90246_pass_barcode19_061fc2eb_41692f9c_3_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode19/FAS90246_pass_barcode19_061fc2eb_41692f9c_13_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode38/FAS90246_pass_barcode38_061fc2eb_41692f9c_9_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode38/FAS90246_pass_barcode38_061fc2eb_41692f9c_3_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_fasta/barcode38/FAS90246_pass_barcode38_061fc2eb_41692f9c_12_reads.fasta': Input/output error
cp: error reading '15022024/150224_RK_metaFlye-20240301T075028Z-001/barcode70/20-repeat/read_alignment_dump': Input/output error
```

I will run CAP3 on fastq data to assemble it again.
After clearing up some question, here are the objectives:
1) because it's mostly eDNA, the problematic barcodes are the ones that made 1 big sequence, rather than many small ones
2) need to use software that allows fir multiple sequences with multiplex amplicons
3) the end result needs to be consistent with lines showin on gel electrophoresis
4) only use barodes 1 to 70 as bases were not called for the rest of them
5) need to merge all fastq files per barcode (because each contains only up to 4000 reads)

Worflow:
1) sequencing = MinKNOW (ONT device control software) -> fast5 files (squiggle data / chromatogram peaks / raw data)
2) basecalling = Dorado (integrated with MinKNOW) -> fastq (raw sequences with quality scores)


Options:
- https://epi2me.nanoporetech.com/wfindex/ = doesn't work because their amplicon assembly mode works only for a single amplicon per barcode
- https://github.com/GaetanBenoitDev/metaMDBG = state of the art tool for metagenome assembly
- SemiBin2 and other binnign software tools = don't use it; only suitable for assemblying near whole genomes from eDNA assembled contigs
