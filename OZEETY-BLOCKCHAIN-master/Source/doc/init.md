Sample init scripts and service configuration for ozeetyd
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/ozeetyd.service:    systemd service unit configuration
    contrib/init/ozeetyd.openrc:     OpenRC compatible SysV style init script
    contrib/init/ozeetyd.openrcconf: OpenRC conf.d file
    contrib/init/ozeetyd.conf:       Upstart service configuration file
    contrib/init/ozeetyd.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three startup configurations assume the existence of a "ozeety" user
and group.  They must be created before attempting to use these scripts.

2. Configuration
---------------------------------

At a bare minimum, ozeetyd requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, ozeetyd will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that ozeetyd and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If ozeetyd is run with "-daemon" flag, and no rpcpassword is set, it will
print a randomly generated suitable password to stderr.  You can also
generate one from the shell yourself like this:

bash -c 'tr -dc a-zA-Z0-9 < /dev/urandom | head -c32 && echo'

Once you have a password in hand, set rpcpassword= in /etc/ozeety/ozeety.conf

For an example configuration file that describes the configuration settings,
see contrib/debian/examples/ozeety.conf.

3. Paths
---------------------------------

All three configurations assume several paths that might need to be adjusted.

Binary:              /usr/bin/ozeetyd
Configuration file:  /etc/ozeety/ozeety.conf
Data directory:      /var/lib/ozeetyd
PID file:            /var/run/ozeetyd/ozeetyd.pid (OpenRC and Upstart)
                     /var/lib/ozeetyd/ozeetyd.pid (systemd)

The configuration file, PID directory (if applicable) and data directory
should all be owned by the ozeety user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
ozeety user and group.  Access to ozeety-cli and other ozeetyd rpc clients
can then be controlled by group membership.

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists on just copying it to
/usr/lib/systemd/system directory, followed by the command
"systemctl daemon-reload" in order to update running systemd configuration.

To test, run "systemctl start ozeetyd" and to enable for system startup run
"systemctl enable ozeetyd"

4b) OpenRC

Rename ozeetyd.openrc to ozeetyd and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
"/etc/init.d/ozeetyd start" and configure it to run on startup with
"rc-update add ozeetyd"

4c) Upstart (for Debian/Ubuntu based distributions)

Drop ozeetyd.conf in /etc/init.  Test by running "service ozeetyd start"
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon uitility.

4d) CentOS

Copy ozeetyd.init to /etc/init.d/ozeetyd. Test by running "service ozeetyd start".

Using this script, you can adjust the path and flags to the ozeetyd program by
setting the ozeetyD and FLAGS environment variables in the file
/etc/sysconfig/ozeetyd. You can also use the DAEMONOPTS environment variable here.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
