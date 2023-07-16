# Scaling Splunk
## Ambition bites the nails of success

### This phase is designed to build out our Splunk infrastructure to be able to handle more data using best practices and our consultant experience to install and configure Splunk. We are going to go over connecting via ssh, explain why you should create a dedicated Splunk user, installing Splunk, checking permissions and starting your Splunk instance.

### [Discussion Point] : Implementation Planning (ITIL)
### [Discussion Point] : What is a Splunk all-in-one environemnt and Scaling trigger points
### [Discussion Point] : Screenshot Guidance


How does Splunk scale?
What is a cluster?
How do you build one?

Start with establishing the Cluster Manager
https://pla1256c-build.splunk.show:445/
StudyClubForSplunk-Manager

This can be achieved via command line
/opt/splunk/bin/splunk edit cluster-config -mode master -replication_factor 2 -search_factor 2 -secret sc4sKey -cluster_label studyclub  
/opt/splunk/bin/splunk restart  

Server configuration is saved in server.conf
cat /opt/splunk-2/splunk/etc/system/local/server.conf

Check what account splunk is running under
```
ps -aux | grep splunk/ | grep  "/opt/splunk/"
```

```
sudo cat /opt/splunk-2/splunk/etc/system/local/server.conf
```
### [Discussion Point] : Missing configuration values

### [Discussion Point] : btool
```
sudo /opt/splunk-2/splunk/bin/splunk btool server list cluster debug | grep factor
```



