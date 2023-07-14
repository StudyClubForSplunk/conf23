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

```
sudo su - splunk 
```
```
cat  /var/log/palo_endpoint.log
```
```
exit
```
```
sudo setfacl -m u:splunk:rw /var/log/palo_endpoint.log
```
```
sudo su - splunk 
```
```
vi  /var/log/palo_endpoint.log
```

Splunk UI
Event Breaks

LINE_BREAKER=([\r\n]+) AUDIENCE PROBING

LINE_BREAKER=([\r\n]+)\w+\s\d+\s\d+\s\d+:\d+:\d+\s\w{4}\s

Timestamp

Extraction = Advanced

TimeZone = GMT

TIME_FORMAT=%b %d %Y %H:%M:%S

TIME_PREFIX=^

MAX_TIMESTAMP_LOOKAHEAD=20

Advanced

TRUNCATE=99999

EVENT_BREAKER = ([\r\n]+)\w+\s\d+\s\d+\s\d+:\d+:\d+\s\w{4}\s

EVENT_BREAKER_ENABLE = true


Command Line in All in One Splunk Server
```
sudo su - splunk 
```
use btool to find props.conf file 
```
/opt/splunk/bin/splunk btool props list --debug | grep pan:endpoint
```
```
cat  /opt/splunk/etc/apps/search/local/props.conf
```

use btool to find inputs.conf file 
```
/opt/splunk/bin/splunk btool inputs list --debug | grep pan:endpoint
```
```
cat  /opt/splunk/etc/apps/search/local/inputs.conf
```
