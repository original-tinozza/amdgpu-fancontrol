# amdgpu-fancontrol
Linux script to manually control cooling fans of modern AMD GPUs

## Introduction

This is a simple bash script that can be used to control the cooling fans of recent AMD GPUs supported by the **amdgpu** kernel driver. The script is able to let the user control manually the fans rotational speed by changing directly the fans PWM set-point or by defining a custom fans control curve (with a separate curve definition file or using directly the script user interface).

## How does it work

By default the **amdgpu** kernel driver has a userspace interface inside the /sys directory. Below the list of files monitored and/or manipulated by the script:

| File Name | Used to... |
|---|---|
| /sys/class/drm/card?/device/hwmon/hwmon?/temp1_input | monitor the EDGE temperature reading |
| /sys/class/drm/card?/device/hwmon/hwmon?/temp2_input | monitor the JUNCTION temperature reading |
| /sys/class/drm/card?/device/hwmon/hwmon?/temp3_input | monitor the VRAM temperature reading |
| /sys/class/drm/card?/device/hwmon/hwmon?/fan1_input | monitor the FANS rotational speed reading in RPM |
| /sys/class/drm/card?/device/hwmon/hwmon?/pwm1_enable | change the FANS PWM control mode |
| /sys/class/drm/card?/device/hwmon/hwmon?/pwm1 | change the FANS PWM set-point value |
| /sys/class/drm/card?/device/hwmon/hwmon?/pwm1_max | get the FANS PWM set-point maximum allowed value |
| /sys/class/drm/card?/device/hwmon/hwmon?/pwm1_min | get the FANS PWM set-point minimum allowed value |

where **'?'** *(question mark)* is an integer number that can vary wildly from system to system (the script automatically search for the right value. Usually there is no need for input from the user)

## Installing and manually running the script

Place the script wherever you like and make it executable by using:  
<code>$ chmod +x amdgpu-fancontrol</code>

To get a little help on the various available options/arguments available, from inside the directory in which the script resides run:  
<code>$ ./amdgpu-fancontrol --help</code>

Run the script by doing:  
<code>$ sudo ./amdgpu-fancontrol</code>  
*(To manipulate the files listed in the list above elevated privileges are necessary).*

## Fan control curve

By default the script looks for a file named **amdgpu-fancurve** inside the directory **/etc/default** (or use the argument *'--fancurve=/path/to/filename'* to use a custom named file located in a different directory).

Below an example of the fan control curve file:

<code>$ cat /etc/default/amdgpu-fancurve</code>

> \### Fan curve template file for amdgpu-fancontrol ###
> 
> \# Lines starting with a 'dash' (#) or completely empty are ignored
> 
> \# C_MAXPWM defines the maximum PWM value allowed when controlling the  
> \# cooling fans.  
> \# Only modifications to the numeric values are allowed!  
> C_MAXPWM=145
> 
> \# C_MAXPOINTS defines the total number of points needed to create  
> \# the controlling curve.  
> \# Only modifications to the numeric values are allowed!  
> \# (at the moment only curves with C_MAXPOINTS=5 are supported,  
> \# changing the value will trigger an error.)  
> C_MAXPOINTS=5
> 
> \# Curve points definition in the format XX:YY:ZZ where:  
> \# XX is the point number from 1 to C_MAXPOINTS (0 is ignored)  
> \# YY is the TEMPERATURE value at the XX point  
> \# ZZ is the PWM set-point value at the XX point  
> \# Strictly respect the format (whitespace and tab are not allowed)  
> \# Only modifications to the numeric values YY and ZZ are allowed!  
> 1:60:65  
> 2:70:75  
> 3:80:95  
> 4:85:115  
> 5:90:145  
> 
> \# the last ZZ value (145 in this case) must be equal to C_MAXPWM defined above

## Tested with positive results

The script was tested with the following:
- **POLARIS 10** chip (RX480)
- **NAVI 10** chip (RX5700XT)

## Credits and acknowledgments
Thanks to [sibradzic](https://github.com/sibradzic) and his script [amdgpu-clocks](https://github.com/sibradzic/amdgpu-clocks). It was the spark that inspired me in writing this script and make it to work alongside his one.

## Disclaimer
**THE APPLICATION/SCRIPT IS PROVIDED “AS IS” WITHOUT ANY WARRANTIES.**  
**THE USE OF THE APPLICATION/SCRIPT IS AT YOUR OWN RISK.**
