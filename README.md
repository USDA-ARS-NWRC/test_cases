# AWSM Test Cases

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/USDA-ARS-NWRC/awsm_test_cases/master)

This repository contains instructions for installing the Docker container of AWSM and running short test cases. The instructions involve installing the supporting software and the AWSM system. The test cases include three model tests:
1. Reynolds Mountain East in the Reynolds Creek Experimental Watershed for the entire WY 2006 with wind redistribution of precipitation
2. Boise River Basin three month simulation during WY 2017 for a historic snowmelt event
3. Tuolumne River Basin two day simulation during WY 2016, which includes an lidar snow depth update from Airborne Snow Observatory

## Table of contents
- [AWSM Test Cases](#awsm-test-cases)
  - [Table of contents](#table-of-contents)
  - [Repository contents](#repository-contents)
- [Downloading this repository](#downloading-this-repository)
- [Docker setup](#docker-setup)
  - [Installing Docker](#installing-docker)
  - [Installing `docker-compose`](#installing-docker-compose)
  - [Compute resources for docker](#compute-resources-for-docker)
- [NetCDF viewers](#netcdf-viewers)
  - [Windows](#windows)
  - [Mac and Linux](#mac-and-linux)
- [Running the test cases](#running-the-test-cases)
  - [Reynolds Mountain East, Idaho (RME)](#reynolds-mountain-east-idaho-rme)
  - [Boise River Basin, Idaho (BRB)](#boise-river-basin-idaho-brb)
  - [Tuolumne River Basin, California](#tuolumne-river-basin-california)
- [Jupyter Lab for analysis](#jupyter-lab-for-analysis)
  - [Linux and Mac](#linux-and-mac)
  - [Windows](#windows-1)

## Repository contents
- **brb/:** all files necessary to run the Boise River Basin test case
- **tuolumne/:** all files necessary to run the Tuolumne River Basin test case
- **rme/:** all files necessary to run the Reynolds Mountain East test case
- **output/:** contains output from the test cases
- **README.md:** documentation on running the test cases
- **awsm_docker:** python script wrapping the docker compose calls for each test case
- **docker-compose.yml:** Jupyter Lab analysis
- **docker-compose.windows.yml:** Jupyter Lab analysis for Windows


# Downloading this repository
If you have git installed, you can clone the `awsm_test_cases` repository with this command
```
git clone https://github.com/USDA-ARS-NWRC/awsm_test_cases.git
```
For users without git installed, go to https://github.com/USDA-ARS-NWRC/awsm_test_cases. Click on the green "Clone or download" button, download the zip file, and unzip it.

Make sure you have a Terminal open. For **Windows** users, use PowerShell, which should come installed on your computer. Move into the folder where the unzipped repository is located. Next, move into the repository. If the repository was downloaded and unzipped, this will involve `cd awsm_test_cases-master/`. If it was cloned with Git, this will involve `cd awsm_test_cases/`.

Type `ls` in the command prompt and make sure you see the correct contents of
the repository, as listed in the [directory contents](#directory-contents) section.

# Docker setup
Our modeling system is isolated in a Docker container in order to run consistently on multiple operating systems. The source code is contained and installed within a Docker image, isolating it from the host computer and handling almost all system software dependencies.

In order to run AWSM, you will need to install Docker and understand the basics of running a Docker container. For a more in depth information, read https://docs.docker.com/engine/docker-overview/. 

>**NOTE: Docker requires that you are a system administrator for your computer or have sudo/administrative privileges.**

## Installing Docker

* [Window installation instructions](https://docs.docker.com/docker-for-windows/install/)
* [Mac installation instructions](https://docs.docker.com/docker-for-mac/install/)
* [Ubuntu installation instruction](https://docs.docker.com/install/linux/docker-ce/ubuntu/) and many other Linux distributions are supported

>**NOTE: Windows/Mac users need to ensure that Docker Desktop is running and configured prior to running test cases. Enabling shared drives is required to run the test cases.**

## Installing `docker-compose`

For ease of use, we use `docker-compose` to handle the mounting of folders between the Docker container and the host computer. Mounting folders between the two systems is required to share the input and output data from the model. Read the [documentation](https://docs.docker.com/compose/install/) for further installation instructions.

* Windows already includes `docker-compose`
* Mac already includes `docker-compose`
* Linux [latest install instructions](https://docs.docker.com/compose/install/)

## Compute resources for docker
The AWSM test cases have been optimized to limit the computational burden but increasing the computational resources will make the simulations run faster. On Windows and Mac, docker is only allowed limited compute resources from your computer and you will need to provide more resources to Docker for AWSM to function properly. We recommend at a minimum the following computer specifications:
* 2 cores
* 16GB of RAM
* 20GB of hard drive space if running all test cases

# NetCDF viewers

The output files from [SMRF](https://github.com/USDA-ARS-NWRC/smrf) and [PySnobal](https://github.com/USDA-ARS-NWRC/pysnobal) are in NetCDF format. In order to view these files, you will need a NetCDF viewer. The following are a small collection of viewers that we have used on the different operating systems but are by no means the only available viewers.

## Windows
[Panoply](https://www.giss.nasa.gov/tools/panoply/) is a NetCDF, HDF, and GRIB viewer maintained by NASA, and is a good option for **Windows** users.

## Mac and Linux
[Ncview](http://meteora.ucsd.edu/~pierce/ncview_home_page.html) is a lightweight NetCDF viewer that allows for quick visualization of NetCDF files and is maintained by UC San Diego.

# Running the test cases

There are three tests cases in this `awsm_test_cases` repo. They are Reynolds Mountain East, Boise River Basin and Tuolumne. In order to run these cases using the AWSM Docker image, the correct folders must be mounted to the Docker image, and the AWSM configuration file must be passed to the Docker image.

For these test cases, the folder sharing is handled through a docker-compose file (located in each test case folder), and a Python script that will create and call the docker-compose routine behind the scenes. The docker compose commands are printed to the terminal to run outside of the Python script.

The `output` folder structure for AWSM was developed to organize multiple runs over various water years and basins. The base output structure for these test cases are
```
output/<basin>/devel/<year>/<run_name>/<data | logs | runs><run_date_range>
```

The model results will be available in the `run` directory, simply open the netCDF files with a viewer to quickly visualize the results. `snow.nc` will have the snowpack state variables like `specific_mass` or `snow_depth` and the `em.nc` will have all the snowpack energetics like `swi` or `net_rad` if the simulation output those variables.

## Reynolds Mountain East, Idaho (RME)

The RME test case is for the complete water year 2006 for a small snow dominated headwater catchment within the larger Reynolds Creek Experimental Watershed (RCEW). Specifics for this model run:
* 10 meter resolution, hourly timestep
* 0.27 km<sup>2</sup> research watershed
* 28 meterological stations across RCEW
* Wind redistribution of precipitation based on [Winstral and Marks 2002](https://doi.org/10.1002/hyp.1238)
* Model type is `smrf_ipysnobal` meaning `SMRF` and `pysnobal` run at the same time utilizing the in memory queue

From the command prompt on **Linux** or **Mac**, run
```
python ./awsm_docker --case rme
```
On **Windows**, run the PowerShell command
```
python .\awsm_docker --case rme
```

> **NOTE:** will take approximatel 1 to 2 hours to complete.

## Boise River Basin, Idaho (BRB)

The BRB test case is for a significant rain on snow event in water year 2017 where the second highest inflows to the Lucky Peak Reservoir were recorded.
* 100 meter resolution, hourly timestep
* 7,000 km<sup>2</sup> watershed
* 58 meterological stations
* Model spin up previously performed to produce a model state file to restart model
* 3 month model run
* Model type is `smrf_ipysnobal` meaning `SMRF` and `pysnobal` run at the same time utilizing the in memory queue


From the command prompt on **Linux** or **Mac**, run
```****
python ./awsm_docker --case brb
```
On **Windows**, run the PowerShell command
```
python .\awsm_docker --case brb
```

> **NOTE:** will take approximately 2 to 8 hours to complete, dependent on computer resources and if threading is turned on or off.

> **WARNING:** Due to the domain size of the BRB, the simulation will consume significant memory. If the computer does not have 16GB minimum allocated to Docker, you can turn off threading by setting `threading: False` in the configuration file under `[system]`. Otherwise `AWSM` will quit unexpectedly.

## Tuolumne River Basin, California

The Tuolumne test case is for April 15 through April 17 in WY 2016. It contains a 50 meter Airborne Snow Observatory snow depth product that automatically updates the model state and shows the model results in a summary report.

* 50 meter resolution, hourly timestep
* 1,400 km<sup>2</sup> watershed
* 21 meterological stations
* 2 day model run, first runs SMRF and then runs `ipysnobal`
* 50 meter snow depth update in netCDF file format
* The simulation will first run `SMRF` then run `ipysnobal`
* Automated report generated to show lidar flight update change to model state


From the command prompt on **Linux** or **Mac**, run
```
python ./awsm_docker --case tuol
```
On **Windows**, run the PowerShell command
```
python .\awsm_docker --case tuol
```

> **NOTE:** will take approximately 10 to 30 minutes to complete.

The generated report will be in the `report` directory under `output/tuolumne`.


# Jupyter Lab for analysis

All the analysis for the manuscript figures are performed in Jupter Lab included in this repository. A `docker-compose.yml` will pull the image `jupyter/scipy-notebook` which is a pre-built docker image containing everything needed for analysis. Only the BRB and RME test case are analyzed as the figures for Tuolumne were generated with `snowav` during the run.

Once the Jupyter Lab is started, go to `http://localhost:10000` where you will be prompted for a token. The token will have appeared in the terminal, copy and paste into the form and submit.


## Linux and Mac
To start Jupyter Lab in Linux:
```
CURRENT_UID=$(id -u) docker-compose up
```

## Windows
The Jupyter Lab needs the current user id to ensure that any files created or modified are owned by the user. Windows is slightly different in that the `CURRENT_UID` is just a placeholder and does not matter. To start the docker image in Windows, either:

```
docker-compose -f docker-compose.windows.yml up
```
or
```
SET CURRENT_UID=1000
docker-compose up
```

