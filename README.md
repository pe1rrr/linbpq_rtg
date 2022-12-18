# linbpq_rtg
```
| |   |_ _| \ | | __ )|  _ \ / _ \ 
| |    | ||  \| |  _ \| |_) | | | |
| |___ | || |\  | |_) |  __/| |_| |
|_____|___|_| \_|____/|_|    \__\_\
```
                                   
# Before you get started:


This setup only contains binaries for the Raspberry Pi platform.

If you wish to have x86 32bit binaries, edit the update-xxxxx scripts and remove 'pi' from the filename
Then run the update scripts, this should download the lastest build from G8BPQ's website.

## Tips:

Don't move any of the config files!
Some files (namely .dat/.SYS) are autogenerated by the system, many of these are binary format and are not 
human-readable. Do not attempt to edit these files with a text editor.


# Ok so you downloaded the repo, now what?

Make a note of the current working directory and put it into the top of the ```runbpq``` script.


```
  cd linbpq_rtg

  echo $PWD
```

Open ```runbpq``` in your favourite editor:

```
nano runbpq
```

Example contents of ```runbpq```:
```
cd /home/pi/linbpq_rtg
```
If this already matches your directory structure, you're good.

**Note: you can rename your ```linbpq_rtg``` installation to anything, just ensure the path is set accordingly in ```runbpq```**

Open the ```bpq32.cfg``` in whichever text editor you prefer (mousepad, vim or nano for example) and begin filling in the parts marked out by the comments such as all of the places where ```YOURCALL``` is mentioned.

# Additional Libraries

LinBPQ may fail to start if there are some required libraries missing from the operating system, as of version 6.0.23.33 there is a requirement for ```libasound```, installed with:

* Raspberry Pi:
```
sudo apt install libasound
```

* linbpq for x86 requires 32bit libraries:
```
sudo apt install libasound:i386
```

# Set your sysop login info

The web HTTP and telnet login for the sysop is located down in the **TELNET** port section of the ```bpq32.cfg``` file.
Configure as necessary, modifying the example provided within the ```bpq32.cfg``` file.

At this point is should be possible to launch the ```./runbpq``` script and then interact with the node via the web interface.
Open a terminal and run ```./runbpq```, it should start the node and sit showing debug information, meanwhile open a browser to ``` http://IP-of-your-pi:8080/```

If you have a web browser running on your Pi, open ```http://127.0.0.1:8080```

You should be prompted with a webpage with a selection of links including one to a sysop sign-in.
If not, these docs are broken and you need to report this to ```pe1rrr@amsat.org```!


# Headless running / automatic 

Setting up auto-start and headless running is covered under the ```systemd``` setup below in the appendix

It is also covered in my packet artciles section along with a host of other useful information for BPQ node operation. 

Visit **http://eindhoven.space/radio-experiments/packet-radio**

----

# File descriptions: (excluding auto-generated files)


* ```runbpq``` - A script which launches BPQ, this needs to be edited to reflect the precise location of your
         BPQ installation directory. In the future, a systemd auto-start script can use this script
         to monitor and start/restart the linbpq process.

* ```bpq32.cfg``` - main node configuration 
            You will need to edit YOURCALL! Most of the configuration is documented with comments.

* ```chatconfig.cfg``` - bpqchat server configuration.

Do note that for chat servers to link they MUST both be appearing in the node table.


* ```info.txt``` - When someone types 'i' on your BBS (not node), this is sent to them.

* ```badwords.sys``` - Any incoming mail which matches any words or phrases on this list will be set HOLD status.

* ```linmail.cfg``` - This is the BBS configuration file, best not to edit by hand. Saved by the web interface.
                You will need to edit YOURCALL!

* ```WP.CFG``` - white pages configuration, a database of users to help mail routing. Saved by the web interface.

* ```BPQAPRS``` - Directory.
          This directory is auto-generated by the APRS server and serves the web interface and caches
          map tile files downloaded from OpenStreeMap.

