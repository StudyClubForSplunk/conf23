# Onboarding Data
# Letting the rubber hit the road

## Commands needed for Study Club Session


### Once connected to the jump box, use SSH again to connect to the Splunk All in One server.

### copy from your email message that had subject line "GAS: Your Study Club for Splunk .conf22 Lab environment is ready"

The command would look similar to "ssh username@002-Ja8Am7G5ehFh-study-club-lab-2-jumpbox.bitsioservices.com"
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
