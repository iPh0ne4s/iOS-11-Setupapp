# iOS 11 Untethered Setup.app Removal

This guide is for an obsolete iOS version while I still wrote it down, as I could not find a feasible iOS 11 setup.app tutorial that mentions `snaputil`. For those who are trying to remove setup.app on an iOS 11 device without updating, only to find that checkra1n being incompatible, or setup.app still exists after being removed via SSH ramdisk, it might help.

## Preparation

- A USB-A cable

- A computer running linux or macOS

- Clone [SSHRD_Script](https://github.com/verygenericname/SSHRD_Script) and cd into its directory (TYVM verygenericname for such a mighty tool):

  `git clone https://github.com/verygenericname/SSHRD_Script --recursive && cd SSHRD_Script`

- Place your device into DFU mode

## Reset Device

- On iOS 10+, devices must be reset and placed into DFU immediately, otherwise removing setup.app via ramdisk will render a bootloop

- Run the following commands:

  `sudo ./sshrd.sh 12.0`

  `sudo ./sshrd.sh boot`

  `sudo ./sshrd.sh ssh`

  `nvram oblit-inprogress=5`

- Reboot, the device will start resetting itself, enter DFU as soon as reset finishes and screen turns black, do not let apple logo appear

## Remove Setup.app

- Boot SSH ramdisk and mount system partition:

  `sudo ./sshrd.sh 12.0`

  `sudo ./sshrd.sh boot`

  `sudo ./sshrd.sh ssh`

  `mount_apfs /dev/disk0s1s1 /mnt1`

- Obtain snapshot name:

  `snaputil -l /mnt1`

- The snapshot name will be displayed, it should be a random string like com.apple.os.update-xxxxxxxx. Run the following command to rename snapshot, replace $name with the one displayed above:

  `snaputil -n $name orig-fs /mnt1`

- Remove setup.app:

  `rm -rf /mnt1/Applications/Setup.app`

- Reboot, if you did everything correctly, you should be able to access home screen

## Notes

- On linux, usbmuxd needs to be restarted. Open another terminal, run the following commands before running `sudo ./sshrd.sh ssh`, leave that terminal open:

  `sudo systemctl stop usbmuxd`

  `sudo usbmuxd -pf`

- In some cases there is no snapshot on /mnt1 (happened on my iPhone 8 11.0 and iPhone X 11.0.1), idk if it is normal on early iOS 11. If nothing is displayed after running `snaputil -l /mnt1`, simply skip this step

- Sometimes SSHRD_Script randomly says an error occurred, try running previous command again
