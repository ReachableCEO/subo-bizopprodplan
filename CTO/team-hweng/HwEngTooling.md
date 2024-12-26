# TSYS Group Handbook - Engineering Documentation - Team HwEng - Tooling

## Introduction

This article covers the support stack of software and hardware used by the hardware engineering
team to develop the orbiter.

Topics covered by this documentation:

* Computer Systems
  * Dev/qa/prod systems

* (Custom) Hardware machines/tools for prototypes
  * Envelope Sealer
  * Inflator
  * Pressure chamber
  * Reflow Oven
  * RF Testing chamber
  * Environmental Testing - Temperature
  * Environmental Testing - UV

* Software

See the following links for the software setup guides

	- <https://git.turnsys.com/TSGTechops/docs-techops/src/branch/master/TSYS-DevEnv-VsCode.md>
	- <https://git.turnsys.com/mrcharles/dotfiles/src/branch/master/README.md>


## Computer Systems

* Raspberry Pi
  * subobench (all lab equipment with a digital interface is attached to this). Documented on discourse.

* x86 servers
  * subodev We develop workloads here. Once a day update or so. (After any needed iterations on our dev workstations)
  * suboqa We run qa/unit/integration/burn in etc tests here on all workloads. Expect once a week updates or so.
  * suboprod We run production workloads here. All things on here should be expected to come under regulatory review. May be considered a system of record, need to ponder. This is CUDA enabled (quadro). Expected to be under cluster control (slurm).

* Tegra cluster
  * suboprod01-05

## (custom) tooling for prototypes

### Envelope Heat Sealer

* Overview

This is used for assembling the envelopes. It's a customized heat sealer, controlled by an Arduino for precision temperature control.

* Bill of Materials

### Inflator

* Overview

This is used for inflating the envelopes once they've been assembled (in particular for leak detection).

It's a customized pump , controlled by an Arduino for precision pressure control.

* Bill of Materials

### Pressure chamber

* Overview

* Bill of Materials

### Reflow Oven

* Overview

This is used 

* Bill of Materials

### Cameo Silhouette 

* Overview

This is used to (primarily) make solder stencils for PCB re-work we will be doing on prototypes.

### RF Testing Chamber

We will do as much EMC testing in house as we can. This will save us 10s of thousands of dollars
and will increase our first pass chances at the FCC certified lab immensely. We can also rent out the chamber when we aren't using it.

* Major components
* Considerations/concerns
* Budget/BoM

### Environmental Testing

We will do as much environmental testing in house as we can. Need to research if any external testing is required by regulation, but currently (03/03/2021) I'm un-aware of any that is required.
Of course we will test extensively , for robustness etc.

* Major components
* Considerations/concerns
* Budget/BoM

#### UV

#### Extreme cold

