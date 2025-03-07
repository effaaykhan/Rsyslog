# Rsyslog
Rsyslog (Rocket-fast System for Log Processing) is an advanced and high-performance log processing system for Linux and Unix-based systems. It is an enhanced version of the traditional syslog service, offering more features, better security, and improved performance.

## rsyslog.conf
```bash
# /etc/rsyslog.conf - Optimized Configuration File

#################
#### MODULES ####
#################

# Support for local system logging
module(load="imuxsock")

# Kernel logging support and enable non-kernel klog messages
module(load="imklog" permitnonkernelfacility="on")

# Provides UDP syslog reception
#module(load="imudp")
#input(type="imudp" port="514")

# Provides TCP syslog reception (Uncomment if needed)
# module(load="imtcp")
# input(type="imtcp" port="1514")

# Load the JSON processing module
module(load="mmjsonparse")
#module(load="mmjsonparse")
#$MaxJsonFields 100
#$MaxJsonFieldSize 65536

# Load structured data processing module
module(load="mmpstrucdata")

###########################
#### GLOBAL DIRECTIVES ####
###########################

# Use traditional timestamp format
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat

# Filter duplicated messages
$RepeatedMsgReduction on

# Set the default permissions for all log files
$FileOwner syslog
$FileGroup adm
$FileCreateMode 0640
$DirCreateMode 0755
$Umask 0022
$PrivDropToUser syslog
$PrivDropToGroup syslog

# Where to place spool and state files
$WorkDirectory /var/spool/rsyslog

# Include additional configuration files
$IncludeConfig /etc/rsyslog.d/*.conf

###########################
#### REMOTE LOGGING ####
###########################

# Allow log reception from 192.X.X.X/24 via UDP
$AllowedSender UDP, 192.X.X.X/24

# Template for remote logs
template(name="remote-incoming-logs" type="string" string="/var/log/%HOSTNAME%.log")

# Store all incoming logs in the specified format
*.* ?remote-incoming-logs

###########################
#### JSON PROCESSING ####
###########################

# Forward FortiGate logs to Wazuh
if $fromhost-ip == '<fortigate-IP>' then @127.0.0.1:1514
& stop

```
