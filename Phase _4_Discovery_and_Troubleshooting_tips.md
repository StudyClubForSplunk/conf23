# Bonus - How to Discover and Troubleshoot Data Like an Ace Student

## Search for your Onboarded Data
index=study_club source="/var/log/palo_endpoint.log"

## Check the tailreader for issues reported
index=_internal TAILREADER file="'/var/log/palo_endpoint.log'"

## Connect to the Command Line
use the SSH
Instructions in the Study Club Email

## Change to Splunk User
sudo su - splunk

## Check for Issues on Splunk's Inputstatus
/opt/splunk/bin/splunk list inputstatus

## Check for Issues via REST
```
/opt/splunk/bin/splunk _internal call /services/admin/inputstatus/TailingProcessor:FileStatus
 ```
 
## Try to Read the Log File
```
cat /var/log/palo_endpoint.log
 ```
##Exit from Splunk User
```
exit
```
## Allow Access to Splunk User Account
```
sudo setfacl -m u:splunk:rw /var/log/palo_endpoint.log
```
## Change to Splunk User
```
sudo su - splunk
```

## List File Permissions
```
ll /var/log
```

## Open Log File in VI
```
vi  /var/log/palo_endpoint.log
:q! to (quit VI)
```

## Check Status of Fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /var/log/paloconfig.log
```
## Restart Splunk
```
/opt/splunk/bin/splunk restart
```

## Search for your Onboarded Data
index=study_club


# Problem 2 sourcetype was set to palo_alto_logs Rather than pan:config
## DISCOVERY

## Check Location of inputs.conf
```
/opt/splunk/bin/splunk btool inputs list --debug | grep palo
```

## Check Location of props.conf
```
/opt/splunk/bin/splunk btool props list --debug | grep palo
```

## Check Status of Fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /var/log/paloconfig.log
```

## Stop Splunk
```
/opt/splunk/bin/splunk stop
```

## Clear out Fishbucket 
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /var/log/paloconfig.log --reset
```
## Check Status of Fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /var/log/paloconfig.log
```
# Don't Start Splunk Yet!!!

## Display props.conf
```
cat /opt/splunk/etc/apps/studyClub_all_props/default/props.conf
```
## Create a New Sourcetype in Correct Folder
```
cd /opt/splunk/etc/apps/studyClub_all_props
ll
mkdir local
cd default
ll
cp props.conf ../local/props.conf
cd ../local
ll
```

## Open props.conf in the Command Line 
```
vi props.conf
i
```
## Props Settings - Update sourcetype palo_alto_logs to pan:config
```
[pan:config]
SHOULD_LINEMERGE=false
LINE_BREAKER=([\r\n]+)\w+\s\d+\s\d+\s\d+:\d+:\d+\s\w{4}\s
NO_BINARY_CHECK=true
CHARSET=UTF-8
MAX_TIMESTAMP_LOOKAHEAD=39
disabled=false
TIME_FORMAT=%b %d %Y %H:%M:%S
TIME_PREFIX=^
TRUNCATE=99999

[pan:endpoint]
SHOULD_LINEMERGE=false
LINE_BREAKER=([\r\n]+)\w+\s\d+\s\d+\s\d+:\d+:\d+\s\w{4}\s
NO_BINARY_CHECK=true
CHARSET=UTF-8
MAX_TIMESTAMP_LOOKAHEAD=39
disabled=false
TIME_FORMAT=%b %d %Y %H:%M:%S
TIME_PREFIX=^
TRUNCATE=99999
```
## Exit and Save
:x


## Update Inputs.conf - the sourcetype for paloconfig.log from palo_alto_logs to pan:config
```
cd /opt/splunk/etc/apps/studyClub_all_inputs/local
ll
vi inputs.conf

[monitor:///var/log/paloconfig.log]
index = study_club
sourcetype = pan:config
disabled = false

[monitor:///var/log/palo_endpoint.log]
index = study_club
sourcetype = pan:endpoint
disabled = false
```
## Exit and Save
:x

## Start Splunk (ensure you are splunk user)
```
/opt/splunk/bin/splunk start
```
## Check status of fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /var/log/paloconfig.log
```
## Search for log data
```
index=study_club source="/var/log/paloconfig.log"
```
# Note that there are duplicate logs

## Need to Add the 'can_delete' Role to the Splunk UI Admin Account 
```
index=study_club source="/var/log/paloconfig.log" sourcetype=palo_alto_logs | delete
index=study_club source="/var/log/paloconfig.log"
```

## Need to Remove the can_delete Role to the Splunk UI Admin Account 

## Useful Commands
```
head -c256 /var/log/paloconfig.log
tail -n 3 /opt/splunk/var/log/splunk/splunkd.log
```

## See What User Splunk is Running Under
```
ps -ef | grep splunk
kill -9 <PID> 
```
