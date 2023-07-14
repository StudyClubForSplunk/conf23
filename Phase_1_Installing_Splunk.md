# Installing Splunk
# Power is everything with Control

### This lab is designed to take a fresh Linux instance and use best practices to install and configure Splunk. We are going to go over connecting via ssh, explain why you should create a dedicated Splunk user, installing Splunk, checking permissions and starting your Splunk instance.


### Linux Install - Start to Finish
### If a fresh download is required then navigate to temp folder
cd /tmp/
### We would then use the wget command to download installer to current folder (i.e. /tmp/)
wget -O splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/9.0.5/linux/splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz"

### For our .conf23 setup, the installer file is already downloaded 
### Navigate to /home/splunk/ folder
```
cd /home/splunk/
```
### Confirm Splunk installer file is present
```
ls
```

### Move Splunk installer to /tmp folder
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

```
ls -las /opt/splunk
```

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
### confirm permissions to double confirm
```
ls -las /opt/splunk
```
### Start Splunk for first time
administrator username: **admin** 
new password: **changeme**
```
/opt/splunk/bin/splunk start --accept-license --answer-yes 
```

### Check Splunk WebUI is accessible

Update Default hostname and Splunk server name to
```
StudyClubForSplunk-IDX01
```
#### add global banner in blue
#### udpate hostname to StudyClubForSplunk-IDX01
#### update servername to StudyClubForSplunk-IDX01
#### show config file as 0
### Enable ssl in UI
#### show config file as 1

### could be done in command line
```
sudo /opt/splunk/bin/splunk enable web-ssl
```
### run health check showing failures

### Stop Splunk as it is running
```
/opt/splunk/bin/splunk stop
``` 

### disable existing init-d boot-start
#### only needed in this test environment
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
#### Systemd manages the Splunk service. Use 'systemctl start Splunkd' to start the service. Root permission is required. Login as root user or use sudo.
```
sudo systemctl start Splunkd
```

### Start Splunk running as splunk user for init.d only
sudo -u splunk /opt/splunk/bin/splunk start

## confirm the Splunk permissions
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
## Optional during session
### Set optimized Linux OS limits for Splunk
```
sudo vi /etc/systemd/system/Splunkd.service
```

#### Delete line with LimitNOFILE value and replace with lines the following  
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
### copy and paste loses carriage returns
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
### check status new systemd service for disabling THP
```
sudo systemctl status disable-thp
```
### Enable new systemd service for disabling THP to allow it to run at boot-start
```
sudo systemctl enable disable-thp
```

### NOTE: You “technically” don’t have to reboot Linux. However, to ensure Splunk comes back up properly after an outage, it is a good step to take for a Splunk Enterprise installation. This should be a new instance with nothing else running on it, so it should not be impactful as it has yet to enter production. 

### We are not going to reboot the linux instance for this lab

## Check Your Install Post Reboot

```
/opt/splunk/bin/splunk status
```

## Verify that Splunk is running
Access web front end

## Are THP Disabled?
### Run health check in monitoring console 

### Run Splunk search in Web UI
```
| rest /services/server/sysinfo | fields splunk_server transparent_hugepages.defrag transparent_hugepages.effective_state transparent_hugepages.enabled
```

## Are ulimits set and THP Disabled?
## Run Splunk search in CLI
```
/opt/splunk/bin/splunk search "| rest /services/server/sysinfo | fields splunk_server transparent_hugepages.defrag transparent_hugepages.effective_state transparent_hugepages.enabled"
```

### Check Linux OS
```
cat /opt/splunk/var/log/splunk/splunkd.log | grep  "Limit: open files: "
```



## Is splunk running as the non-root (splunk) user?
```
ps -ef | grep splunk/ | grep  "/opt/splunk/bin/"
```

## fix ssl errors

  sudo cp -r /opt/splunk-1/splunk/etc/auth/splunkweb/ /opt/splunk/etc/auth/                                                                                                               
   cd ../                                                                                                                                                                                  
   cd ../auth                                                                                                                                                                              
   ls                                                                                                                                                                                      
   cd splunkweb/                                                                                                                                                                           
   ls                                                                                                                                                                                      
   cd /opt/splunk/etc/system/local/                                                                                                                                                        
   vi web.conf                                                                                                                                                                             
   sudo systemctl stop Splunkd                                                                                                                                                             
   vi web.conf                                                                                                                                                                             
   sudo systemctl start Splunkd
```
