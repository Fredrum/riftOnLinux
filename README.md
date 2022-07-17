# riftOnLinux
Getting Oculus Rift CV1 to work on Linux. Currently on Raspberry Pi 4b with Ubuntu 22.04.
I am using full fat 'kms' kernel display system, not the old 'fkms'. This is partially the reason for this exercise.

This is a work in progress and its possible that some steps are actually not required.
I'll update as I'll figure out more. 

If you have any information that you think might help please add an 'Issue' to let me know!



Current Steps:

1.  Copy the file 83-hmd.rules to  
`/etc/udev/rules.d/`

2.  Copy the modded edid from this repo modified_rift_edid_01.bin to  
`/lib/firmware`

This edid has been hacked so that the Kernel won't take notice of the 'non-desktop' attribute which is hindering the display to get recognized and take signal. You can compare with the un-modified edid to see how that was done. I got some help doing the edid hack as I didn't know how to find and change the right data.


3.  Edit cmdline.txt to have this before 'rootwait'  
`video=HDMI-A-2:2160x1200M@90e drm.edid_firmware=HDMI-A-2:modified_rift_edid_01.bin`  
The above line assumes you are using the secondary hdmi port on the Rpi4b.


4.  Reboot  
When you return you might not get a good login screen at least I don't. I can't see a field to put user name.

5.  go to tty3, start up riftDriverPi  
https://github.com/OhioIon/riftDriverPi  
(Don't press any keys after you started this, other than to switch tty)

This is currently needed to wakeup the Rift and keeping it alive. The orange light in the headset should go white and you should see the tty sceen in the Rift. So far I haven't found another way to get the Rift screen alive.

6.  go to tty2, run kms-vulkan  
https://github.com/nyorain/kms-vulkan  
I'm currently hacking in a line in device.c just after  
`for (int i = 0; i < ret->res->count_connectors; i++) {`  
to skip past the first out device (my monitor) bu doing  
`if(i==0) continue;`  

For me this program currently fails setting up for Vulkan rendering but falls back to OpenGL.



Notes:
- Look for Rift display mode to be present in return of  
`modetest -c`  


ToDo:
- Figure out how to get the display modes to allow Desktop to co-exist.
- Get kms-vulkan to actually use Vulkan and not OpenGL.
- Get a stereo/multiview example using Vulkan working on Raspberry Pi4b.
