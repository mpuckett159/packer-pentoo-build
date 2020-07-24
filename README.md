packer build -var-file=/path/to/variables.json /path/to/pentoo-builder.json

Note: the reason there's so many <wait100ms> in the VirtualBox one is that it would appear that Packer doesn't limit the rate of key presses like it does for Workstation.

Setting the keymap for the LiveCD environment would be super hard I think because you gotta time stuff weirdly so just letting it boot all the way to the live environment using the US keymap.

variables list:
LIVECD_PASSWORD - Password for liveCD environment (pentoo user) persists to new system
ISO_PATH - Path to the ISO file location
ISO_CHECKSUM - ISO checksum (e.g. "md5:1C9BFC24BC11F7A854EFA8CDDAB1A4FA")
OUTPUT_DIR - Output directory for the OVA file