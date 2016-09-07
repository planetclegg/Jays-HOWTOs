```text

# Problem: booting chromebook from fresh linux SD card image, only about 8gb of 32gb is taken advantage of, need resize the root partition:

# NOTE: check the /dev/ paths, may be different for you.  
# WARNING: be prepared to lose all data if this doesn't work

#I did this from a live running machine, resizing the disk I was running off of.

fdisk -l    # find the right device.. in this case /dev/mmcblk1, which is reporting size mismatch

parted /dev/mmcblk1
(parted) print
# Warning: Not all of the space available to /dev/mmcblk1 appears to be used, you can fix the GPT to use all of the space
# (an extra ...
# Fix/Ignore? 
Fix
# (press Ctrl-D to exit)

cgpt add -i 2  -t data -b 40960 -s 60000000 -l Root /dev/mmcblk1   # try to use almost all the SD card.. 60000000 not exact, I'm lazy
cgpt repair /dev/mmcblk1
cgpt add -i 1 -S 1 -T 5 -P 10 -l KERN-A /dev/mmcblk1
cgpt add -i 2 -S 1 -T 5 -P 5 -l KERN-B  /dev/mmcblk1
resize2fs /dev/mmcblk1p2
reboot

#Note: resize2fs above didn't work until rebooting, had to run it again and reboot again.



```
