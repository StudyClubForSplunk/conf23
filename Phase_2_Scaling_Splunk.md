# Scaling Splunk
## Ambition bites the nails of success

### This phase is designed to build out our Splunk infrastructure to be able to handle more data using best practices and our consultant experience.

### [Discussion Point] : Screenshot Guidance
### [Discussion Point] : Implementation Planning (ITIL)
### [Discussion Point] : Splunk Instance Types
What is an all-in-one  
What is an indexer cluster  
Scaling trigger points  
Members of an indexer cluster

### [Discussion Point] : Building out an Indexer Cluster
Order of implementation  

### StudyClubForSplunk-Manager
### We will use the Web UI to create the cluster

## Please click the links in the Splunkshow enrolment page ending in :444, :445 and :446

This could also be achieved via command line  
/opt/splunk/bin/splunk edit cluster-config -mode master -replication_factor 2 -search_factor 2 -secret sc4sKey -cluster_label studyclub  
/opt/splunk/bin/splunk restart  

Server configuration is saved in server.conf
```
cat /opt/splunk-2/splunk/etc/system/local/server.conf
```
### [Observation Point] : Was the splunk user able to view server.conf

Check what account splunk is running under
```
ps -aux | grep splunk/ | grep  "/opt/splunk/"
```

```
sudo cat /opt/splunk-2/splunk/etc/system/local/server.conf
```
### [Discussion Point] : Are there any missing configuration values


### [Discussion Point] : btool is your friend
What configuration is written to disk?
```
sudo /opt/splunk-2/splunk/bin/splunk btool server list cluster debug | grep factor
```

### We need to be able to assign ports for clustering
### Check what ports are in use by Splunk

```
sudo lsof -i -P -n
```
### Observe ports used by Splunk
```
sudo lsof -i -P -n | grep LISTEN | grep splunkd
```
### Be sure to select a port that is not in use - * Implementation Planning * 
## [Discussion Point] : Change Control (ITIL)

## [Observation Point] : View Clustering info on Cluster Manager in UI



### Peer node configuration for StudyClubForSplunk-IDX02 in UI
Manager URI  
```
https://localhost:8092
```
Peer replication port  
```
8081
```
Security key  
```
sc4sKey
```

### After restarting StudyClubForSplunk-IDX02
Implementation Testing Step  
show that Splunk is now listening on port 8081  
```
sudo lsof -i -P -n | grep LISTEN | grep splunkd
```


### Peer node configuration for StudyClubForSplunk-IDX01 in UI
Manager URI  
```
https://localhost:8092
```
Peer replication port  
```
8080
```
Security key  
```
sc4sKey
```

### After restarting StudyClubForSplunk-IDX01
Implementation Testing Step  
show that Splunk is now listening on port 8080    
```
sudo lsof -i -P -n | grep LISTEN | grep splunkd
```

### Peer node configuration for StudyClubForSplunk-SH01 in UI
Manager URI  
```
https://localhost:8092
```
Security key  
```
sc4sKey
```

### After restarting StudyClubForSplunk-SH01
Implementation Testing Step  
show that Splunk is now listening on port 8080    
```
sudo lsof -i -P -n | grep LISTEN | grep splunkd
```

## [Discussion Point] : Monitor Console Mode

## [Observation Point] : Is StudyClubForSplunk-SH01 showing in monitor console on StudyClubForSplunk-Manager

Add search peers
Peer URI  
```
https://localhost:8093
```

Distributed search authentication
Remote username  
```
admin
```
Remote password  
```
changeme
```
Confirm password
```
changeme
```

## [Discussion Point] : Monitor Console - Instance Roles

### Implementation Testing Steps    
Confirm visibility in Monitoring Console Overview on StudyClubForSplunk-Manager  
Confirm Clustering Setup on StudyClubForSplunk-SH01  

## [Observation Point] : Attendee Progress