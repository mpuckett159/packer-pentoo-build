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

"boot_command": [
/    ## Sets the password for the LiveCD environment
    "{{user `LIVECD_PASSWORD` }}<enter><wait1s>{{user `LIVECD_PASSWORD` }}<enter><wait1s>",
  
/    ## Starts networking, should allow me to do SSH stuff in the future
    "sudo dhcpcd eth0<enter><wait15s>",
    
/    ## starts the pentoo-installer TUI
    "sudo pentoo-installer<enter><wait15s>",
    
/    ## goes from the welcome screen to enter the Set Clock menu, sets system time to UTC
    "<enter><wait1s><enter><wait1s><enter><wait5s>",
    
/    ## Sets the timezone to US/Pacific, sets calendar to whatever the LiveCD sees
    "2<enter><wait1s>44444444444<enter><wait1s>222<enter><wait1s><enter><wait5s>",
  
/    ## Enters Prepare Hard Drive menu and selects Auto-Prepare
    "<enter><wait1s><enter><wait5s>",
    
/    ## Selects the default disk (probably /dev/sda/)
    "<enter><wait1s>",
    
/    ## Sets the partition sizes for the various partitions (/boot and swap then what's left is taken by /), you could set custom stuff if you wanted but decided to just leave defaults for now.
    "<enter><wait1s><enter><wait1s><enter><wait1s><enter><wait1s><enter><wait1s><left><enter><wait5s><enter><wait1s><down><enter><wait5s>",
    
/    ## Copy the Distribution. This is probably gonna be where you gotta change the wait values depending on your system resources
    "<enter><wait5m><left><enter><wait2m>",
    
/    ## Selects the default profile (pentoo/hardened/linux/amd64/binary) easy to use <up> or <down> to select pentoo/hardened/linux/amd64 or pentoo/hardened/linux/amd64/bleeding_edge respectively
    "<enter><wait5s><enter><wait5s>",
    
/    ## Enters Configure System menu
    "<enter><wait5s>",
    
/    ## Selects nano, easy to user <down> to change the text editor selected
    "<enter><wait1s>",
    
/    ## Skips all the /etc/ files to change and selects the Root-Password set
    "<down><down><down><down><down><down><down>",
    
/    ## Sets the root password for the system
    "<enter><wait1s>{{user `SYSTEM_PASSWORD` }}<enter><wait1s>{{user `SYSTEM_PASSWORD` }}<enter><wait1s>",
    
/    ## Selects the Add a new user menu
    "<down><down><down><down><down><down><down><down>",
    
/    ## Sets the new user and its password
    "<enter><wait1s>{{user `NEW_USERNAME` }}<enter><wait1s>{{user `USER_PASSWORD` }}<enter><wait1s>{{user `USER_PASSWORD` }}<enter><wait1s>",
    
/    ## hit d to get down to the bottom without having to do stupid amount of <down>'s
    "d<enter><wait5s>",
    
/    ## Return to Main Menu
    "<enter><wait1s>",
    
/    ## Installs GRUB as the BootLoader
    "<enter><wait1s><left><enter><wait1s><leftCtrlOn>X<leftCtrlOff><wait3m><enter><wait1s>",
    
/    ## exits out of the installer
    "<enter><wait5s>"
]

After that Packer will pick back up and do the rest of what it needs to do to export the VM automagically.
