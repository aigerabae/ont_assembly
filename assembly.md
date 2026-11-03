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
micromamba activate consensus
export AMPLICON_SORTER=$T/amplicon_sorter/amplicon_sorter.py
export NGSPECIESID=$(ls $T/NGSpeciesID-*/NGSpeciesID)
OUT=/data/adaniyarov/ds1821p_IV_nanopore_data/icebox_gridION_70/dorado_basecaling
ls $OUT/near_full/plants_5765/*.fastq.gz | head -3     # should list plants_01... files
bash $T/run_consensus.sh $OUT/near_full/plants_5765 $OUT/consensus/plants_5765 1000 1250 16
```
