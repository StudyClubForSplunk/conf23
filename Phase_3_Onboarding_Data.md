# Onboarding Data
## Letting the rubber hit the road


### This phase is designed to onboard data to our Splunk infrastructure using best practices and our consultant experience.

### [Observation Point] : Are logs accessible in the WebUI of StudyClubForSplunk-IDX01?
### [Observation Point] : Is there anything different about the source folder?

### Observe permissions on source folder
```
ls -las /studyclubforsplunk
```

### Observe permissions on source folder and subfolders
```
ls -Rlas /studyclubforsplunk
```

### list effective permissions on source folder
```
getfacl /studyclubforsplunk
```

### list effective permissions on source folder and subfolders
```
getfacl -R /studyclubforsplunk
```

### modify permissions on source folder and subfolders
### [Discussion Point] : Governance - Change Control (ITIL)
```
cd /
```
### [Observation Point] : Is sudo really required?
getfacl related to modifications of permissions for users or groups  
It does not update permissions to existing users  
Different to chmod (permissions modes)  
Different to chown (changing ownership)  
```
sudo setfacl -R -m u:splunk:rx /studyclubforsplunk
```

### check effective permissions on source folder and subfolders
```
getfacl -R /studyclubforsplunk
```
### [Observation Point] : Are permissions source folder and subfolders okay?
### [Observation Point] : Has data been onboarded?
Check in WebUI of StudyClubForSplunk-IDX01 using SPL  
```
index=web
```
Check in WebUI of StudyClubForSplunk-SH01 using SPL  
```
index=web
```
Check in WebUI of StudyClubForSplunk-IDX02 using SPL  
```
index=web
```

### [Discussion Point] : How indexer cluster works for onboarding


### We need to have the cluster manager push out the configuration to create indexes automatically 
StudyClubForSplunk-IDX01  
```
cat /opt/splunk/etc/apps/search/local/indexes.conf
```
### We need to copy this stanza format to the ClusterManager instance
#### correct location is /opt/splunk-2/splunk/etc/master-apps/_cluster/local/indexes.conf
#### create files if it does not already exist
```
/opt/splunk-2/splunk/etc/master-apps/_cluster/local
```
```
cp /opt/splunk-2/splunk/etc/master-apps/_cluster/default/indexes.conf /opt/splunk-2/splunk/etc/master-apps/_cluster/local
```

```
sudo cp /opt/splunk-2/splunk/etc/master-apps/_cluster/default/indexes.conf /opt/splunk-2/splunk/etc/master-apps/_cluster/local
```
### [Discussion Point] : Default folder v Local folder

```
sudo vi /opt/splunk-2/splunk/etc/master-apps/_cluster/local/indexes.conf
```
insert stanza and modify
```
[studyclub_web]
coldPath = $SPLUNK_DB/studyclub_web/colddb
enableDataIntegrityControl = 0
enableTsidxReduction = 0
homePath = $SPLUNK_DB/studyclub_web/db
maxTotalDataSizeMB = 512000
thawedPath = $SPLUNK_DB/studyclub_web/thaweddb
repFactor = auto
```
### [Discussion Point] : repFactor = auto 

### confirm file contents
```
sudo cat /opt/splunk-2/splunk/etc/manager-apps/_cluster/local/indexes.conf
```
 ### this action effectively creates a bundle

 ### [Discussion Point] : How can budles be pushed out
We use the WebUI on StudyClubForSplunk-Manager for this session  
Command line method on StudyClubForSplunk-Manager  would be  
 ### [Discussion Point] : Implementation Planning 
 use correct locations  
 right action  
 right configuration  
 right instance  
 right permissions  
 right authority  
 right approval
 right testing and assurance  
   
 ### Check and validate the bundle  
 
/opt/splunk-2/splunk/bin/splunk validate cluster-bundle  

### Push out bundle  

/opt/splunk-2/splunk/bin/splunk apply cluster-bundle  

### Check status of the pushed bundle  

/opt/splunk-2/splunk/bin/splunk show cluster-bundle-status  


### Check if index is showing in WebUI on StudyClubForSplunk-Manager
Observe Indexer Clustering : Manager Node page

### [Observation Point] : Is the index web_studyclub showing

### [Observation Point] : Have indexes been pushed out
StudyClubForSplunk-IDX02
```
sudo cat /opt/splunk/etc/peer-apps/_cluster/local/indexes.conf
```
StudyClubForSplunk-IDX01
```
sudo cat /opt/splunk-1/splunk/etc/peer-apps/_cluster/local/indexes.conf
```

### Check if index is showing in WebUI
Check index in WebUI of StudyClubForSplunk-IDX01 using SPL  
```
| eventcount summarize=false index=* | dedup index | fields index
```
Check index in WebUI of StudyClubForSplunk-IDX02 using SPL  
``` 
| eventcount summarize=false index=* | dedup index | fields index 
```

## Onboard another log file on StudyClubForSplunk-IDX01
/studyclubforsplunk/www2/access.log
use existing index web_studyclub during wizard

### [Observation Point] : Has data been onboarded?
Check in WebUI of StudyClubForSplunk-IDX01 using SPL  
```
index=web_studyclub
```
Check in WebUI of StudyClubForSplunk-SH01 using SPL  
```
index=web_studyclub
```
Check in WebUI of StudyClubForSplunk-IDX02 using SPL  
```
index=web_studyclub
```

### [Observation Point] : Are index buckets showing up on StudyClubForSplunk-Manager
Observe Indexer Clustering : Manager Node page


### [Observation Point] : How could this be more effecient and more automated