* ```HTML``` - Directory.
       Cached web interface files unpacked by bpq32. 

* ```Files``` - Directory.
        This is where files can be put which you want to provide via your BBS. Subdirectories are NOT
        supported!

* ```Logs``` - Directory.
       Logs are stored here for many of the services. Every day BBS house keeping rotates and deletes older
       logs. The length of time you wish to keep logs is defined under the BBS house keeping tab within the
       web interface. Every day, symbolic links to the latest log files are generated in the BPQ directory.
       e.g logLatest_BBS.txt will always point to the current live log.

* ```bpqbin``` - Directory
         This directory is where the binaries are put by the update scripts (by PE1RRR). 
         Every time a new updates is pulled from the Beta site download, the previous
         version is kept. Symbolic links are used to keep 'linbpq' always pointing to
         the latest version. If there is a need to roll back to a previous version this
         is easily accomplished by first unlinking the new version and re-linking the
         previous version. Each version is stored with the date as part of the filename. 

```
        unlinking: cd /dir/to/bpq
                   unlink linbpq
        relinking: cd /dir/to/bpq
                   ln -s bpqbin/pilinbpq.<version> linbpq
```

Good to know: Updates via the web interface will create .bak backup copies of files.

# Node Linking

## For Amateur Radio Use Only!

The provided configuration is preset to open a UDP wormhole to both of PE1RRR's nodes, 
PE1RRR-5 node is set up to allow automatic connections while setup to prevent 
rogue nodes poisoning the network. The limitation is that your nodes won't propagate
thought this node, but you will be able to access the network and all destinations
provided by PE1RRR-7 as this is daisy-chained to PE1RRR-5 and PE1RRR-5 broadcasts
those to you.

Should you wish to have your node propagate through the network, please get in touch
so that we can arrange a direct connection to PE1RRR-7, if resources permit!

Once linked into the network, you should be able to reach nodes such as RRRCHT. If you wish to link
your own chat-server to the network (if you absolutely must run one) there still needs to be some manual
configuration done on them to allow your chat-server to join the network, so if intending to link with mine
please get in touch with your node and chat server's details. pe1rrr@amsat.org

## For those wishing to experiment on CB Net 

Replace: 
	MAP PE1RRR-5 rrrnet.ehv-------.com UDP 10093 B ; Connection to PE1RRR's Auto-Happy-Node.
With:
	MAP DX3RRR-5 cbnet.ehv-------.com UDP 27235 B ; Connection to DX3RRR's Auto-Happy-Node.

# Additional Tools provided:

* ```qtsm``` - QtSoundmodem - Advanced Soundcard modem, extended from UZ7HO's SoundModem
        
* ```qtsm_rpi3``` - wrapper script for Raspberry Pi 3. Due to a glitch in ALSA, you will need to use 'aoss', an sound system bridge, this should be installed with:
```
sudo apt install alsa-oss
```
This installs a tool called aoss which bridges ALSA, a workaround for the PTT-delay bug in the OS, QtSoundModem.ini will need to be provided with /dev/dsp0 style paths.

* ```qtterm``` - QtTermTCP - Multi-terminal tab/windowed interface for BPQ32/linbpq

