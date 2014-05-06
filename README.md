Sonic Potions LXR Drumsynth Firmware
====================================

This is a fork of the LXR DrumSynth firmware. My intention for this fork is to add features and fix issues
that are important to me. I will be freely offering any changes back to the SonicPotions repository.
The custom firmware is <a href="https://github.com/rudeog/LXR/raw/master/firmware%20image/FIRMWARE.BIN">RIGHT HERE</a> (right click the link and do save as).

Here are a list of current changes (differences from Sonic Potions repos):
--------------------------------------------------------------------------
- Merge TB323's repository which includes a "rot" parameter in the pattern generator allowing you to apply a rotation while generating a pattern.
- Remove untargetable modulation destinations from menus (LFO mod target, and LFO voice can't themselves be modulated as it doesn't make sense to do so)
- Pattern Rotation - When in performance mode, if you hold shift button and select one of the sequence step buttons you can rotate the pattern for the active voice by a number of steps relative to the other voices.
- Save/Load performance - this new save option lets you save the current drum patch along with the pattern set and the BPM into one file allowing multiple slots.
- Save/Load all - this new save option lets you save all the data in the LXR (kit, pattern, settings) into one file allowing multiple slots.
- Better handling of startup not finding the default drum kit. Will display an error and continue with a patch that needs editing before it will work.
- Realtime erase - This is useful, I've found, when recording sequences on a keyboard that are multiple bars (measures) in length. Hold down shift and copy(clear) button. While these are held, the notes on the active voice are erased. You must be in record mode, and the sequencer must be playing. So you could erase a few bad notes as you listen to your sequence, or erase multiple bars as they switch without having to mess around with selecting patterns.
- Recording quantization enhancement - When recording a sequence that spans multiple bars, if you are quantizing, and you hit a note right before the next bar starts (such that the note should quantize to the next bar's first step) it will now do so.
- Global menu can now hold more than 8 items and will scroll to page 3 (and beyond) when toggling pages or moving encoder right
- Pattern change reset mode. This is a new global setting that, when turned on, will cause song counter to start from 0 whenever a pattern changes. This prevents the behavior of not playing the displayed number of repetitions for a pattern (when in certain song positions. see the LXR manual). 
- Global midi channel setting. Affects recording notes from external gear, program change, and control change messages.
- Active voice control and recording. When a voice is active and notes are received on the global midi channel, they will be sent to the active voice, possibly recorded on the active voice, and echoed on the active voice's configured channel (all of this regardless of what channel the active voice is set to)

Here are a list of changes that are now merged back into the Sonic Potions repos:
---------------------------------------------------------------------------------
- Midi filtering. You can filter messages from being transmitted or received. Tx is independent of Rx. There are four categories of messages that can be individually configured: Program Change (P), Control Change (C), Realtime start/stop/clock (R), and Note on/off (N). These are set in the global menu under txf and rxf (transmit and receive respectively). Combinations of letters are shown. If a letter is shown, the midi message in that category will be transmitted or received. If set to off, nothing is sent/received, and if set to all everything is sent/received. This has nothing to do with midi routing which works the same way regardless of filter.
- Midi routing (midi soft thru). You can route midi messages from input to output. This is set in the global menu under "mrt". There are 6 possible values: off - no routing, U2M - usb midi is routed to midi out port; M2M - midi received on midi in port is routed to midi out port; A2M - both usb and midi are routed to midi out port; M2U - midi input port is routed to usb output port; M2A - midi received on midi port is routed to both midi output and USB output.
- Midi note off fixes. Will send midi off when changing pattern, channel or muting voice
- MTC sync. Will now recognize MIDI MTC quarter frames and start when it receives a 0:0:0 location, and stop when MTC stops
- Program change. Will respond to program change and will change between the 8 patterns when received via MIDI/USB port. For programs > 7 will use remainder when divided by 8. Program change will only respond to PC messages on the global midi channel.
- Program change. Will send program change 1 to 8 on MIDI when manually changing drum patterns. The channel that it sends it on is the configured global channel
- Control change. Should only respond to CC messages on the global midi channel.
- Pattern/Kit save will use a proper cursor when editing names
- Midi Off - Will now send midi note off messages before the next midi note on message and also when
  the sequencer is stopped. This allows better interaction with external synths.
- Note names - instead of showing -63 to 64 it will show real note names like C, C# D, etc. This is useful when using the LXR as a sequencer
- Midi Note. Each voice has an assigned midi note. If this is set to C0 then it will play the note assigned to the sequencer step, otherwise it will play this note. This replaces the standard behavior and allows one to set the note that is played for the drum sound (old behavior was to have the note fixed when in trigger mode).
- Midi mode has been removed (the above feature allows more flexibility)
- LFO target fixes - Setting LFO targets is now much easier. The list of targets is straightforward and more descriptive. There are no missing entries or dupes.
- Velocity target - Same type of fix as LFO target
- Automation target - Same type of fix as LFO target. Also menu is more descriptive.
- Separate open and closed hi-hat EG decay onto separate drum buttons to make it clearer which one is being edited.
- Fixed some minor bugs that were annoying me.
- Add Eclipse CDT support for AVR. This allows all development of front and mainboard to be done in one 
  Eclipse workspace, and gets rid of the need for installing AVR studio
- Remove generated files. These makefiles are generated by eclipse and contain local paths etc. No need
  for them in the source tree.
- Fix firmware image builder to accept command line parameters, and create new batch file


Instructions for building:
--------------------------

NOTE: It is now possible to use Make to build the firmware. I will update the instructions, in the meantime see: http://forum.sonic-potions.com/discussion/417/how-to-build-the-lxr-firmware-code-on-mac-os-10-8-and-above
Which includes instructions for building on all platforms.

1.  Install Eclipse Juno CDT (You could install a later version, but this is the version I have working)

2.  Install the Eclipse GNU ARM plugin. Go into the help menu, Install new Software, add a site: http://gnuarmeclipse.sourceforge.net/updates. Then check the box to install that plugin.

3.  Download and install the GCC ARM toolchain https://launchpad.net/gcc-arm-embedded/+download

4.  Download and install gnu make: http://gnuwin32.sourceforge.net/packages/make.htm

5.  Download and install Atmel AVR toolchain from http://www.atmel.com/tools/ATMELAVRTOOLCHAINFORWINDOWS.aspx (you don't need the headers package)

6.  Ensure that the bin directory from 3, 4, and 5 are on the path. I made a batch file that adds these 3 bin directories to my path and launches eclipse.

7.  Fetch the LXR sources from github. You can either install git and do it the git way, or download a zip and unzip it.

8.  In Eclipse, create a workspace in root folder of whole tree that you unzipped (or git'd).

9.  Add two project dirs mainboard\firmware\DrumSynth_FPU and front\AVR to the workspace. To do this, use File/Import/General/Existing projects into workspace. Then select root directory and browse to these dirs. Do this step once for each of these two dirs. You will end up with two projects in your workspace.

10.  These should build. Eclipse is a bit squirrely, so you might need to do a make clean first to create the first makefiles, or rebuild indexes.

11.  I've built the firmwareimagebuilder.exe in the \tools\bin folder. I've also put a batch file that launches it and copies the binaries from the respective output directories to create FIRMWARE.BIN in that same dir. If you don't trust the .EXE I built, you will need to build it from tools\FirmwareImageBuilder\FirmwareImageBuilder. As is you will need visual studio. If you don't have it, you can try to install the free version, mingw, etc and compile the one file FirmwareImageBuilder.cpp (I've fixed it so it should build with any tool) and make your own exe and copy it to that dir.

12.  Thats it, after running the batch file you will have your firmware file. 

More Info
---------

Please visit https://github.com/SonicPotions/LXR for the original firmware

- The 'front' folder contains the AVR code

- The 'mainboard' folder contains the STM32F4 code

- The 'tools' folder contains the firmware image builder tool, to combine AVR 
  and Cortex code into a single file, usable by the bootloader. This requires visual studio to compile right now.
