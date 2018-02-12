# ndpull

Python 3 command line program to download data from NeuroData.  Can download a full stack of data or specify limits.  View available data at [ndwebtools](https://ndwebtools.neurodata.io/) or [neurodata.io](https://neurodata.io/)

## Install

### From PyPI

1. `pip install ndpull`

### From github (latest development version)

1. `git clone https://github.com/neurodata/ndpull.git`
1. `virtualenv env -p python3`
    1. LINUX: `env/bin/activate`
    1. WIN: `.\env\Scripts\activate` (Change permissions if on Powershell: [guide](https://virtualenv.pypa.io/en/stable/userguide/#activate-script))
1. `pip install -r requirements.txt`

## Config

1. generate a [Boss API key](https://api.boss.neurodata.io/v1/mgmt/token) and save it to file named `neurodata.cfg` (example provided: [neurodata.cfg.example](neurodata.cfg.example))

## Run

### Command line

```dos
> sliceDownload --help

usage: stackDownload.py [-h] [--config_file CONFIG_FILE] [--token TOKEN]
                        [--url URL] [--collection COLLECTION]
                        [--experiment EXPERIMENT] [--channel CHANNEL]
                        [--x X X] [--y Y Y] [--z Z Z] [--res RES]
                        [--outdir OUTDIR] [--full_extent] [--print_metadata]

optional arguments:
  -h, --help            show this help message and exit
  --config_file CONFIG_FILE
                        User config file for BOSS
  --token TOKEN         User token for the boss (not used if config file
                        specified)
  --url URL             URL to boss endpoint (not used if config file
                        specified)
  --collection COLLECTION
                        Collection
  --experiment EXPERIMENT
                        Experiment
  --channel CHANNEL     Channel
  --x X X               X range for stack
  --y Y Y               Y range for stack
  --z Z Z               Z range for stack
  --res RES             Stack resolution
  --outdir OUTDIR       Path to output directory.
  --full_extent         Use the full extent of the data on the BOSS
  --print_metadata      Prints the metadata on the
                        collection/experiment/channel and quits
```

### Within Python

```python
from ndpull import sliceDownload

collection = 'kharris15'
experiment = 'apical'
channel = 'em'

# see neurodata.cfg.example to generate your own
config_file = 'neurodata.cfg'

# print metadata
meta = sliceDownload.BossMeta(collection, experiment, channel)
token, boss_url = sliceDownload.get_boss_config(config_file)
rmt = sliceDownload.BossRemote(boss_url, token, meta)
print(rmt)

# download slices with these limits:
x = [0, 512]
y = [500, 1000]
z = [0, 10]

# returns a namespace as a way of passing arguments
result = sliceDownload.parse_input_args(
    collection, experiment, channel, config_file, x=x, y=y, z=z, res=0, outdir='./')

# downloads the data
sliceDownload.download_slices(result, rmt)
```
