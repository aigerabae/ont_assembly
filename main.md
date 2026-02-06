ssh prom@10.1.131.183
cd /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/

This will add color to the command line prompt but only in the current session:
```bash
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
```

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
```bash
cd /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly/files/
ls > ../files.txt
cd ../
for line in $(cat files.txt); do
    echo "Processing file: $line"
    cap3.linux.x86_64/CAP3/cap3 /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly/files/${line}
done
mv files/*.txt.* ../result/
```

Nothing was assembled. May want to:
a) trim sequences
b) merge lines together
c) change settings (is there a setting for single end reads?)

Nevermind. It just needs fasta, not fastq. I will try to find a pipeline from fastq to fasta with qual file of scores 

Fastq to fasta + qual:
```python
from Bio import SeqIO

records = SeqIO.parse("barcode01.txt", "fastq")
count = SeqIO.write(records, "barcode01.qual", "qual")

records = SeqIO.parse("barcode01.txt", "fastq")
fasta = SeqIO.write(records, "barcode01.fa", "fasta")

print("Converted %i records" % count)
```

It started running barcode01 at 4 February 2026 around 13:00 and finished running 5 February 2026 22∶26∶50. Now I want to veiw the results with python. For that, I need to install jupyter lab. I did it via conda to biostar conda env but it didn't work. I then tried to create a virtual environment and do it there:
```bash
cd /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/cap3_assembly
python3 -m venv jupyter_env
source jupyter_env/bin/activate
pip install jupyterlab
jupyter lab --no-browser --port=8080
ssh -L 8080:localhost:8080 prom@10.1.131.183
```

Then in broser go:
```
http://localhost:8080/
```

Turns out, i assembled most only to 60 nucleotides. Migh want to rerun it on other barcodes and then run it again

#### Chapter 2: LACA wokflow
from here:
https://pmc.ncbi.nlm.nih.gov/articles/PMC12160608/#s0008   
https://github.com/yanhui09/laca  

Installing
```bash
docker pull yanhui09/laca
```

```bash
docker run -it -v `pwd`:/home --privileged yanhui09/laca
laca init -x barcode01/ --ont -w laca_result/ -d
laca run all -j 20 -w laca_result/                                    
```

