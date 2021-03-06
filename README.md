# musclebeachtools
---
This package used to analyze neuronal data, from basic plotting
 (ISI histograms, firing rate, cross correlation),
 to hard core computational work (ising models, branching ratios, etc).  

---
## Installation

### Download musclebeachtools
```
git clone https://github.com/hengenlab/musclebeachtools_hlab.git   
```
### Using pip
```
cd locationofmusclebeachtools_hlab/musclebeachtools/  
pip install .
# In Linux and windows
pip install xgboost
# In mac, install brew,gcc then install xgboost
bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
brew install gcc@5
conda install -c conda-forge xgboost
```

---
### Installation by adding to path

##### Windows
```
My Computer > Properties > Advanced System Settings > Environment Variables >  
In system variables, create a new variable  
    Variable name  : PYTHONPATH  
    Variable value : location where musclebeachtools_hlab is located  
    Click OK
```

##### Linux
```
If you are using bash shell  
In terminal open .barshrc or .bash_profile  
add this line  
export PYTHONPATH=/location_of_musclebeachtools_hlab:$PYTHONPATH
```

##### Mac
```
If you are using bash shell  
In terminal cd ~/  
then open  .profile using your favourite text editor  
add this line  
export PYTHONPATH=/location_of_musclebeachtools_hlab:$PYTHONPATH
```

---
### Test import
```
Open powershell/terminal     
    ipython    
    import musclebeachtools.musclebeachtools as mbt   
```
---

## Usage

#### Load data from ntksortingb
```
import musclebeachtools as mbt

datadir = "/hlabhome/kiranbn/neuronclass/t_lit_EAB50final/"
n1 = mbt.ksout(datadir, filenum=0, prbnum=4, filt=[])
```


#### Load spike amplitudes from spikeinteface output
```
import numpy as np
import musclebeachtools as mbt
n = np.load('neurons_group0.npy', allow_pickle=True)
n_amp = mbt.load_spike_amplitudes(n, '/home/kbn/amplitudes0.npy')
# For 4th neuron, by neuron.clust_idx
n_amp[4].spike_amplitude
```

