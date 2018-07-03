# Proxmox stuff
This is a collection of stuff that I wrote for Proxmox.

[TOC]

## prox_config_backup

** B E T A ! **
I just wrote this script quick and dirty. Not tested yet! But it might still be of help.

### Backup
* Download the [script](https://raw.githubusercontent.com/DerDanilo/proxmox-stuff/master/prox_config_backup.sh)  
```cd /root/; wget -qO- https://raw.githubusercontent.com/DerDanilo/proxmox-stuff/master/prox_config_backup.sh```
* Set the permanent backups directory ```_bdir="/path/to/backup/directory"```
* Make the script executable ```chmod +x ./prox_config_backup.sh```
* Shut down ALL VMs + LXC Containers if you want to go the save way. (Not required)
* Run the script ```./prox_config_backup.sh```

### Restore
On my machine, you end up with a GZipped file of about 1-5 MB with a name like "proxmox_backup_proxmoxhostname_2017-12-02.15.48.10.tar.gz".  
Depending upon how you schedule it and the size of your server, that could eventually become a space issue so don't  
forget to set up some kind of archive maintenance.

To restore, move the file back to proxmox with cp, scp, webmin, a thumb drive, whatever.  
I place it back into the /var/tmp directory from where it came. 

```
# Unpack the original backup
tar -zxvf proxmox_backup_proxmoxhostname_2017-12-02.15.48.10.tar.gz
# unpack the tared contents
tar -xvf proxmoxpve.2017-12-02.15.48.10.tar
tar -xvf proxmoxetc.2017-12-02.15.48.10.tar
tar -xvf proxmoxroot.2017-12-02.15.48.10.tar

# If the services are running, stop them:
for i in pve-cluster pvedaemon vz qemu-server; do systemctl stop $i ; done

# Copy the old content to the original directory:
cp -avr /var/tmp/etc /etc
cp -avr /var/tmp/var /var
cp -avr /var/tmp/root /root

# And, finally, restart services:
for i in qemu-server vz pvedaemon pve-cluster; do systemctl start $i ; done
```

If nothing goes wrong, and you have separately restored the VM images using the default ProxMox process.  
You should be back where you started. But let's hope it never comes to that.

### Sources
http://ziemecki.net/content/proxmox-config-backups
