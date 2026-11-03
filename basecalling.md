Re-running basecalling to see if newer version of dorado rescues some reads (but since its r9 the last supported version is  dorado-0.9.6)  

Opening server:
```
ssh prom@10.1.131.183
cd /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/150224_RK/20240215_0639_X3_FAS90246_061fc2eb
```

Installation:
```
TOOLS=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
cd $TOOLS
wget https://cdn.oxfordnanoportal.com/software/analysis/dorado-0.9.6-linux-x64.tar.gz
tar -xzf dorado-0.9.6-linux-x64.tar.gz
export PATH=$TOOLS/dorado-0.9.6-linux-x64/bin:$PATH                                        # this adds it to path but only in the current session
dorado --version
```

New venv:
```
cd /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
python3 -m venv pod5_env
source pod5_env/bin/activate
pip install pod5
pod5 --version
```

Configuring paths again:
```
TOOLS=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
RUN=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/150224_RK/20240215_0639_X3_FAS90246_061fc2eb
OUT=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/dorado_basecaling

mkdir -p $OUT
export PATH=$TOOLS/dorado-0.9.6-linux-x64/bin:$PATH
```

Converting to pod5:
```
source /data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools/pod5_env/bin/activate
cd $RUN
pod5 convert fast5 fast5_pass/ fast5_fail/ -r --output $OUT/pod5/ --one-to-one ./ -t 16
```

Checking results:
```
find $OUT/pod5 -name "*.pod5" | wc -l    # should be 1333
```

Opening tmux:
```
tmux new -s basecall
```

Inside it doing basecalling:
```
TOOLS=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
OUT=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/dorado_basecaling
export PATH=$TOOLS/dorado-0.9.6-linux-x64/bin:$PATH
export CUDA_DEVICE_ORDER=PCI_BUS_ID

dorado --version                      # sanity check that it's installed
mkdir -p $TOOLS/models
dorado download --model dna_r9.4.1_e8_sup@v3.6 --models-directory $TOOLS/models

dorado basecaller $TOOLS/models/dna_r9.4.1_e8_sup@v3.6 $OUT/pod5/ -r \
  --device cuda:1,2,3,4 \
  --kit-name SQK-RBK110-96 \
  > $OUT/calls_sup.bam 2> $OUT/basecall.log
```

Checking results:
```
samtools quickcheck -u $OUT/calls_sup.bam && echo "BAM OK"
```

Splitting by barcode and summarizing:
```
dorado demux --no-classify --emit-fastq --output-dir $OUT/demux/ $OUT/calls_sup.bam
dorado summary $OUT/calls_sup.bam > $OUT/summary.tsv
```

Then count reads per barcode to compare with the original HAC run:
```
find $OUT/demux -name "*.fastq" | head
for f in $OUT/demux/*.fastq; do echo -e "$(basename $f)\t$(( $(wc -l < $f) / 4 ))"; done | sort -k2,2nr
```
