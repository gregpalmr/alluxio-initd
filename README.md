# alluxio-initd
An example of starting Alluxio daemons using Linux init.d start/stop scripts

This git repo offers some example Linux init.d script for starting and stopping Alluxio daemons. It should be noted that most Red Hat deployments now use the systemd sub-system to manage services (see https://www.linux.com/training-tutorials/understanding-and-using-systemd).  If you would like to review some examples of starting Alluxio daemons using systemd commands (systemctl, journalctl), see https://github.com/gregpalmr/alluxio-systemd.

# Usage

Step 1. Clone this git repo, or download the source files

     git clone https://github.com/gregpalmr/alluxio-initd

     or, download the repo zip file, by clicking on the green "Code" button.

Step 2. Modify the init.d scripts

Step 3. Copy the Alluxio init.d script to the /etc/init.d directory

When you copy the Alluxio init.d scripts to the /etc/init.d directory, they will be staged to the appropriate /etc/rc.d/rc[0-6].d/ directories, which coorispond to each run-level. They will be "linked" from the /etc/rc.d directories to the /etc/init.d/alluxio-* scripts.

     sudo cp init.d-scripts/alluxio-master /etc/init.d/
     sudo cp init.d-scripts/alluxio-job-master /etc/init.d/
     sudo cp init.d-scripts/alluxio-proxy /etc/init.d/
     sudo cp init.d-scripts/alluxio-logserver /etc/init.d/
     sudo chmod +x /etc/init.d/alluxio-*

Step 4. Enable the Alluxio init.d scripts

Use the Linux chkconfig command to place the Alluxio init.d scripts in the correct /etc/rc.d directories, based on the required start order.

     sudo chkconfig --add alluxio-master
     sudo chkconfig --add alluxio-job-master
     sudo chkconfig --add alluxio-proxy
     sudo chkconfig --add alluxio-logserver

     chkconfig --list
     ...
     alluxio-master	0:off	1:off	2:on	3:on	4:on	5:on	6:off
     alluxio-job-master	0:off	1:off	2:on	3:on	4:on	5:on	6:off
     alluxio-proxy	0:off	1:off	2:on	3:on	4:on	5:on	6:off
     alluxio-logserver	0:off	1:off	2:on	3:on	4:on	5:on	6:off
     ...

Step 5. Reboot server to test init.d scripts

Now that you have anabled the Alluxio init.d scripts, the Linux init daemon should run the scripts and start the daemons at boot time. Reboot the server to test that functionality.

     shutdown -r 0

Step 6. Disable and re-enable as needed

     chkconfig alluxio-master off

     chkconfig --list
     ...
     alluxio-logserver	0:off	1:off	2:off	3:off	4:off	5:off	6:off
     ...

     chkconfig alluxio-master on
     ...
     alluxio-master	0:off	1:off	2:on	3:on	4:on	5:on	6:off
     ...

---
Please direct any comments or questions to greg.palmer@alluxio.com

