# ndpull

Python 3 command line program to download data from NeuroData.  Can download a full stack of data or with specific limits.  View available data at [ndwebtools](https://ndwebtools.neurodata.io/) or [neurodata.io](https://neurodata.io/)

## Install

### From github (latest development version)

`pip install git+git://github.com/neurodata/ndpull.git`

## Config

Generate a [Boss API key](https://api.boss.neurodata.io/v1/mgmt/token) and save it to file named `neurodata.cfg` (example provided: [neurodata.cfg.example](neurodata.cfg.example))

## Run

### Command line

```sh
> ndpull --help
usage: ndpull [-h] [--config_file CONFIG_FILE] [--token TOKEN] [--url URL]
              [--collection COLLECTION] [--experiment EXPERIMENT]
              [--channel CHANNEL] [--x X X] [--y Y Y] [--z Z Z] [--res RES]
              [--outdir OUTDIR] [--full_extent] [--print_metadata]
              [--threads THREADS] [--iso]

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
  --threads THREADS     Number of threads for downloading data.
  --iso                 Returns iso data (for downsampling in z)
```

To run

```sh
> ndpull --config_file neurodata.cfg --collection kharris15 --experiment apical --channel em --x 4096 4608 --y 4608 5120 --z 90 100 --outdir .
```

### Python

```python
from ndpull import ndpull

collection = 'kharris15'
experiment = 'apical'
channel = 'em'

# see neurodata.cfg.example to generate your own
config_file = 'neurodata.cfg'

# print metadata
meta = ndpull.BossMeta(collection, experiment, channel)
token, boss_url = ndpull.get_boss_config(config_file)
rmt = ndpull.BossRemote(boss_url, token, meta)
print(rmt) # prints metadata

# download slices with these limits:
x = [4096, 4608]
y = [4608, 5120]
z = [90, 100]

# returns a namespace as a way of passing arguments
result = ndpull.collect_input_args(
    collection, experiment, channel, config_file, x=x, y=y, z=z, res=0, outdir='./')

# validate args
result, rmt = validate_args(args)

# downloads the data
ndpull.download_slices(result, rmt)
```
