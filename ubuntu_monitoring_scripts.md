
# Ubuntu 20.04 Monitoring scripts

#### Update System

Keeping the system updated is vital before starting anything on your system. This will prevent people to use known vulnerabilities to enter in your system.

```bash
apt-get update -y && apt-get full-upgrade -y
```

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
