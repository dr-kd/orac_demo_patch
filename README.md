## Prerequisites.

Hardware: 

This is written for Orac 2.0 on the organelle.

Knowledge:

* You need to know something about how puredata works.
* You need to know what json is.
* You need to understand files and folders, how to use a text editor and how to use VNC.
* My own workflow is very terminal heavy on using the terminal.  I'm going to assume you know what that means and how relates to your own preferred workflow.  I use the terminal by ssh but you can use it in VNC too.  In fact you can use the organelle as a desktop environment.

## Startup

* Connect your organelle to wifi and start VNC.  Start your VNC client and connect on your computer.
*Keep VNC running for this whole tutorial

## Basic mostly minimal orac patch

So first we set up wifi for the organelle, start VNC then connect to the organelle via the vnc client.  ssh into the organelle, then copy the empty patch into our development folder using the  following command:

    mkdir -p /usbdrive/media/orac/usermodules/dev/demo
    cd /usbdrive/media/orac/usermodules/dev/demo

Here I've made the decision to put my modules under development into the `dev` folder.

Next, copy the empty patch files into the new directory:

    cp -rp /usbdrive/Patches/orac/modules/utility/empty/module.json .

-- commit 1 here "copy of empty patch"

### Set up parameters with modules.json

Now we're ready to set up the parameters received by the patch.  Open up the file `module.json` in a text editor and you'll see this:

    {
        "display" : "Empty",
    "parameters" :  [ 
    ],
    "pages" : [ 
        ["pg_main",[]]
    ]
}

Each parameter received by the patch goes into a list The format is going to look something like this:

    "parameters" :  [
        [ "[type]", "[variable name]", "Display text", min, max, default],
	// ... more definitions here
    ]

Note the json above contains comments, but the puredata json parser as per the json spec doesn't support comments.

There are a number of valid types as below:

    float  : decimal number - displays to 1 decimal place
    freq   : number between 0 and 25,000 or so - displays with the units Hz
    int    : whole number
    pan    : dont' know - only seen in the router modules
    pct    : percent 0 to 100 - displays with the units '%'
    pitch  : integer - displays with the unit 'st'
    time   : milliseconds - displays with the units 'Ms'

So in our new patch we can add the following parameters:

	[ "pitch", "var1", "Line 1", 0, 127, 60],
	[ "int", "var2", "Line 2", 0, 3, 0],
	[ "time", "var3", "Line 3", 0, 5000, 500],
	[ "freq", "var4", "Line 4", 20, 20000, 440 ],
	[ "float", "var5", "Line 5", 0.01, 1.01, 0.53 ],
	[ "bool", "var6", "Line 5", 0 ]		


Following this under pages we describe the display order

    "pages" : [ 
        ["pg_one","Page one", ["var1", "var2", "var3", "var4"]],
        ["pg_two","Page two", ["var5", "var6"]]
    ]

So here for each page, we have a unique label for the page (like a
variable name), the Display name of the page, and then a list of the
up to four parameters for that page.

So now the entire module.json looks as follows:

{
    "display" : "Demo Patch",
    "parameters" :  [
	[ "pitch", "var1", "Line 1", 0, 127, 60],
	[ "int", "var2", "Line 2", 0, 3, 0],
	[ "time", "var3", "Line 3", 0, 5000, 500],
	[ "freq", "var4", "Line 4", 20, 20000, 440 ],
	[ "float", "var5", "Line 5", 0.01, 1.01, 0.53 ],
	[ "bool", "var6", "Line 6", 0 ]        
    ],
    "pages" : [ 
        ["pg_one","Page one", ["var1", "var2", "var3", "var4"]],
        ["pg_two","Page two", ["var5", "var6"]]
    ]
}

-- commit two here "basic demo modules.json"

So now if you reload the storage on the organelle, reload orac (load another patch, load orac then load the demo module into slot A1) then you'll see the display components for the patch.

If you don't see then then your JSON is wrong.  Use a json validator like the one https://jsonlint.com/, or if you know how, `apt-get install` jq on your organelle.

## Now the patch itself.

In VNC we'll need to find the module we've just loaded.  Puredata will have loaded the whole orac patch.  You should find the window `main.pd` and click on the box `[pd a1]`.  Then you'll see a box in the middle on the left hand side `/tmp/media/orac/usermodules/dev/demo/module a1`.  Double click on this. Now we see the empty patch that we copied over.

Time to go into edit mode and delete all of the not required loadbang and oled stuff on the right hand side.

Now we're left with an empty patch that discards audio input, receives midi input and sends it straight out.

-- commit three.  strip down the empty patch module.pd

At this point we've demoed a bare minimum module with all the data types (except pan as I don't understand that one) available in the menu that does nothing.  I guess in lesson two we're going to have to do something real.

## Distributing the patch

While this patch does nothing it's a good time to describe how to distribute it.


First create a deploy.sh script:

    oscsend localhost 4001 /oled/aux/line/2 s "installing"
    oscsend localhost 4001 /oled/aux/line/3 s "orac module"
    cd ..
    mv $1 /usbdrive/media/orac/usermodules/dev
    exit 1


Note the penultimate line - this is where you want the module to end up on the file system.

In the folder `/usbdrive/media/orac/usermodules/dev`, issue the following command at the terminal:

     ~/scripts/create_install_package.sh demo

This will output a demo.zop file you can distribute to others.
