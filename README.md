# LabVIEW controls for van der Pauw *in-situ* resistivity measurement
## Requirements
###Hardware


*Keithley 4200-SCS (or 4200A-SCS) with at least 4 SMUs, on the *same network as the host PC.
*Triaxial cabling from the four SMUs to four peripheral *contacts on the sample.
*ALD reactor / tube furnace with the sample probe integrated.

###Software

*LabVIEW (2018 or later recommended). Uses only base LabVIEW primitives — no MathScript,MATLAB, or add-on toolkits required.  
*NI-VISA (provides the VISA functions and NI MAX).
*KXCI running on the 4200-SCS (configured via KCon).

## Usage guide
The electronic controls is meant to interact with the LabVIEW interface for running atomic layer deposition (ALD). The interface presented is simply the code which calculates sheet resistance between each deposition cycle. The interface needs to be extended to be input into the deposition loop cycle.  
The design has three main components: initialize the 4200 (init_Keithley_4200.vi), run (vdp_measurement.vi), and close (Close_Keithley_4200). There exists a fourth file for testing (Main_Test.vi).  
A separate repository exists to run vdP controls using the Keithley 2600B, [2600B Repo](https://github.com/lpaul27/vdP_in_situ_resisitivity). Note that this repository and code has access to only 1 SMU, and therefore must rely upon the uniform and even film assumption which may result in higher error. 