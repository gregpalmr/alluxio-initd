# alluxio-initd
An example of starting Alluxio daemons using Linux init.d start/stop scripts

This git repo offers some example Linux init.d scripts for starting and stopping Alluxio daemons. 

It should be noted that most Red Hat deployments now use the systemd sub-system to manage services (see https://www.linux.com/training-tutorials/understanding-and-using-systemd).  If you would like to review some examples of starting Alluxio daemons using systemd commands (systemctl, journalctl), see https://github.com/gregpalmr/alluxio-systemd.

# Usage

### Step 1. Clone this git repo, or download the source files

     git clone https://github.com/gregpalmr/alluxio-initd

or, download the repo zip file, by clicking on the green "Code" button.

### Step 2. Modify the init.d scripts

In each Alluxio init.d script there is a section at the top of the file that specifies when the script will be executed. For example, here is the section for the alluxio-master init.d script that shows it will be started in run levels 3, 4 and 5 and will only be started after some other services are started, like $network, $named, $syslog and others.

     ### BEGIN INIT INFO
     # Provides:        alluxio-master
     # Required-Start:  $local_fs $remote_fs $network $named $syslog $time
     # Required-Stop:   $local_fs $remote_fs $network $named $syslog $time
     # Default-Start:   3 4 5
     # Default-Stop:
     # Short-Description: Example of Start/Stop Alluxio Master
     ### END INIT INFO

You may modify this section to customize the startup behavior. For instance if you want the Alluxio daemons to be started AFTER the Puppet process starts and does its work, you can specify "$puppet" in the "Required-Start" section (or what ever you named the Puppet unit in its init.d script). For example:

     # Required-Start:  $local_fs $remote_fs $network $named $syslog $time $puppet

You can also change the run-levels that the script will be executed on by modifying the "Default-Start" specification. Here is a list of run levels on Red Hat EL servers.

Run Level | Mode | Action
--- | --- | ---
0 | Halt | Shuts down system
1 | Single-User Mode | Does not configure network interfaces, start daemons, or allow non-root logins
2 | Multi-User Mode | Does not configure network interfaces or start daemons.
3 | Multi-User Mode with Networking | Starts the system normally.
4 | Undefined | Not used/User-definable
5 | X11 | As runlevel 3 + display manager(X)
6 | Reboot | Reboots the system

You may also modify the user that will start the Alluxio daemons, as well as the ALLUXIO_HOME directory, if you installed it in a non-traditional location. Here is an example with the user set to "alluxio", but you can change it to "root" if you want the root user to start the daemons.

     LINUX_USER=alluxio
     ALLUXIO_HOME=/opt/alluxio

### Step 3. Copy the Alluxio init.d script to the /etc/init.d directory

When you copy the Alluxio init.d scripts to the /etc/init.d directory, they will be staged to the appropriate /etc/rc.d/rc[0-6].d/ directories, which coorispond to each run-level. They will be "linked" from the /etc/rc.d directories to the /etc/init.d/alluxio-* scripts.

On the Alluxio master nodes, copy these scripts:

     sudo cp init.d-scripts/alluxio-master /etc/init.d/
     sudo cp init.d-scripts/alluxio-job-master /etc/init.d/
     sudo cp init.d-scripts/alluxio-proxy /etc/init.d/
     sudo cp init.d-scripts/alluxio-logserver /etc/init.d/
     sudo chmod +x /etc/init.d/alluxio-*

On the Alluxio worker nodes, copy these scripts:

     sudo cp init.d-scripts/alluxio-worker /etc/init.d/
     sudo cp init.d-scripts/alluxio-job-worker /etc/init.d/
     sudo cp init.d-scripts/alluxio-proxy /etc/init.d/
     sudo chmod +x /etc/init.d/alluxio-*

### Step 4. Enable the Alluxio init.d scripts

Use the Linux `chkconfig` command to place the Alluxio init.d scripts in the correct /etc/rc.d directories, based on the required start order.

On the Alluxio master nodes, enable these scripts:

     sudo chkconfig --add alluxio-master
     sudo chkconfig --add alluxio-job-master
     sudo chkconfig --add alluxio-proxy
     sudo chkconfig --add alluxio-logserver

On the Alluxio worker nodes, enable these scripts:

     sudo chkconfig --add alluxio-worker
     sudo chkconfig --add alluxio-job-worker
     sudo chkconfig --add alluxio-proxy

Now if you look at the sub-directories under the /etc/rc.d directory, you will see where the file links were created:

     find /etc/rc.d/ | grep alluxio-master
     ...
     /etc/rc.d/rc3.d/S50alluxio-master
     /etc/rc.d/rc4.d/S50alluxio-master
     /etc/rc.d/rc5.d/S50alluxio-master
     ...

Or you can use the `chkconfig` command to list the new services:

     chkconfig --list
     ...
     alluxio-master     0:off	1:off	2:off	3:on	4:on	5:on	6:off
     alluxio-job-master 0:off	1:off	2:off	3:on	4:on	5:on	6:off
     alluxio-proxy      0:off	1:off	2:off	3:on	4:on	5:on	6:off
     alluxio-logserver  0:off	1:off	2:off	3:on	4:on	5:on	6:off
     ...

### Step 5. Reboot server to test init.d scripts

Now that you have anabled the Alluxio init.d scripts, the Linux init process should run the scripts and start the Alluxio daemons at boot time. Reboot the server to test that functionality.

     shutdown -r 0

### Step 6. Disable and re-enable as needed

     sudo chkconfig alluxio-master off

     chkconfig --list
     ...
     alluxio-logserver  0:off	1:off	2:off	3:off	4:off	5:off	6:off
     ...

     sudo chkconfig alluxio-master on

     chkconfig --list
     ...
     alluxio-master     0:off	1:off	2:off	3:on	4:on	5:on	6:off
     ...

---
Please direct any comments or questions to greg.palmer@alluxio.com

