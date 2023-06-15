# Session 1: How to Install Splunk Products Like an Ace Student

### This lab is designed to take a fresh Linux instance and use Splunk best practices to configure Splunk. We are going to go over connecting to your client via ssh,explain why you should create a Splunk user, installing Splunk, checking permission and starting your Splunk instance.


### Linux Install - Start to Finish
### If a fresh download is required then navigate to temp folder
```
cd /tmp/
```
### We would then use the wget command to download installer to current folder (i.e. /tmp/)
```
wget -O splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/9.0.5/linux/splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz"
```

### For our .conf23 setup, the installer file is already downloaded 
### Navigate to /home/splunk/ folder
```
cd /home/splunk/
```
### Move Splunk installer to /tmp folder (use copy until just before session)
```
cp splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz /tmp
```
### navigate to /tmp foler and upack out the compressed installer files to /opt/
### This will create a subfoler named splunk and place all files there (/opt/splunk/)
```
cd /tmp
```
```
sudo tar -xvzf splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz -C /opt/
```
### confirm files arrived at /opt/splunk

``
ls -las /opt/splunk
``

### {BEST PRACTICE} Splunk should always run under a dedicated accound with appropraite permissions. We already have such an account. It is named splunk
### Confirm that account named splunk already exists
``` 
cat /etc/passwd | grep splunk
```
### If an account named splunk is not present, the following command would be used. 
``` 
sudo adduser splunk
```
### Assign ownership of /opt/splunk and subfolders to the splunk user
```
sudo chown -R splunk:splunk /opt/splunk
```
### Start Splunk for first time
```
sudo /opt/splunk/bin/splunk start --accept-license --answer-yes 
```
### Check Splunk WebUI is accessible
### Discussion Point

```
sudo /opt/splunk/bin/splunk enable web-ssl
```

### Stop Splunk as it is running
```
sudo /opt/splunk/bin/splunk stop
``` 

### disable existing init-d boot-start
```
sudo /opt/splunk/bin/splunk disable boot-start
```
### Configure Splunk to run when rebooted 
```
sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1
```

### Assign ownership of /opt/splunk and subfolders to the splunk user again after boot-start command
```
sudo chown -R splunk:splunk /opt/splunk
```

### Start Splunk running as splunk user
```
sudo -u splunk /opt/splunk/bin/splunk start
```
## confirm te Splunk permissions
```
cd /opt/splunk/bin
```
```
ls -las
```
## confirm Splunk status
```
./splunk status
```
### Set optimized Linux OS limits for Splunk
```
sudo vi /etc/systemd/system/Splunkd.service
```

### Open /etc/systemd/system/Splunkd.service 
### Delete line with LimitNOFILE value and replace with lines the following  
```
LimitCORE=0
LimitDATA=infinity
LimitNICE=0
LimitFSIZE=infinity
LimitSIGPENDING=385952
LimitMEMLOCK=65536
LimitRSS=infinity
LimitMSGQUEUE=819200
LimitRTPRIO=0
LimitSTACK=infinity
LimitCPU=infinity
LimitAS=infinity
LimitLOCKS=infinity
LimitNOFILE=1024000
LimitNPROC=512000
TasksMax=infinity
```
### Create a systemd service for disabling THP
```
sudo vi /etc/systemd/system/disable-thp.service
```
#### Update as follows
```
[Unit]
Description=Disable Transparent Huge Pages (THP)

[Service]
Type=simple
ExecStart=/bin/sh -c "echo 'never' > /sys/kernel/mm/transparent_hugepage/enabled && echo 'never' > /sys/kernel/mm/transparent_hugepage/defrag"

[Install]
WantedBy=multi-user.target
```

### Update read permissions so all users can read and execute this file
```
sudo chmod 755 /etc/systemd/system/disable-thp.service
```

### Reload Linux configurations 
```
sudo systemctl daemon-reload
```

### Start new systemd service for disabling THP
```
sudo systemctl start disable-thp
```
### Enable new systemd service for disabling THP to allow it to run at boot-start
```
sudo systemctl enable disable-thp
```

### NOTE: You “technically” don’t have to reboot Linux. However, to ensure Splunk comes back up properly after an outage, it is a good step to take for a Splunk Enterprise installation. This should be a new instance with nothing else running on it, so it should not be impactful as it has yet to enter production. 

### We are not going to reboot the linux instance for this lab
sudo reboot

## Check Your Install Post Reboot

```
/opt/splunk/bin/splunk status
```

## Verify that Splunk is running
Access web front end


## Are ulimits set and THP Disabled?
``
cat /opt/splunk/var/log/splunk/splunkd.log | grep ulimit
``
Run health check in monitoring console - this requires a licence??

## Is splunk running as the non-root (splunk) user?
```
ps -ef | grep splunk/
```

## Good housekeeping
Delete Splunk installer

### Other thoughts / talking points
Disable WebUI on Indexers
Enable WebUI on SH, DS
Enable SSL on SH
Enable email (SMPT) via SH UI
forward _* internal logs to the indexers
enable cooked Splunk port 9997 inputs on the indexers
Apply Licence
```
/opt/splunk/bin/splunk edit licenser-localpeer -manager_uri 'https://deploymentserver.yourdomain.com:8089'
/opt/splunk/bin/splunk restart
/opt/splunk/bin/splunk list licenser-localpeer
```
Add deployment client
```
vi /opt/splunk/etc/system/local/deploymentclient.conf
```
```
[deployment-client]
[target-broker:deploymentServer]
targetUri = https://deploymentserver.yourdomain.com:8089
```
School of thought: start Splunk as root first, then stop, chown, set boot and run as splunk user
chmod v chown 
