
# Ubuntu 20.04 Monitoring scripts

#### Update System

Keeping the system updated is vital before starting anything on your system. This will prevent people to use known vulnerabilities to enter in your system.

```bash
apt-get update -y && apt-get full-upgrade -y
```

### AIDE Scripts
Update database - `sudo aideinit`
Run Scan - `aide -c /etc/aide/aide.conf --check`

### Configure AIDE
Cron configuration file - `/etc/crontab`

Cron AIDE daily run at late night (IST) -  `20 19   * * *   root    /etc/aide/aide`

### Install Postfix

```bash
sudo apt update
sudo apt install postfix

```

### Configure Postfix

```bash
sudo nano /etc/aliases
```

### Configure ClamAV

Log file - `/var/log/clamav/clamav-{yyyy-mm-dd}.log`

Cron ClamAV daily run at late night (IST)  - `25 19   * * *   root    /home/ubuntu/scripts/clamscan.sh`
#### ClamAV Script
```
#!/bin/bash
LOGFILE="/var/log/clamav/clamav-$(date +'%Y-%m-%d').log";
EMAIL_MSG="Please see the log file attached";
EMAIL_FROM="clamav@cardse.co";
EMAIL_TO="archit.jain@onion-pay.com,vivek.sharma@onion-pay.com,jayant.varma@onion-pay.com";
DIRTOSCAN="/home/ubuntu/NodeJS-QRCode-with-Bank";


for S in ${DIRTOSCAN}; do
 DIRSIZE=$(du -sh "$S" 2>/dev/null | cut -f1);
 echo "Starting scan of "$S" directory.
 Directory size: "$DIRSIZE".";
 clamscan -lri --remove --detect-pua=yes "$S" >> "$LOGFILE";
 #find /var/log/clamav/ -type f -mtime +30 -exec rm {} \;
 MALWARE=$(tail "$LOGFILE"|grep Infected|cut -d" " -f3);

  if [ "$MALWARE" -ne "0" ];then
     echo "$EMAIL_MSG"|mail -a $LOGFILE  -s "Malware Found" -r "$EMAIL_FROM" "$EMAIL_TO";
  fi

done

exit 0
```

### Install awslogs
Follow this guide - [AWS Document](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html)

### Configure awslogs
Configure only for existing setup - `sudo python ./awslogs-agent-setup.py --region ap-south-1 --only-generate-config`

Configuration file path - `/var/awslogs/etc/awslogs.conf`

Configuration should look like this - 
```
[/var/log/syslog]
datetime_format = %b %d %H:%M:%S
file = /var/log/syslog
buffer_duration = 5000
log_stream_name = {instance_id}
initial_position = start_of_file
log_group_name = /var/log/syslog
[/var/log/dpkg.log]
datetime_format = %b %d %H:%M:%S
file = /var/log/dpkg.log
buffer_duration = 5000
log_stream_name = {instance_id}
initial_position = start_of_file
log_group_name = /var/log/dpkg.log
[/var/log/clamav/clamav.log]
datetime_format = %b %d %H:%M:%S
file = /var/log/clamav/clamav-*.log
buffer_duration = 5000
log_stream_name = {instance_id}
initial_position = start_of_file
log_group_name = /var/log/clamav/clamav.log
[/var/log/aide/aide.log]
datetime_format = %b %d %H:%M:%S
file = /var/log/aide/aide.log
buffer_duration = 5000
log_stream_name = {instance_id}
initial_position = start_of_file
log_group_name = /var/log/aide/aide.log
```

