# Linux Device Management


----------------------
Costa Rica

Belinda Brown, belindabrownr04@gmail.com

[![GitHub](https://badgen.net/badge/icon/github?icon=github&label)](https://github.com) [![Open Source? Yes!](https://badgen.net/badge/Open%20Source%20%3F/Yes%21/blue?icon=github)](https://github.com/Naereen/badges/)

[![GitHub](https://img.shields.io/badge/--181717?logo=github&logoColor=ffffff)](https://github.com/) [brown9804](https://github.com/brown9804)


March, 2022

----------------------

### _Connect to the server_:

`ssh <user_name>@<IPadress>`


## Adding a New Hard Disk to a Linux System:
Understand how to create a new filesystem, mounting the filesystem to a directory, and then configuring the system so the mount persists across reboots.

### _Create a New Partition_:
> Before we go mounting any new partition up, we've got to create that partition.
1. Open up a terminal window and log in using the credentials provided on the lab page, replacing x.x.x.x with the public IP address listed: <br/>
`ssh cloud_user@x.x.x.x`
> Enter the provided password when prompted.
2. Next, let's run the lsblk command to verify we have a /dev/nvme1n1 device available. Once we've confirmed that, we'll create a partition on the /dev/nvme1n1 disk using fdisk. Note that we'll need to preface these commands with sudo for these commands. This partition we create will span the entire disk: <br/>
```
[cloud_user@host]$ lsblk
[cloud_user@host]$ sudo fdisk /dev/nvme1n1
```
3. After running fdisk, we'll have to perform a few tasks. At the Command (m for help): command, type n to make a new partition, then hit Enter. Our Partition type will be p, primary. 
4. Press Enter for Partition number, the First sector, and the Last sector options. This will make fdisk ready to create the partition. Type p at the Command (m for help): to print out what the disk will look like after we commit our changes. 
5. If that all looks good, type w and press Enter to write our changes to disk.

### _Create the Filesystem_:
1. Next, we've got to create a filesystem, so we can read and write data. We'll format the partition to the XFS file system with the mkfs.xfs command. Once that is complete, we'll run blkid on the newly created partition to obtain the UUID. We'll have to make a note of this UUID, since we're going to need it later: <br/>
```
[cloud_user@host]$ sudo mkfs.xfs /dev/nvme1n1p1
[cloud_user@host]$ sudo blkid /dev/nvme1n1p1
```

### _Mount the New Filesystem and Make It Permanent_:
1. We can mount this partition up manually with the mount command, but it won't be a persistent mount; it won't get mounted after something like a reboot.We're going to edit /etc/fstab and create a new entry for the new disk at the bottom: <br/>
`sudo vi /etc/fstab`
2. When you want to add text: hit the esc key and then i to go into insert mode type as normal.
3. When you want to save: hit the esc key and then :wq!
4. You may find the following vim cheat sheet helpful as well: https://linuxacademy.com/site-content/uploads/2019/05/vim-1.png
5. The format should follow the following (be sure to use your disk's UUID from the previous step): <br/>
`UUID=YOURUUID /opt xfs defaults 0 0`
6. We can save the file (:wq!), Then run: <br/>
`[cloud_user@host]$ sudo mount -a`
7. This will mount everything that's listed in fstab, including our new partition.
8. And running a quick df -h /opt should show us roughly 5GB available for the /opt directory.

## Working with the CUPS Print Server:
Understand how to work with print server that will send jobs to PDF files. We will use the lpd (line print daemon) toolset provided by a CUPS installation.

### _Install a PDF Printer_:
1. Open your terminal application.
2. Check to see which printers are installed: <br/>
`lpstat -s`
3. Check to see what types of printer connections are available:  <br/>
`sudo lpinfo -v`
4. Install a PDF printer to use with CUPS:  <br/>
`sudo lpadmin -p CUPS-PDF -v cups-pdf:/`
5. Determine which driver files we can use with our printer by querying the CUPS database for files that contain "PDF":  <br/>
`lpinfo --make-and-model "PDF" -m`
6. Use CUPS-PDF.ppd as the driver file:  <br/>
`sudo lpadmin -p CUPS-PDF -m "CUPS-PDF.ppd"`
7. Run the lpstat command again:  <br/>
`lpstat -s`
8. Check the status of the printer we just installed:  <br/>
`lpc status`
9. Enable the printer to accept jobs, and set it up as the default printer: 
```
sudo lpadmin -d CUPS-PDF -E
sudo cupsenable CUPS-PDF
sudo cupsaccept CUPS-PDF
```
10. Run the lpc status command again:
`lpc status`
> The printer should now be ready.

### _Print a Test Page_:
1. Print a copy of the /etc/passwd file to a PDF file in our home directory: <br/>
`lpr /etc/passwd`
2. Verify that there is a copy of the /etc/passwd file in the home directory: <br/>
`ls`

### _Modify the Printer and Work with the Print Queue_:
1. Configure the printer so that it will not accept any new jobs: <br/>
`sudo cupsreject CUPS-PDF`
2. Verify the status of the printer: <br/>
`lpc status`
3. Attempt to print the /etc/group file to the printer: <br/>
`lpr /etc/group`
4. You should receive a message that says the printer is not currently accepting jobs.
5. Reconfigure the printer to once again accept incoming jobs:
`sudo cupsaccept CUPS-PDF`
6. Check the status of the printer:
`lpc status`
7. Configure the printer so that it accepts jobs to its queue but will not print them:
`sudo cupsdisable CUPS-PDF`
8. Check the status of the printer:
`lpc status`
9. Attempt to print the /etc/group file again:
`lpr /etc/group`
10. List the contents of the /home directory:
`ls`
11. Check the printer's queue: 
`lpq`
12. Remove the job from the printer's queue (remember to substitute the job ID from your command's output):
`lprm <JOB_ID>`
13. Verify that the job was successfully removed from the printer's queue:
`lpq`
14. Re-enable the printer's ability to print new jobs:
`sudo cupsenable CUPS-PDF`
15. Verify that the CUPS-PDF printer is once again ready to accept new jobs:
`lpq`



## References

https://learn.acloud.guru/course/cad92c58-0fd2-4657-98f7-79268b4ff2db/dashboard