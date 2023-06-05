# Session 1: How to Install Splunk Products Like an Ace Student

## This lab is designed to take a fresh Linux instance and use Splunk best practices to configure Splunk. We are going to go over connecting to your client via ssh, why you should create a Splunk user, installing Splunk, checking permission and starting your Splunk instance.


### Instructions
wget -O splunk-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/8.2.6/linux/splunk-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz"

wget -O splunk-8.2.6-a6fe1ee8894b-x64-release.msi "https://download.splunk.com/products/splunk/releases/8.2.6/windows/splunk-8.2.6-a6fe1ee8894b-x64-release.msi"

wget -O splunkforwarder-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz "https://download.splunk.com/products/universalforwarder/releases/8.2.6/linux/splunkforwarder-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz"

wget -O splunkforwarder-8.2.6-a6fe1ee8894b-x64-release.msi "https://download.splunk.com/products/universalforwarder/releases/8.2.6/windows/splunkforwarder-8.2.6-a6fe1ee8894b-x64-release.msi"

wget -O splunkforwarder-8.2.6-a6fe1ee8894b-linux-2.6-x86_64.rpm "https://download.splunk.com/products/universalforwarder/releases/8.2.6/linux/splunkforwarder-8.2.6-a6fe1ee8894b-linux-2.6-x86_64.rpm"

Install - Start to Finish
```
cd /tmp/
```
```
wget -O splunk-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/8.2.6/linux/splunk-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz"
```
### Alternatively, scp the installation file to the server depending on the install scenario


``` 
sudo tar -xvzf splunk-8.2.6-a6fe1ee8894b-Linux-x86_64.tgz -C /opt/
```

``` 
sudo adduser splunk
```

``` 
cat /etc/passwd | grep splunk
```

```
sudo /opt/splunk/bin/splunk start --accept-license --answer-yes 
```

```
sudo /opt/splunk/bin/splunk stop
```

```
sudo /opt/splunk/bin/splunk enable boot-start -user splunk -systemd-managed 1
```

```
sudo chown -R splunk:splunk /opt/splunk
```

```
sudo -u splunk /opt/splunk/bin/splunk start
```

```
sudo vi /etc/systemd/system/Splunkd.service
```

### Delete original LimitNOFILE and replace with below###
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
```
sudo vi /etc/systemd/system/disable-thp.service
```
```
[Unit]
Description=Disable Transparent Huge Pages (THP)

[Service]
Type=simple
ExecStart=/bin/sh -c "echo 'never' > /sys/kernel/mm/transparent_hugepage/enabled && echo 'never' > /sys/kernel/mm/transparent_hugepage/defrag"

[Install]
WantedBy=multi-user.target
```
```
sudo chmod 755 /etc/systemd/system/disable-thp.service
```

```
sudo systemctl daemon-reload
```

```
sudo systemctl start disable-thp
```

```
sudo systemctl enable disable-thp
```

```
sudo reboot
```

### NOTE: You “technically” don’t have to reboot Linux. However, to ensure Splunk comes back up properly after an outage, it is a good step to take for a Splunk Enterprise installation. This should be a new instance with nothing else running on it, so it should not be impactful as it has yet to enter production. 

## Check Your Install Post Reboot
After reboot checklist

## Verify that Splunk is running
Access web front end

## Is THP Disabled?
Run health check in monitoring console

## Are ulimits set?
Run health check in monitoring console

## Is splunk running as the non-root (splunk) user?
ps -ef | grep splunk

