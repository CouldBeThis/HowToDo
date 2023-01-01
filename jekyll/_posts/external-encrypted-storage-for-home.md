# how to use encrypted external storage for /home


## not covered here

Please read and make sure you understand each of the following in order to avoid a 🔥disaster🔥 or wasting your time. 

- how to install linux on machine and basic usage
  - this example was created and tested on a fresh install of Gallium OS 31 `Ubuntu bionic` and should be broadly applicable to other systems, but difference may exist if you are using something else 
  - if you are dual-booting Windows, Mac OS, Chrome OS etc, or if you wish to make the contents of the hard drive available to another operating system by plugging it in to another computer, you will need to conduct additional research about how to make that possible
- If you wish to use more than a single user account on the system you will need to do more research as this guide does not cover required user permission set up
- how and when to use `root` user permissions (e.g. via `sudo`) when appropriate and how to edit files as `root`
- 🔥 determining which device is the one you want to manipulate and not accidentally wiping the wrong thing. This will *very likely* be different on your system. In this example:
  - `/dev/sda1` - new drive which will be used as new `/home`
- backing up and transfering files from existing `/home`
  - Here the assumption is made that you have not only backup up but also *removed* all your files as they will be rendered inaccessible without special steps which are not included. They will continue to use storage on your existing partition. You may wait until just before "set up auto mounting at boot" to do this. 
- 🔥 backing up everything else on the computer in case you do the wrong thing and hose your system
- 🔥 how to select and maintain the integrity of your storage medium. In particular, remember that most USB sticks are not reliable enough for long term use like this.  Be sure to make backups in all cases, but especially if using a thumb drive. 

## prepare storage device

This will destroy any data on the device. You will need to use `root` or `sudo` as appropriate. Not covered in detail here.  In this example, it is assumed later (in the section about setting up` /etc/fstab`) that you have used the `ext4` file system.

Connect external device.

**Option A: Using command line:**

1. Partition it if needed (`fdisk`, `cfdisk`, ...)
2. use `mkfs` to create a file system

**Option B: Or if you prefer GUI option:**

1. Use `GParted` to create partition and file system

## encrypt

Assuming the USB has only one partition:  

```sh
sudo cryptsetup luksFormat /dev/sda1
```

You must create a password. Keep it somewhere safe and secure. If you are successful at completing this guide, you will not need to enter the password on a regular basis. But it's very likely that at some point you will need it. It will be really, truly impossible to retrieve your data without the password. 

🔥 Even the NSA can't open your hard drive, encrypted with a strong, unique password, without said password. So keep it somewhere you will be able to find it. 

Create a keyfile and add it to the LUKS partition:

```sh
sudo dd if=/dev/urandom bs=1M count=1 of=/root/home.keyfile
sudo cryptsetup luksAddKey /dev/sda1 /root/home.keyfile
```

Enter the encryption password when prompted. 

### Optional: Add a file to `/home` to check if new `/home` mounted correctly

Since we are assuming you have already removed the files from your existing `/home` directory, it might be helpful to add something obvious. So that later on you can check if it's gone. 

Create a file in the existing `/home`

```sh
touch ~/ThisIsATestFile
```

## mount device

This step is done once, to check everything is working correctly before setting it up to automatically mount and unlock at every boot. 

Enter the encryption password when prompted. 

```sh
sudo cryptsetup open --type luks /dev/sda1 HOME
sudo mount /dev/mapper/HOME /home
```

the last command will make your existing `/home` invisible until you `umount` it, but you anyway did not have anything on it.

Using your terminal or GUI, go to `/home`. There should be only one directory, named after your currently active user. In there you may find already a few files, because applications and the operating system make continuous use of these and just recreated them once they became unavailable. 

If you created  `ThisIsATestFile` in the previous step, it should *not* be visible. If it is, something has gone wrong. Do *not* proceed. 

## set up auto mounting at boot

Note that the device must be opened and mounted as described in previous step to continue. For example if you have rebooted in the meantime, repeat the above 2 lines. 

You may wish to create copies of files before editing them. 

### `/etc/fstab`

Edit `/etc/fstab`:

```zsh
sudo nano /etc/fstab
```

add this:

```
sudo /dev/mapper/HOME /home ext4 auto 0 2
```

Note that if you used a file system other than `ext4`  when setting up the drive, you will need to reflect it. 

### `/etc/crypttab`

Obtain the `UUID` of `/dev/sda1`:

```sh
sudo lsblk -f /dev/sda1
```

example output might be:

```sh
NAME   FSTYPE      LABEL UUID                                 MOUNTPOINT
sda1   crypto_LUKS       e26579d8-ebf4-44aa-9e61-47c8a52a150c
└─HOME ext4        home  704f2fac-e181-4838-97a5-ef2ef58ccf98 /home
```

The correct `UUID` in this case would be `e26579d8-ebf4-44aa-9e61-47c8a52a150c` which is for `sda1`, not the second one which is for `home`.

```zsh
sudo nano /etc/crypttab
```

and this to `/etc/crypttab`:

```
HOME UUID=UUID_OF_DEV_SDA1 /root/home.keyfile luks
```

Replace `UUID_OF_DEV_SDA1` with the `UUID` you just obtained. 









