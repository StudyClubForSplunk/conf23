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

### If the installer file is available from an accessible remote server location, the scp command could be used to retrieve it
### For our .conf23 setup, the installer file is already downloaded 

### Navigate to /home/splunk/ folder
```
cd /home/splunk/
```
### Move Splunk installer to /tmp folder
### change to mv before session
```
cp splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz /tmp
```
### navigate to /tmp foler and expand out the compressed installer file to /opt/
### This will create a subfoler named splunk and place all files there (/opt/splunk/)
```
cd /tmp
```
```
sudo tar -xvzf splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz -C /opt/
```
### validate /opt/splunk

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

### Run Splunk for the first time as the root user
```
sudo /opt/splunk/bin/splunk start --accept-license --answer-yes 
```

### Stop Splunk as it is running
```
sudo /opt/splunk/bin/splunk stop
```

### Assign ownership of /opt/splunk and subfolders to the splunk user
```
sudo chown -R splunk:splunk /opt/splunk
```

### Configure Splunk to run when rebooted 
```
sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1
```

### Start Splunk running as splunk user
```
sudo -u splunk /opt/splunk/bin/splunk start
```
## Validate Splunk permissions
```
cd /opt/splunk/bin
```
```
ls -las
```
## Validate Splunk status
```
./splunk status
```
### Optimize the Splunk service configurations
```
sudo vi /etc/systemd/system/Splunkd.service
```

### Open /etc/systemd/system/Splunkd.service or create if necessary
### Delete original LimitNOFILE value and replace with 1024000
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
### Open /etc/systemd/system/disable-thp.service
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

### Update permissions
```
sudo chmod 755 /etc/systemd/system/disable-thp.service
```

### Reload Splunk 
```
sudo systemctl daemon-reload
```

### Start
```
sudo systemctl start disable-thp
```

```
sudo systemctl enable disable-thp
```

### NOTE: You “technically” don’t have to reboot Linux. However, to ensure Splunk comes back up properly after an outage, it is a good step to take for a Splunk Enterprise installation. This should be a new instance with nothing else running on it, so it should not be impactful as it has yet to enter production. 
### Reboot linux instance 
```
sudo reboot
```

## Check Your Install Post Reboot
After reboot checklist

## Verify that Splunk is running
Access web front end

## Is THP Disabled?
Run health check in monitoring console

## Are ulimits set?
Run health check in monitoring console

## Is splunk running as the non-root (splunk) user?
```
ps -ef | grep splunk
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