#### Usage of properties and functions
```
# Get basic info of a neuron, here 6th in list n1
print(n1[6])
Neuron with (clust_idx=6, quality=1, peak_channel=6)

# Get sampling rate for 4th neuron
n1[4].fs

# Get sample time for 4th neuron
n1[4].spike_time

# Get spike time in seconds for 4th neuron
n1[4].spike_time_sec

# Get spike time in seconds for 4th neuron from on off times
n1[4].spike_time_sec_onoff

# Other properties
n1[4].start_time
n1[4].end_time
n1[4].on_times
n1[4].off_times
n1[4].peak_channel
n1[4].quality
n1[4].qual_prob
n1[4].region
n1[4].birthday
n1[4].sex
n1[4].species
n1[4].region
n1[4].waveform
n1[4].waveform_tetrodes
n1[4].waveforms
n1[4].mean_amplitude
n1[4].cell_type
n1[4].peaklatency
n1[4].clust_idx

# Plot mean waveform of 4th neuron
n1[4].plot_wf()

# Plot isi of 4th neuron
#  start : Start time (default self.start_time)
#  end : End time (default self.end_time)
#  isi_thresh : isi threshold (default 0.1)
#  nbins : Number of bins (default 101)
#  lplot : To plot or not (default lplot=1, plot isi)
#  lonoff : Apply on off times (default on, 1)
n1[4].isi_hist(start=False, end=False, isi_thresh=0.1, nbins=101,
               lplot=1, lonoff=1)

# plot firing rate of 4th neuron
# binsz : Bin size (default 3600)
# start : Start time (default self.start_time)
# end : End time (default self.end_time)
# lplot : To plot or not (default lplot=1, plot firing rate)
# lonoff : Apply on off times (default on, 1)
n1[4].plotFR(binsz=3600, start=False, end=False,
             lplot=1, lonoff=1)

# Calculate presence ratio
# nbins : Number of bins (default 101)
# start : Start time (default self.start_time)
# end : End time (default self.end_time)
# lonoff : Apply on off times (default on, 1)
n1[4].presence_ratio(nbins=101, start=False, end=False,
                     lonoff=1)

# Calculate isi contamination at various thresholds, 2 and 4 ms
# cont_thresh_list : threshold lists for calculating isi contamination
# time_limit : count spikes upto, default np.inf. Try also 100 ms, 0.1
# start : Start time (default self.start_time)
# end : End time (default self.end_time)
# lonoff : Apply on off times (default on, 1)
n1[4].isi_contamination(cont_thresh_list=[0.002, 0.004], time_limit=np.inf)
                        start=False, end=False, lonoff=1)

# Check quality and its probability from autoqual (see below).
print(n[2].quality, "", n[2].qual_prob[n[2].quality - 1])

# Change quality of neuron n[0] to 1
# qual : Quality values should be 1, 2, 3 or 4
#        1 : Good
#        2 : Good but some contamination
#        3 : Multiunit contaminated unit
#        4 : Noise unit
# qual_prob array with probabilities for each quality from autoqual.
# When quality is assigned manualy qual_prob is set to 100% for that quality
n[0].set_qual(1)

# get spiketimes from all neurons in n1 as a list
spiketimes_list = n_getspikes(n1)

# Get spikewords from all neurons in n as a list
import numpy as np
import musclebeachtools as mbt
n = np.load('neurons_group0.npy', allow_pickle=True)
sw = mbt.n_spiketimes_to_spikewords(n)

# Set on off times
n[2].set_onofftimes_from_list([0, 3600], [900, 7200])
print(n[2].on_times)
[0, 3600]
print(n[2].off_times)
[900, 7200]

# Change quality with plot of ISI, Firingrate and Waveform
import numpy as np
import musclebeachtools as mbt
n = np.load('neurons_group0.npy', allow_pickle=True)

# binsz : Bin size (default 3600)
# start : Start time (default self.start_time)
# end : End time (default self.end_time)
# lsavepng : Save checkqual results as png's
# png_outdir : Directory to save png files
#              if lsavepng=1 and png_outdir=None
#              png's will be saved in current working directory
# fix_amp_ylim : default 0, yaxis max in amplitude plot.
#                For example can be fix_amp_ylim=500 to see from 0 to 500
#                in amplitude plot.
n[2].checkqual(binsz=3600, start=False, end=False, lsavepng=0,
               png_outdir=None, fix_amp_ylim=0)

# Check quality is changed also there is a log from checkqual
print(n[2].quality)

# Find quality using xgboost
import numpy as np
import musclebeachtools as mbt
# Load neurons
neurons = \
    np.load('H_2020-04-09_09-11-37_2020-04-10_01-06-37_neurons_group0.npy',
            allow_pickle=True)

# Find quality
# neuron_list : List of neurons from (usually output from ksout)
# model_file : model file with path
mbt.autoqual(neurons, '/media/HlabShare/models/xgb_model')

# Verify quality is correct using checkqual
# lsavepng : Save checkqual results as png's, default 0
# png_outdir : Directory to save png files
#              if lsavepng=1 and png_outdir=None
#              png's will be saved in current working directory
# fix_amp_ylim=0 : Default off
# fix_amp_ylim=500, to change amplitude plot ylim from 0 to 500.
for neuron in neurons:
    neuron.checkqual(savepng=0, png_outdir=None, fix_amp_ylim=500)

# Remove spikes for neuron with large amplitudes
# Default method based on standard deviation, for example 1.5
n[4].remove_large_amplitude_spikes(1.5, lstd_deviation=True, start=False, end=False, lplot=True)
# Based on threshold value, for example 1000
n[4].remove_large_amplitude_spikes(1000, lstd_deviation=False, start=False, end=False, lplot=True)
If you are sure and do not want to check plots
 to confirm change lplot=False (not recommended)

# Update behavior
# behavior has a value of np.zeros(2,6) by default to update behaviour of all cells
# behavior is 2d array with first one with time in each state and second one with
# behavioral states,
n[0].update_behavior(behavior))

# Save a modified neuron list
mbt.n_save_modified_neuron_list(n, '/home/kbn/neuron_mod.npy')

# Plot all waveforms in a neuron list
# maxqual filter by quality, list
# plot waveforms for neurons with quality in maxqual
# To see plot
mbt.n_plot_neuron_wfs(n, maxqual=[1, 2, 3, 4], pltname="block1")
# To save plot
mbt.n_plot_neuron_wfs(n, maxqual=[1], pltname="block1",
                      saveloc='/home/kbn/')

# To create neuron list from spikeinteface output folder in spikeinterface environmnet
import numpy as np
import glob
import musclebeachtools as mbt
from datetime import datetime
n = mbt.mbt_spkinterface_out('/home/kbn/co/',
                             '/media/HlabShare/models/xgboost_autoqual_prob',
                             sex='m', birthday=datetime(1970, 1, 1, 00, 00),
                             species='m',
                             animal_name='ABC12345',
                             region_loc='CA1',
                             genotype='te4',
                             expt_cond='monocular deprivation')
nnew[0].species
'm'

nnew[0].region
'CA1'

nnew[0].animal_name
'ABC12345'

nnew[0].sex
'm'

nnew[0].birthday
datetime.datetime(1970, 1, 1, 0, 0)

nnew[0].age_rec # age based on first file in sorting block
datetime.timedelta(days=-18625, seconds=2958)

nnew[0].genotype
'te4'

nnew[0].expt_cond
'monocular deprivation'

```

## FAQ
```
1. spike_time vs spike_time_sec
Property spike_time is in sample times.
To to get spike time in seconds
please use spike_time_sec, n1[4].spike_time_sec
or
For example for 4th neuron n1[4].spike_time/n1[4].fs
Also spike_time_sec_onoff filters spike_time_sec based on on/off times

```

## Issues

```Please slack Kiran ```
---
