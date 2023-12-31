# Nortek Signature VM Ingestion Pipeline

This pipeline reads in the .SigVM files output from the Nortek SignatureVM data acquisition software. It 
is currently set up to read in water-track, bottom-track, echo sounder, altimeter and acoustic surface 
tracking variables, all of which are automatically set on by the software. You may need to remove some 
variables if they aren't saved, as detailed below.

This README file contains instructions for running and testing this pipeline. Datafiles are saved under
"./storage/root/data" in netCDF4 and MATLAB file formats. Velocity, amplitude and correlation plots are 
saved in the corresponding "./storage/root/ancillary" folder.

The Advanced Navigation Compass in the Nortek Signature VM acquisition software should be set 
to the channel outputting NMEA (typically 9001 or 9002), not "ANPP" (Advanced Navigation Packet Protocol, 
an AdNav custom format), which can be set from the Compass's internal html webpage. The GGA, VTG, RMC, and
HDT sentences should be active and set to at least 2 Hz.

============================================================================================================

## Prerequisites

* Ensure that your development environment has been set up according to
[the instructions](../../README.md#development-environment-setup).

> **Windows Users** - Make sure to run your `conda` commands from an Anaconda prompt OR from a WSL shell with miniconda
> installed. If using WSL, see [this tutorial on WSL](https://tsdat.readthedocs.io/en/latest/tutorials/wsl.html) for
> how to set up a WSL environment and attach VS Code to it.

* Make sure to activate the tsdat-pipelines anaconda environment before running any 
commands:  `conda activate tsdat-pipelines`

=============================================================================================================

## Editing pipeline data fields
This pipeline is set up to handle data created by a Nortek Signature1000 VM running both bottom track and the
echo sounder. It also has some basic parameters set up for sampling in Sequim Bay.

1. If you are not running the echo sounder, navigate to `pipelines/sigvm/config` and open `retriever.yaml` 
and `dataset.yaml`. Remove all entries that have the `_echo` tag. 

2. There are a number of parameters listed in `retriever.yaml` in lines 15-18 that should be updated.

    a. "depth_offset" is the distance below the waterline that the ADCP transducers sit

    b. "salinity" is the water salinity, which ranges around 31 for the channel into Sequim Bay

    c. "magnetic_declination" is the current magnetic declination. You can look this up online.

3. In `dataset.yaml`, update the information under the `attrs` block per the data collection specifics.

4. There is one parameter listed in `shared/quality.yaml` that can be updated:

    a. "correlation_threshold", on line 71, is for a QC test that removes velocity data below a certain 
    percent (%) acoustic signal correlation.

=============================================================================================================

## Running your pipeline
This section shows you how to run the ingest pipeline created by the template.  Note that `{ingest-name}` refers
to the pipeline name you typed into the template prompt, and `{location}` refers to the location you typed into
the template prompt.

1. Make sure to be in the `acoustic_Doppler_pipelines` folder

2. Run the runner.py with your test data input file as shown below:

```bash
cd $PATH_TO_PIPELINE/acoustic_Doppler_pipelines
conda activate tsdat-pipelines # <-- you only need to do this the first time you start a terminal shell
python runner.py $PATH_TO_DATA/{filename}.SigVM
```

3. Finally, data and plots are saved in `acoustic_Doppler_pipelines/storage/root/`
