# iOS 11 Untethered Setup.app Removal
This guide is for an obsolete iOS version while I still wrote it down, as I could not find a feasible iOS 11 setup.app tutorial that mentions `snaputil`. For those who are trying to remove setup.app on an iOS 11 device without updating, only to find that checkra1n being incompatible, or setup.app still exists after being removed via SSH ramdisk, it might help.
## Preparation
- A USB-A cable
- A computer running linux or macOS
- Clone [SSHRD_Script](https://github.com/verygenericname/SSHRD_Script) and cd into its directory (TYVM Nathan verygenericname for such a mighty tool)  
  `git clone https://github.com/verygenericname/SSHRD_Script --recursive && cd SSHRD_Script`
- Place your device into DFU mode
## Remove Setup.app
- Create a ramdisk, system partition can be mounted with various versions of ramdisk, 11.3 is just an example here  
  `./sshrd.sh 11.3`
- Boot the ramdisk  
  `./sshrd.sh boot`
- On linux, usbmuxd needs to be restarted, open another terminal, run the following command and leave that terminal open. On macOS simply skip this step  
  `sudo systemctl stop usbmuxd; sudo usbmuxd -pf`
- Connect to SSH  
  `./sshrd.sh ssh`
- Mount system partition  
  `mount_apfs /dev/disk0s1s1 /mnt1`
- Obtain snapshot name on system partition  
  `snaputil -l /mnt1`
- The snapshot name will be displayed, it should be a random string like com.apple.os.update-xxxxxxxx. Run the following command to rename snapshot, replace $name with the one displayed above  
  `snaputil -n $name orig-fs /mnt1`
- Remove setup.app  
  `rm -rf /mnt1/Applications/Setup.app`
- On iOS 10+, the device needs to be reset after removing setup.app via ramdisk  
  `/usr/sbin/nvram oblit-inprogress=5`
- Reboot, if the device fails to reboot, you may do it manually  
  `/sbin/reboot`
## Notes
- Sometimes SSHRD_Script randomly says error init failed or an error occurred, check internet connection and run previous command again
- On early iOS 11 there is no snapshot on /mnt1, probably 11.0-11.2.6. If nothing is displayed after running `snaputil -l /mnt1`, skip next step
- It is a bare-bones bypass, allowing you to access home screen but many things will be broken, such as cellular, iCloud, sideloading, iTunes sync, notifications, etc.
- This guide is about hello screen setup.app removal, for passcode locked iOS 11 devices, try using [this modified SSHRD_Script](https://github.com/iPh0ne4s/SSHRD_Script) to backup and restore activation files without updating
