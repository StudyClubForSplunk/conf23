## Bonus Discovery and Troubleshooting Tips 

## Search for your Onboarded Data
index=study_club source="/studyclubforsplunk/www2/access.log"

## Check the tailreader for issues reported
index=_internal TAILREADER file="'/studyclubforsplunk/www2/access.log'"


## Change to Splunk User
sudo su - splunk

## Check for Issues on Splunk's Inputstatus via commandline
```
/opt/splunk/bin/splunk list inputstatus
```
## Check for Issues via REST via commandline
```
/opt/splunk/bin/splunk _internal call /services/admin/inputstatus/TailingProcessor:FileStatus
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
vi  /studyclubforsplunk/www2/access.log
:q! to (quit VI)
```

## Check Status of Fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /studyclubforsplunk/www2/access.log
```
## Restart Splunk
```
/opt/splunk/bin/splunk restart
```

## Search for your Onboarded Data
index=studyclub_web


# Problem 2 sourcetype was set to palo_alto_logs Rather than pan:config
## DISCOVERY

## Check Location of inputs.conf
```
/opt/splunk/bin/splunk btool inputs list --debug
```

## Check Location of props.conf
```
/opt/splunk/bin/splunk btool props list --debug
```

## Check Status of Fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /studyclubforsplunk/www2/access.log
```

## Stop Splunk
```
/opt/splunk/bin/splunk stop
```

## Clear out Fishbucket 
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /studyclubforsplunk/www2/access.log --reset
```
## Check Status of Fishbucket
```
/opt/splunk/bin/splunk cmd btprobe -d /opt/splunk/var/lib/splunk/fishbucket/splunk_private_db/ --file /studyclubforsplunk/www2/access.log
```

## Start Splunk
```
/opt/splunk/bin/splunk start
```

## [Observation Point] - Determine what happens to presiously onboarded events from this log file. Any other steps to consider?

## Example props.conf using great 8
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

## Useful Commands
```
head -c256 /studyclubforsplunk/www2/access.log
```
```
tail -n 3 /opt/splunk/var/log/splunk/splunkd.log
```