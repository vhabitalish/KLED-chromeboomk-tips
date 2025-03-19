# KLED-chromebook-tips
ACER spin 713 tips for ubuntu

Some tips to get the chromebook working
install UEFI bios from Mrchromebox, follow guide for diabling writeprotect etc
install ubuntu (24.10 is what i had)

1> Sound:
https://github.com/WeirdTreeThing/chromebook-linux-audio
works fine, reboot after following instructions

2> keymapping :
go to settings-> keyboard->shortcuts add shortcuts for volume up down and also screen brightness up and down with ctrl_F6/7/8/9 etc
custom command for scrtteen brightness:
gdbus call --session --dest org.gnome.SettingsDaemon.Power --object-path /org/gnome/SettingsDaemon/Power --method org.gnome.SettingsDaemon.Power.Screen.StepDown 
gdbus call --session --dest org.gnome.SettingsDaemon.Power --object-path /org/gnome/SettingsDaemon/Power --method org.gnome.SettingsDaemon.Power.Screen.StepUp

 map anything else that is needed

 3> suspend and hibernate
 out of box suspend doesn´t work keeps restarting
 s2idle does work if you switch /sys/power/mem_sleep , deep doesn´t seem to work,
 change LID0 to disabled in /proc/acpi/wakeup to get s2idle to work

put kernel parameter in grub defualts:
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash mem_sleep_default=s2idle resume=/dev/disk/by-uuid/UUIDofdisk resume_offset=resumeoffset"
 ( resume and resume_offset are needed only if setting hibernate not needed if not)
sudo update-grub
 
create a systemd service to disable LID0

put a script in /opt/suspend/wakeup.sh
***
#!/bin/bash

##Disable devices
echo 'LID0' | sudo tee /proc/acpi/wakeup
exit 0
EOF
***

create wakeup.service
***
[Unit]
Description=Disable devices
#After=network.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/bin/bash /opt/suspend/wakeup.sh

[Install]
WantedBy=multi-user.target
***

follow guides to setup hibernate
make swap file large enough for ram size
get the resume offset 
sudo filefrag -v /swapfile
add resume and resume_offset in grub default
add resume and resume_offset in /etc/initramfs-tools/conf.d/resume
sudo update-initramfs -u -k all
add
AllowHibernation=yes
HibernateMode=shutdown
in 
/etc/systemd/sleep.conf.d/sleep.conf

sudo systemctl hibernate works to hibernate

close lid or systemctl suspend to suspend
s2idle is fairly good at preserving battery