* debug-tools/**gdbwait** - GDB debugging script which will automatically attach to the running linbpq process. See 'How to be helpful' below for more about this.

**Important**

Depending upon where you launch the binaries from will determine where their configuration file will
appear. It is best to launch the tools from within the bpq directory, but it isn't mandatory.

* ```qtsm``` will create ```QtSoundModem.ini``` (one is provided in the linbpq dir)
* ```qtterm``` will create ```QtTermTCP.ini```

Both of the above **require QT libraries** to be installed:

```
sudo apt-get install qt5-default
```

# Access and Credentials:

Web interface: http://127.0.0.1:8080

Telnet TCP port: 2323 Use a regular Telnet client. 

QtTerm TCP port: 2424 (for use with qtterm) this port is also known as the FBBPORT.
                When setting up a connection to linbpq or BPQ32 from QtTermTCP, use this port along
                with the telnet username and password configured in bpq32.cfg - look under Port 10.

The reason these ports are not set on standard service ports is due to the the rule that ports under 1024 
are restricted by the OS for the root user. As BPQ runs as a non-root users, these standard ports are not accessible to BPQ.
It is generally ill-advised to run BPQ, or any user service as root for the sake of security.

Credentials for logging into the web interface, telnet and QtTerm port are configured within the 'Telnet' Port in bpq32.cfg

# BPQ Ports:

For reference, in this unofficial configuration distribution, the following BPQ-Ports are preconfigured:

* BPQ-Port 1: QtSoundModem connected via AGWPE Mode (UZ7HO driver). 

It is possible to run two ports on SSB with different audio tones. These two ports are set up
to demonstrate that. It may be useful if you want to keep your BBS forwarding traffic away
from the general user port (usually Net105, which is 14.105 LSB 1700hz center frequency).

Of course it is possible to set up qtsm as a VHF radio port on FM, in which case, simply disable
the second port below.

* BPQ-Port 2: QtSoundModem connected via AGWPE Mode (UZ7HO driver) 
            As above but with a different audio center frequency.  Cool eh?

* BPQ-Port 10: Telnet

* BPQ-Port 11: AXIP/AXUDP

If you wish to add radio ports to BPQ, the numbers 1-9 are available. QtSoundModem is defined as a usable example for
those without a hardware TNC (its very good on HF!). QtSoundModem can also be configured to work as a KISS TNC which
may be desirable for VHF usage (multiuser), and KISS mode is mandatory to use if you wish to utilise NETROM functionality.

```
73 and good luck!
red        _             
 _ __  ___/ |_ _ _ _ _ _ 
| '_ \/ -_) | '_| '_| '_|
| .__/\___|_|_| |_| |_|  
|_|                      
```


# Appendix 

## Notes:

### Get the latest binaries 

All of the tools have an unofficial ```update-*XXXX*``` script that will download the latest Beta release from G8BPQ's website.

### Additional documentation:

* https://www.cantab.net/users/john.wiseman/Documents/QtSoundModem.html

* https://www.cantab.net/users/john.wiseman/Documents/QtTermTCP.html


###  Multiple nodes on one machine.


Due to the fact I run multiple instances of bpq on my machine it is important to declare a different
subdirectory name per install, in this case I use my callsign as a directory for linbpq install.
This is necessary for the program to identify its own segment of shared memory, the way linbpq accomplishes
this is by using the last part of the binary's path as a 'unique' idenitifer. This prevents the systems from corrupting
each other. This works in tandem with the undocumented 'MULTIPLE' declaration in the APRSDIGI configuration section of
bpq32.cfg. Not many people will need this, but it does make it tidier when running multiple nodes (with totally
different callsigns, obviously!). This distribution has MULTIPLE defined by default. You can see this in action by
looking in the /dev/shm/ directory.


### Auto-Starting with systemd

Look inside the doc/ directory for an example systemd service file as well as the instructions for how to implement the scripts necessary to auto-start linbpq and qtsm at boot up.


### Be prepared to bug report!

Due to this being Beta software, it is advisable to join the BPQ Groups.io community to keep track
of news, if you want to always be cutting edge (moreso than the groups.io) then join the BPQChat
after linking your node into the network. John is usually there and announces updates as and when
bugs are discovered and fixed. 

### How To be helpful:

If you discover a crash, install the GDB debugger with 'sudo apt install gdb'
The debug-tools/gdbwait script is provided so that you can monitor the process while it is running. If it crashes
you can generate what is known as a 'stack trace', this is super-useful for tracking down the bug.
If you encounter a crash, while running gdb via this script, the debugger will prompt you for action
before it will allow the process to die. At this prompt, type 'bt' or 'backtrace' and GDB will spew
a bunch of text to the screen. Copy and paste this into any bug report you might make together
with a description of the steps you made which lead to the issue (if possible).


http://groups.io/g/bpq32



