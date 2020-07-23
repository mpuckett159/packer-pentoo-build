packer build -var-file=/path/to/variables.json /path/to/pentoo-builder.json

Relies on boot_boot commands need to test running stuff over SSH after I figure out the AWS AMI stuff

Setting the keymap for the LiveCD environment would be super hard I think because you gotta time stuff weirdly so just letting it boot all the way to the live environment using the US keymap.

variables list:
LIVECD_PASSWORD - password set for the LiveCD environment, does not persist after reboots
SYSTEM_PASSWORD - root password set for installed environment
NEW_USERNAME - user to create on the new system
USER_PASSWORD - password to set for the new user
ISO_PATH - path to ISO file, can be absolute or relative

Because I'm completely relying on the boot_commands it's kind of hard to figure out what exactly is being done by the commands so here's an explainer:

Sets the password for the LiveCD environment</br>
\{\{user `LIVECD_PASSWORD` \}\}\<enter\>\<wait1s\>\{\{user `LIVECD_PASSWORD` \}\}\<enter\>\<wait1s\>\",

Starts networking, should allow me to do SSH stuff in the future</br>
\"sudo dhcpcd eth0\<enter\>\<wait15s\>\",

starts the pentoo-installer TUI</br>
\"sudo pentoo-installer\<enter\>\<wait15s\>\",

goes from the welcome screen to enter the Set Clock menu, sets system time to UTC</br>
\"\<enter\>\<wait1s\>\<enter\>\<wait1s\>\<enter\>\<wait5s\>\",

Sets the timezone to US/Pacific, sets calendar to whatever the LiveCD sees</br>
\"2\<enter\>\<wait1s\>44444444444\<enter\>\<wait1s\>222\<enter\>\<wait1s\>\<enter\>\<wait5s\>\",

Enters Prepare Hard Drive menu and selects Auto-Prepare</br>
\"\<enter\>\<wait1s\>\<enter\>\<wait5s\>\",

Selects the default disk \(probably /dev/sda/\)</br>
\"\<enter\>\<wait1s\>\",

Sets the partition sizes for the various partitions \(/boot and swap then what's left is taken by /\), you could set custom stuff if you wanted but decided to just leave defaults for now.</br>
\"\<enter\>\<wait1s\>\<enter\>\<wait1s\>\<enter\>\<wait1s\>\<enter\>\<wait1s\>\<enter\>\<wait1s\>\<left\>\<enter\>\<wait5s\>\<enter\>\<wait1s\>\<down\>\<enter\>\<wait5s\>\",

Copy the Distribution. This is probably gonna be where you gotta change the wait values depending on your system resources</br>
\"\<enter\>\<wait5m\>\<left\>\<enter\>\<wait2m\>\",

Selects the default profile \(pentoo/hardened/linux/amd64/binary\) easy to use \<up\> or \<down\> to select pentoo/hardened/linux/amd64 or pentoo/hardened/linux/amd64/bleeding_edge respectively</br>
\"\<enter\>\<wait5s\>\<enter\>\<wait5s\>\",

Enters Configure System menu</br>
\"\<enter\>\<wait5s\>\",

Selects nano, easy to user \<down\> to change the text editor selected</br>
\"\<enter\>\<wait1s\>\",

Skips all the /etc/ files to change and selects the Root-Password set</br>
\"\<down\>\<down\>\<down\>\<down\>\<down\>\<down\>\<down\>\",

Sets the root password for the system</br>
\"\<enter\>\<wait1s\>\{\{user `SYSTEM_PASSWORD` \}\}\<enter\>\<wait1s\>\{\{user `SYSTEM_PASSWORD` \}\}\<enter\>\<wait1s\>\",

Selects the Add a new user menu</br>
\"\<down\>\<down\>\<down\>\<down\>\<down\>\<down\>\<down\>\<down\>\",

Sets the new user and its password</br>
\"\<enter\>\<wait1s\>\{\{user `NEW_USERNAME` \}\}\<enter\>\<wait1s\>\{\{user `USER_PASSWORD` \}\}\<enter\>\<wait1s\>\{\{user `USER_PASSWORD` \}\}\<enter\>\<wait1s\>\",

hit d to get down to the bottom without having to do stupid amount of \<down\>'s</br>
\"d\<enter\>\<wait5s\>\",

Return to Main Menu</br>
\"\<enter\>\<wait1s\>\",

Installs GRUB as the BootLoader</br>
\"\<enter\>\<wait1s\>\<left\>\<enter\>\<wait1s\>\<leftCtrlOn\>X\<leftCtrlOff\>\<wait3m\>\<enter\>\<wait1s\>\",

exits out of the installer</br>
\"\<enter\>\<wait5s\>\"

After that Packer will pick back up and do the rest of what it needs to do to export the VM automagically.

Referenced this build processf or installing Pentoo, can follow it along for the most part though I skip over some things it does.

https://github.com/pentoo/pentoo-overlay/wiki/Pentoo-Installer
