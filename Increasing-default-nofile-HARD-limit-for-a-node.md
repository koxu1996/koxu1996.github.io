## Number of Files Limit

When `casper-node` launches, it tries to set the `nofiles` for the process to `64000`.  With some systems, this will
hit the default hard limit of `4096`.   

Filehandles are used for both files and network connections.  The network connections are unpredictable and running 
out of file handles can stop critical file writes from occurring.  This limit may need to be increased from defaults.

With `casper-node-launcher` running we can see what the system allocated by finding our process id (PID) for `casper-node`
with `pgrep "casper-node$"`.

```shell
$ pgrep "casper-node$"
275928
```

This PID will change so you need to run the above command to get the current version with your system.  
It will not be `275928` each time. If you get no return, you do not have `casper-node-launcher` running properly.

To find the current `nofile` (number of open files) hard limit, we can run `prlimit` with this PID:

```shell
$ sudo prlimit -n -p 275928
RESOURCE DESCRIPTION              SOFT HARD UNITS
NOFILE   max number of open files 1024 4096 files
```

We can embed both commands together so it is only `sudo prlimit -n -p $(pgrep "casper-node$")`.

```shell
$ sudo prlimit -n -p $(pgrep "casper-node$")
RESOURCE DESCRIPTION              SOFT HARD UNITS
NOFILE   max number of open files 1024 4096 files
```

If you receive `prlimit: option requires an argument -- 'p'` with the above command then `pgrep "casper-node$"` is not
returning anything because `casper-node-launcher` is no longer running.

### Manual increase

This is how you set `nofile` for an active process.  It will make sure you don't have issues without having to 
restart the `casper-node-launcher` and your node's `casper-node` process.

We run `sudo prlimit --nofile=64000 --pid=$(pgrep "casper-node$")`.

After this when we look at `prlimit` it should show the change:

```shell
$ sudo prlimit -n -p $(pgrep "casper-node$")
RESOURCE DESCRIPTION               SOFT  HARD UNITS
NOFILE   max number of open files 64000 64000 files
```

This is only active while the `casper-node` process is active and therefore will not persist across server reboots, 
casper-node-launcher restarts, and protocol upgrades.  We need to do something else to make this permanent.

### limits.conf

Adding the `nofile` setting for `casper` user in `/etc/security/limits.conf` will persist this value.

Add:

`casper          hard    nofile          64000`

to the bottom of `/etc/security/limits.conf`.  

After doing this you need to log out of any shells you have to enable this change. Restarting the node should
maintain the correct `nofile` setting.
