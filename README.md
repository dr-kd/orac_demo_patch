# What is this?

This is an attempt to provide some instructional documentation on writing patches for orac, the Pure Data based modular synth rack. [[ link to orac here ]]

I've got an organelle so this is based on using the organelle iteslf as the development environment.

## Prerequisites.

Hardware: 

This is written for Orac 2.0 on the organelle.

You will also probably want to run puredata on a more conventional computer.  I would like to see instructions for running orac on a laptop/workstation hopefully if someone else doesn't get around to it, I might.

Knowledge:

* You need to know something about how puredata works.
* You need to know what json is.
* You need to understand files and folders, how to use a text editor and how to use VNC.
* My own workflow is very heavy on using the terminal.  I'm going to assume you know what that means and how relates to your own preferred workflow.  I use the terminal by ssh but you can use it in VNC too.  In fact you can use the organelle as a desktop environment.

## Startup

* Connect your organelle to wifi and start VNC.  Start your VNC client and connect on your computer.
* Keep VNC running for this whole tutorial

## Contents

- 00_basic_parameters

This covers getting parameters visible on the screen and ready to be received by your patch.

-- 01_full_patch

This covers converting a patch from the puredata examples to an orac patch.
