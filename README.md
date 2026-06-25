# LabVIEW controls for van der Pauw *in-situ* resistivity measurement
## Requirements
### Hardware


* Keithley 4200-SCS (or 4200A-SCS) with at least 4 SMUs, on the same network as the host PC.
* Triaxial cabling from the four SMUs to four peripheral contacts on the sample.
* ALD reactor / tube furnace with the sample probe integrated.

### Software

* LabVIEW (2018 or later recommended). Uses only base LabVIEW primitives — no MathScript,MATLAB, or add-on toolkits required.  
* NI-VISA (provides the VISA functions and NI MAX).
* KXCI running on the 4200-SCS (configured via KCon).

--- 

## Usage guide
The electronic controls is meant to interact with the LabVIEW interface for running atomic layer deposition (ALD). The interface presented is simply the code which calculates sheet resistance between each deposition cycle. The interface needs to be extended to be input into the deposition loop cycle.  
The design has three main components: initialize the 4200 (init_Keithley_4200.vi), run (vdp_measurement.vi), and close (Close_Keithley_4200). There exists a fourth file for testing (Main_Test.vi).  
A separate repository exists to run vdP controls using the Keithley 2600B, [2600B Repo](https://github.com/lpaul27/vdP_in_situ_resisitivity). Note that this repository and code has access to only 1 SMU, and therefore must rely upon the uniform and even film assumption which may result in higher error. 

--- 
## Setup and Configuration
1. KXCI setup (4200 CPU)
* The Visual output on the 4200 does not work, so an external monitor is required to see visual output.
* Start the app KXCI
* In KCon/KXCI settings, note the ethernet port value.
* Set the reading delimiter to None; The 4200 still appends Null terminator automatically, which is required for the system
2. The instrument must be addressed in LabView as a raw socket, such as TCPIP0::<instrumen_ip>::<port>::SOCKET (e.g. TCPIP0::192.168.0.10::1225::SOCKET).
3. Measurement parameters
* Source current is altered by changing column 1 of the force command array (8x4) NOTE: changing in the array requires changing the source current used for the calculations at the right side. 
* Source voltage compliance is altered by changing the last field of each DI source command.
* Sink current compliance is altered by the last field of the DV sink command.

4. Reading arrays:
The 8x4 array is setup in form:
| Configuration | | Source | Sink | Voltmeter | Voltmeter |

5. Solving the transcendental
* vdp relation is transcendental and solved using Newton's method which iterates until the relation is satisfied (up to 10 looping sequences). The initial guess comes from the isotropic assumption used in the 2600 controls. 

--- 
## Troubleshooting 

| Symptom | Cause | Fix |
|---|---|---|
| `-1073807346` at VISA Open | empty resource field or stale session | set the full `::SOCKET` string; restart LabVIEW to clear stale sessions; don't use Abort |
| Read returns `ACK` instead of a number | unread ACK clogging the buffer | pair every Write with a Read (see protocol rules) |
| Read hangs / VI must be aborted | command not NULL-terminated | confirm `\00` shows in `'\' Codes Display` |
| Reading begins with `C` during a real measurement | compliance reached (invalid reading) | lower current or raise voltage compliance |
| A configuration behaves like the previous one | KXCI short-command carryover | one-read-per-write drains the buffer each command; if it persists, pad commands to equal length or add a VISA Clear |
| R_s near zero on an open circuit | expected — voltmeters float on open | not a fault; use the open-circuit *source-channel* test instead |