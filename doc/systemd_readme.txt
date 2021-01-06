Installing linbpq as a system service
=====================================

Note in the below example there is a line referencing an application /usr/bin/screen. If you do not have the screen package installed, it is highly recommended to do so- screen will create a virtual console for linbpq, ideal for checking, diagnostics, debug/errors etc.

   sudo apt install screen

When the node is running, while logged in as the user defined in the linbpq.service:

To attach:

   screen -r linbpq

To detatch, press key combination sequence:

   Ctrl-a d



Create the runbpq Script
========================
Note: this file is already provided with this distribution, you must edit the path in it.

I recommend running a script rather than the binary directly so that if you need to add TNC drivers or bind additional devices such as bluetooth TNCs, this would be the ideal place to put them In sequence.

#!/bin/bash
#
# Edit to reflect the directory location of linBPQ installation
cd /home/bpq/nodes/pe1rrr
#
./linbpq



Set permissions
===============

Set execute and the correct USERID and GROUP permissions on the runbpq script AND linbpq binary. This will make it run with the correct user ID if at any point it is launched manually.

   sudo chmod +x runbpq
   chown <userid>:<groupid> runbpq linbpq
   sudo chmod u+s runbpq linbpq

Note: Some of the above may not be necessary if you have used the update-linbpq.sh script, as this sets the permissions for you.

Starting linbpq from systemd
============================

Create the file linbpq.service with the following contents:

Note: this file is provided in the doc/ directory with this distribution!
You will need to edit the paths below to reflect your installation.


[Unit]
Description=LinBPQ
After=network.target

[Service]
Type=forking
WorkingDirectory=/home/bpq/nodes/pe1rrr
Restart=always
User=bpq
Group=bpq
ExecStart=/usr/bin/screen -S linbpq -d -m /home/bpq/nodes/pe1rrr/runbpq
#ExecStart=/bin/bash /home/bpq/nodes/pe1rrr/runbpq
SyslogIdentifier=LinBPQ

[Install]
WantedBy=multi-user.target


(end of the file)


Copy or move linbpq.service into the /etc/systemd/system/ directory

   sudo cp ./linbpq.service /etc/systemd/system/
   cd /etc/systemd/system/

Then run:

   sudo systemctl daemon-reload
   sudo systemctl enable linbpq.service

Which does this:

Created symlink /etc/systemd/system/multi-user.target.wants/linbpq.service → /etc/systemd/system/linbpq.service.

LinBPQ should now start from boot and the service can be stopped/started/restarted with:

   sudo service linbpq stop/start/restart

Further tips
============

Console access:

After linbpq has started up it is possible to capture the terminal by using the 'screen -r' command so long
as you are logged in as the same user as used by linbpq. To detatch from the console/terminal, see the top
of this document!

Systemd diagnostics:

You can check your systemd startup sequence status issuing:

   sudo systemctl status
