# crashplan-docker-synology

This is an attempt to get CrashPlan running on a Synology NAS inside a Docker
container. It has been tested successfully in the following configuration:

Device: Synology DS713+  
RAM: 4GB  
DSM Version: 5.2-5644 Update 1

I have been running the [PC Load Letter CrashPlan Packages](http://pcloadletter.co.uk/2012/01/30/crashplan-syno-package/)
for a long time. They certainly made it much easier to deal with CrashPlan updates,
but it was still fairly common for me to have issues after new releases. When I had
issues with the 4.4.1 release, I decided to see if Docker would be a smoother way forward.

## Assumptions and Precautions

This documentation assumes that:

* You have a CrashPlan account, a Synology device and some experience using them
together.
* You are comfortable connecting to your Synology device using SSH and running
terminal commands.
* Docker experience *shouldn't* be necessary, but can't hurt :).

Before trying to run CrashPlan in a Docker container, it's a good idea to:

* Back up your CrashPlan configuration files related to any currently installed
instances of CrashPlan, in case you decide to switch back to your previous setup.
* Stop or uninstall existing CrashPlan instances.

## Quick Start

* Ensure that the Docker package is installed through DSM's Package Manager.
* Copy the `S99crashplandocker.sh` script to the `/usr/local/etc/rc.d` directory.
* Run `/usr/local/etc/rc.d/S99crashplandocker.sh start` to start the container.
* Connect to the headless service using CrashPlan Desktop on a remote computer.
See [CrashPlan's documentation](http://support.code42.com/CrashPlan/4/Configuring/Using_CrashPlan_On_A_Headless_Computer)
for help with this, and consult `/usr/local/etc/rc.d/S99crashplandocker.sh status`
for details about the running CrashPlan instance.

## Customization

This script can be customized to suit your specific needs. The options most
likely to need tweaking are:
* `USER_VOLUMES`: The `-v` options passed to the `docker run` command. These
control the paths that will be mounted inside the container. 
* `CRASHPLAN_DIR`: The CrashPlan home/installation directory. When switching
from an existing install of CrashPlan, pointing this variable to your current
installation may avoid the need to "adopt" the previous installation from
the CrashPlan client.
* `DATA_DIR`: The destination for incoming backups. Setting this to a data
directory from an existing installation will save some time synchronizing
backup progress.

## Caveats

* Because this script sets up mounted volumes that are not accessible from the
DSM GUI, trying to manage this container from the Synology Docker GUI will
break it. Checking status and resource utilization with the GUI is fine, but
trying to start the container from the GUI will break the volume configuration.
If that happens, remove the container and run
`/usr/local/etc/rc.d/S99crashplandocker.sh start` again to set it up anew.

* Anytime the container stops and restarts, it's going to overwrite the `.ui_info`
file. That file contains an authentication token GUID that needs to be the same
on the CrashPlan Desktop client and the server. Before trying to start your CrashPlan
client, run `/usr/local/etc/rc.d/S99crashplandocker.sh status` and take note of
the `Auth Token`. Use that to update the `.ui_info` file on your client machine.
See CrashPlan's [documentation for headless computers](http://support.code42.com/CrashPlan/4/Configuring/Using_CrashPlan_On_A_Headless_Computer#Step_1:_Copy_The_Authentication_Token)
for more information. 

## Acknowledgements

* gfjardim's Docker Image for CrashPlan (https://hub.docker.com/r/gfjardim/crashplan/)
was an excellent starting point. I forked it into the `ajkerrigan/crashplan` image
because I expected to need to make changes, and I wanted to accept ownership for it
going forward. As of this writing I haven't needed to make a change to the image.

* patters over at [PC Load Letter](http://pcloadletter.co.uk/) is a damn hero.
He has been wrestling with getting CrashPlan to work on Synology devices for years,
and has done a lot of work so that people like me could be lazier. He accepts donations,
and damn well deserves them. His
[CrashPlan packages](http://pcloadletter.co.uk/2012/01/30/crashplan-syno-package/)
are the only reason I haven't given up running CrashPlan on my NAS by now.

* Scott Hanselman's [blog post](http://www.hanselman.com/blog/UPDATED2014HowToSetupCrashPlanCloudBackupOnASynologyNASRunningDSM50.aspx)
is an excellent walkthrough of getting CrashPlan installed and running. His post is
very readable and supported by plenty of screenshots.

## Troubleshooting

* Running CrashPlan on a Synology device has always been a quirky endeavor. I believe
Docker removes some of the wonkiness, but I'm sure there are issues I haven't run into
yet. I am not an expert on this stuff, but I've broken and fixed enough CrashPlan
installations that I'm familiar with some of the ways things break. If you have issues
you can't fix on your own, please open an issue in GitHub and I'll try to help.

## Contributing

* Do you have suggestions for how this script can be better written or better documented?
Please open an issue, or fix it yourself and submit a pull request.

## Additional Resources

* [PC Load Letter's CrashPlan packages](http://pcloadletter.co.uk/2012/01/30/crashplan-syno-package/).
If you're not using Docker, use those packages.
* Docker Hub pages for the [ajkerrigan/crashplan](https://hub.docker.com/r/ajkerrigan/crashplan/) image,
or the [gfjardim/crashplan](https://hub.docker.com/r/gfjardim/crashplan/) image it was based on.
* gfjardim's [docker-containers](https://github.com/gfjardim/docker-containers) GitHub repository.
* [CrashPlan Headless documentation](http://support.code42.com/CrashPlan/4/Configuring/Using_CrashPlan_On_A_Headless_Computer).