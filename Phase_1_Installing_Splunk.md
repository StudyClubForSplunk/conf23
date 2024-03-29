# Installing Splunk
## Power is everything with Control

### This phase is designed to take a fresh Linux instance and use best practices and our consultant experience to install and configure Splunk. We are going to go over connecting via ssh, explain why you should create a dedicated Splunk user, installing Splunk, checking permissions and starting your Splunk instance.

### [Discussion Point] : Implementation Planning (ITIL)

### [Discussion Point] : connecting via ssh
We connect to our individual Splunkshow instance command line via SSH  
Use the Splunkshow URL on the enrollmennt page  
#### `https://pla1256c-**************.splunk.show:7469`
###
### SSH Usernname
```
splunk
```
### SSH Password
```
5p1unk.conf
```

### If a fresh download was required we would navigate to temp folder and download from there
### [Discussion Point] : Good Housekeeping
cd /tmp/
### [Discussion Point] : wget command and specifying parameters
Reference https://www.splunk.com/en_us/download/splunk-enterprise.html#linux  

Sample wget download command  
wget -O splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/9.0.5/linux/splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz"

### For our .conf23 setup, the installer file is already downloaded 
Navigate to the home folder for splunk user (/home/splunk/)  
### [Discussion Point] : Real World v Lab Environment
```
cd /home/splunk/
```
### Confirm Splunk installer file is present
```
ls
```

### Move Splunk installer to /tmp folder
### [Discussion Point] : Housekeeping
```
mv splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz /tmp
```
### navigate to /tmp foler 

```
cd /tmp
```
Unpack the compressed installer files to /opt/  
This will create a subfoler named splunk and place all files there (/opt/splunk/)  
```
sudo tar -xvzf splunk-9.0.5-e9494146ae5c-Linux-x86_64.tgz -C /opt/
```
### [Discussion Point] : sudo
### [Discussion Point] : Governance (ITIL)
### [Discussion Point] : Implementation Planning, CAB (ITIL)
### [Discussion Point] : Splunk running under a user account
### Confirm that account named splunk already exists

cat /etc/passwd | grep splunk  

### [Observation Point] : Does the user splunk exist?

### If an account named splunk is not present, the following command would be used. 

sudo adduser splunk  

### confirm files arrived at /opt/splunk

```
ls -las /opt/splunk
```


### Assign ownership of /opt/splunk and subfolders to the splunk user
### [Discussion Point] : chown command changes ownership
Reference https://linuxize.com/post/linux-chown-command/  
```
sudo chown -R splunk:splunk /opt/splunk
```
### confirm permissions to double confirm
```
ls -las /opt/splunk
```

### [Observation Point] : Are the files under ownsership of the the splunk user?
### Start Splunk for first time


### [Discussion Point] : The how and why of starting Splunk for the first time
What account would we run Splunk as by using this command?  
Best practive v Organisational standards  
```
/opt/splunk/bin/splunk start --accept-license --answer-yes 
```

### Splunk Administrator username: 
```
admin
``` 
### new password: 
```
changeme
```

### [Discussion Point] : Post installation testing
Is Splunk WebUI accessible?  
#### `https://pla1256c-**************.splunk.show:8000`


### [Discussion Point] : Post installation configuration
Role of scripting  

Update Default hostname and Splunk server name to
```
StudyClubForSplunk-IDX01
```
#### Enable global banner 
Set Banner Visibility to On  
Set Background Color to Blue  
Set Message to  
```
StudyClubForSplunk-IDX01
```
#### Update hostname to 
```
StudyClubForSplunk-IDX01
```
#### Update servername to
``` 
StudyClubForSplunk-IDX01
```
### [Discussion Point] : Securing accessing WebUI
#### examine web.conf
```
cat /opt/splunk/etc/system/local/web.conf
```

### could also be done in command line
```
/opt/splunk/bin/splunk enable web-ssl
```


### Stop Splunk as it is running
```
/opt/splunk/bin/splunk stop
``` 

### disable existing init-d boot-start
### [Discussion Point] : Why do this when we have not enabled before
```
sudo /opt/splunk/bin/splunk disable boot-start
```
### Configure Splunk to run when rebooted 
### [Discussion Point] : What is init-d and systemd?
Reference https://docs.splunk.com/Documentation/Splunk/9.1.0/Admin/RunSplunkassystemdservice  
systemd supports parallel processing  
```
sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1 
```


#### Systemd manages the Splunk service. Use 'systemctl start Splunkd' to start the service. Root permission is required.
```
sudo systemctl start Splunkd
```

### [Discussion Point] : Default starting mode is init.d
sudo -u splunk /opt/splunk/bin/splunk start

### confirm the Splunk permissions
```
cd /opt/splunk/bin
```
```
ls -las
```
### confirm Splunk status
```
./splunk status
```

### [Discussion Point] : Optional to do during session 
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
[Disucssion Point] - vi operations
Save file by pressing ESC key, then :w
Close file by pressing ESC key, then :q

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
### [Discussion Point] : chown changes ownership : chmod changes permissions : authorized actions/remit
```
sudo chmod 755 /etc/systemd/system/disable-thp.service
```
755 open to the world  
Reference https://linuxize.com/post/chmod-command-in-linux/  

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

### [Discussion Point] :  Rebooting the Linux Instance
You “technically” don’t have to reboot Linux. However, to ensure Splunk comes back up properly after an outage, it is a good step to take for a Splunk Enterprise installation. This should be a new instance with nothing else running on it, so it should not be impactful as it has yet to enter production. 

### We are NOT going to reboot the linux instance for this lab

### [Discussion Point] : Post Implementation Testing
Check Your Install Post Reboot  
```
/opt/splunk/bin/splunk status
```

## Verify that Splunk is running
Access web front end
#### `https://pla1256c-**************.splunk.show:8000`

## Are Transparent Huge Pages (THP) Disabled?
### Method 1 Run health check in monitoring console 

### Method 2 Run Splunk search in Web UI
```
| rest /services/server/sysinfo | fields splunk_server transparent_hugepages.defrag transparent_hugepages.effective_state transparent_hugepages.enabled
```
## Method 3 Run Splunk search in CLI
```
/opt/splunk/bin/splunk search "| rest /services/server/sysinfo | fields splunk_server transparent_hugepages.defrag transparent_hugepages.effective_state transparent_hugepages.enabled"
```

### Check Linux OS for ulimits settings
```
cat /opt/splunk/var/log/splunk/splunkd.log | grep  "Limit: open files: "
```

## Is splunk running as the non-root (splunk) user?
```
ps -ef | grep splunk/ | grep  "/opt/splunk/bin/"
```
### [Discussion Point] : Closing out Release Implementation plan (ITIL)

## [Observation Point] : Attendee Progress