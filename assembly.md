Trimming:
```
NF=$OUT/near_full
python3 $T/trim_filter.py --demux $D --map $T/barcode_sample_map.csv --require-primer --threads 16 \
  --only $(printf "barcode%02d " {1..12})  --min-len 1000 --max-len 1250 --out $NF/plants_5765
python3 $T/trim_filter.py --demux $D --map $T/barcode_sample_map.csv --require-primer --threads 16 \
  --only $(printf "barcode%02d " {13..24}) --min-len 1100 --max-len 1350 --out $NF/plants_5756
python3 $T/trim_filter.py --demux $D --map $T/barcode_sample_map.csv --require-primer --threads 16 \
  --project eDNA        --min-len 1000 --max-len 1800 --out $NF/eDNA
python3 $T/trim_filter.py --demux $D --map $T/barcode_sample_map.csv --require-primer --threads 16 \
  --project human_feces --min-len 900  --max-len 1800 --out $NF/feces
```

Installing:
```
deactivate 2>/dev/null
T=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
cd $T

tmux new -s install
```

Inside it:
```
T=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
cd $T
curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
$T/bin/micromamba --version

export MAMBA_ROOT_PREFIX=$T/mamba
$T/bin/micromamba create -n consensus -c conda-forge -c bioconda -y \
  python=3.10 "medaka=1.11.3" racon abpoa minimap2 samtools edlib biopython matplotlib

micromamba install -n consensus -c conda-forge -y "setuptools<70"
```

Checking installation:
```
eval "$($T/bin/micromamba shell hook -s bash)"
micromamba activate consensus
medaka --version
medaka tools list_models | grep -o r941_min_sup_g507
```

amplicon_sorter:
```
T=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
cd $T
git clone https://github.com/avierstr/amplicon_sorter
python3 $T/amplicon_sorter/amplicon_sorter.py -v
```

NGSSpeciesID:
```
T=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
cd $T
curl -LO https://github.com/ksahlin/NGSpeciesID/releases/download/v0.5.0/NGSpeciesID-v0.5.0-linux-x86_64.tar.gz
tar xzf NGSpeciesID-*.tar.gz
chmod +x NGSpeciesID-*/NGSpeciesID
ls $T/NGSpeciesID-*/NGSpeciesID
```

Check everything:
```
abpoa --version
racon --version
minimap2 --version
samtools --version | head -1
medaka --version
medaka tools list_models | grep -o r941_min_sup_g507
$T/NGSpeciesID-*/NGSpeciesID --version
python3 $T/amplicon_sorter/amplicon_sorter.py -v
```

Run:
```
tmux new -s consensus
```

```
T=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
OUT=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/dorado_basecaling
NF=$OUT/near_full
C=$OUT/consensus
cd $OUT                                   # stand somewhere safe
export MAMBA_ROOT_PREFIX=$T/mamba
eval "$($T/bin/micromamba shell hook -s bash)"
micromamba activate consensus
export AMPLICON_SORTER=$T/amplicon_sorter/amplicon_sorter.py
export NGSPECIESID=$T/NGSpeciesID-v0.5.0-linux-x86_64/NGSpeciesID

rm -rf $C/plants_5765                     # clear the failed attempt

JOBS=12 bash $T/run_consensus.sh $NF/plants_5765 $C/plants_5765 1000 1250 10 2>&1 | tee $C/run_plants_5765.log
JOBS=12 bash $T/run_consensus.sh $NF/plants_5756 $C/plants_5756 1100 1350 10 2>&1 | tee $C/run_plants_5756.log
JOBS=14 bash $T/run_consensus.sh $NF/eDNA        $C/eDNA        1000 1800 10 2>&1 | tee $C/run_eDNA.log
JOBS=16 bash $T/run_consensus.sh $NF/feces       $C/feces        900 1800 9  2>&1 | tee $C/run_feces.log
```


OUT=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/dorado_basecaling
D=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/dorado_basecaling/demux/no_sample/20240215_0644_0_FAS90246_061fc2eb/fastq_pass
T=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/tools
python3 $T/primer_scan.py $D/barcode67 | column -t
