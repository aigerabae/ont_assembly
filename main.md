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
    cat $line/* > /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly/${line}.txt
done
```
