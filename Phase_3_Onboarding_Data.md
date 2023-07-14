# Onboarding Data
# Letting the rubber hit the road

## Commands needed for Study Club Session

### list permissions on source folder
```
ls -las /studyclubforsplunk
```

## list permissions on source folder and subfolders
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
```
cd /
```
```
sudo setfacl -R -m u:splunk:rx /studyclubforsplunk
```

### check effective permissions on source folder and subfolders
```
getfacl -R /studyclubforsplunk
```

### We need to have the cluster manager push out the configuration to create indexes automatically 
```
cat /opt/splunk/etc/apps/search/local/indexes.conf
```
### We need to copy this stanza format to the ClusterManager instance
#### correct location is /opt/splunk-2/splunk/etc/master-apps/_cluster/local/indexes.conf
```
cp /opt/splunk-2/splunk/etc/master-apps/_cluster/default/indexes.conf /opt/splunk-2/splunk/etc/master-apps/_cluster/local
```
```
vi /opt/splunk-2/splunk/etc/master-apps/_cluster/local/indexes.conf
```
insert and update index name to studyclub_web
```
[studyclub_web]
coldPath = $SPLUNK_DB/studyclub/colddb
enableDataIntegrityControl = 0
enableTsidxReduction = 0
homePath = $SPLUNK_DB/studyclub/db
maxTotalDataSizeMB = 512000
thawedPath = $SPLUNK_DB/studyclub/thaweddb
repFactor = auto
```

repFactor = auto is needed for replication

:wq to save and exit

