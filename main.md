#### Chapter 0: metadata
First thing first, I made all metadata fit a certain format and got primers used for sequencing. The primers were for 18s-28s region and it was done with amplicon sequencing so possibly not all tools will do well. I also got the numbers of the samples on gel electrphoresis to verify correctness of the assembly. Also, some of the samples ae eDNA and other metagenomic samples, so certain tools that expect a single WGS organism sample won't fit either

#### Chapter 1: CAP3
I will start by running software tol recommended by RN - CAP3. I will work on icebox gridion prom server; the cap3_assembly folder will contain all work.

First, I will concatenate all files in each sample folder to get a single fasta file as all files are just capped at 4000 nucleotides.

```bash
cd /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/150224_RK/20240215_0639_X3_FAS90246_061fc2eb/fastq_pass

ls > folders.txt
for line in $(cat folders.txt); do
    echo "Processing folder: $line"
    zcat $line/* > /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly/files/${line}.txt
done
```

I removed unnecesary files (folder.txt unclassified.txt)
/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly/files/
ls > ../files.txt
cd ../
for line in $(cat files.txt); do
    echo "Processing file: $line"
    cap3.linux.x86_64/CAP3/cap3 /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly/files/${line}
done
mv files/*.txt.* ../result/

Nothing was assembled. May want to:
a) trim sequences
b) merge lines together
c) change settings (is there a setting for single end reads?)
