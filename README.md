If you're trying to upload to AWS and convert to an AMI you'll need to use the following. Note that you also need a role named vmimport that I don't understand how to set permissions for exactly to get it to import end to end.

packer build -var AWS_ACCESSKEY=<access-key> -var AWS_SECRETKEY=<secret-key> -var-file=/path/to/variables.json /path/to/pentoo-builder.json

If you don't want to try automating end to end with importing the OVA into AWS and converting to an AMI it'll look like this:

packer build -var-file=/path/to/variables.json /path/to/pentoo-builder.json

You'll also need to pull out the post-processors block in the build file. 

After that you can just import the OVA into S3 and convert to an AMI manually.

Note: the reason there's so many <wait100ms> in the VirtualBox one is that it would appear that Packer doesn't limit the rate of key presses like it does for Workstation.

Setting the keymap for the LiveCD environment would be super hard I think because you gotta time stuff weirdly so just letting it boot all the way to the live environment using the US keymap.

variables list:
LIVECD_PASSWORD - Password for liveCD environment (pentoo user) persists to new system
ISO_PATH - Path to the ISO file location
ISO_CHECKSUM - ISO checksum (e.g. "md5:1C9BFC24BC11F7A854EFA8CDDAB1A4FA")
OUTPUT_DIR - Output directory for the OVA file