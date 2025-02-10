# Tie Fighter Nix Setup

## Disk setup

* Download ISO Image from https://nixos.org/download.html#nixos-iso
* Find USB drive to install to with `lsblk` (note this will probably be `/dev/sda` or `/dev/sdb` but make sure it's the USB drive NOT your hard drive unless you want to have a bad day)
* Create USB installation with `dd` (note: `/dev/sdX` should be changed to your USB drive from previous step)
  and `nixos.iso` should be the path to the nixos iso you downloaded in the first step
  ```bash
  sudo dd if=~/Downloads/nixos.iso of=/dev/sdX bs=4M status=progress conv=fdatasync
  ```
* Startup computer from USB drive by using the BIOS (use F2 during boot)
* Use the sudoer user as all of the following requires root `sudo -s`
* POINT OF NO RETURN - once the next step is complete, you will no longer have a computer
* Run `fdisk /dev/diskx` where `diskx` is the hard drive of the computer to partition
  * For a UEFI partition, the following is slighly different to the nixos.wiki guide as it
    includes a SWAP partition (see the youtube link, below)
    * `g` (gpt disk label)
    * `n`
    * `1` (partition number [1 of 128])
    * `2048` first sector
    * `+500M` last sector (boot sector size)
    * `t`
    * `1` (EFI System)
    * `n` (swap partition)
    * `2` (partition number [2 of 128])
    * `default` (starting sector)
    * `+4096M` (boot sector size)
    * `t`
    * `2`
    * `19` (Linux Swap)
    * `n`
    * `3` (partition number [3 of 128])
    * default (fill up partition)
    * default (fill up partition)
    * `w` (write - this will wipe the disk)
* `mkfs.fat /dev/sda1` set first partition as FAT
* `mkswap /dev/sda2` set second partition as SWAP
* `swapon /dev/sda2` use second partition SWAP
* `mkfs.ext4 /dev/sda3` set third partition as ext4 for linux filesystem
* `cd` go to `/`
* `mount /dev/sda3 /mnt` mount linux filesystem on `/mnt`
* `mkdir /mnt/boot` make boot partition folder
* `mount /dev/sda1 /mnt/boot` mount boot partition (sda1) on boot partition folder
* `cd /mnt`
* `nixos-generate-config --root /mnt` generate starting config
* you can check the starting config at `/mnt/etc/nixos/configuration.nix`
* `nixos-install` install the configuration.nix start
* set the root password for the system
* `reboot` restart the system to start using it
* login with `root` user unless you added a user in configuration.nix
* You may need to fix the permissions on `/boot`
  ```bash
  chmod -R 700 /boot
  ```

### Links

* https://nixos.wiki/wiki/NixOS_Installation_Guide
* https://www.youtube.com/watch?v=63sSGuclBn0&list=PLuRxZ95-8LY1mlotZMYGYib5sXJRw1RxW&index=1 NOTE this includes a GUI installation AND a manual one at 4:02

## Home Manager

```bash
sudo nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
sudo nix-channel --update
```

### Links

* https://nix-community.github.io/home-manager/index.xhtml
* https://librephoenix.com/2024-03-14-managing-your-nixos-config-with-git
