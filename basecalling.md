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
