# check_unix_file_age
nagios check for file last modification date on UNIX-like operating systems
 - Alerts if a file last modification date is greater than XXX or less than XXX
 - Useful for checking things list the last time a backup logfile was updated, or if a file that should no* be modified has changed recently, sort of like a poor man's tripwire.


# Requirements
perl, SSH key pair auth

# Overview
This is a nagios check for last file modification date for a specified file on UNIX-like operating systems.
Used for checking things like the last time a backup logfile was updated.

This script is executed remotely on a monitored system by the NRPE or check_by_ssh methods available in nagios.

If you are using the check_by_ssh method, you will need a section in the services.cfg file on the nagios server that looks similar to one of the following examples.
This assumes that you already have ssh key pairs configured.
```
# Define service for checking last modification time of a file(s) is no older than XXX time
# This uses the --older parameter, so file(s) must be no older than the --warn threshold
# An example of when to use the --older parameter is to warn if a backup logfile is not updated due to a missed backup job
define service{
        use                             generic-service
        host_name                       unix11
        service_description             file age myfile.txt
        check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_unix_file_age --file=/path/to/myfile.txt --older --warn=24h --crit=48h"
        }
```

```
# Define service for checking last modification time of a file(s) is no younger than XXX time
# This uses the --younger parameter, so file(s) must be no younger than the --warn threshold
# An example of when to use the --younger parameter is to be alerted if a file that should not be changed has been modified recently
define service{
        use                             generic-service
        host_name                       unix11
        service_description             file age /path/to/*.cfg
        check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_unix_file_age --file=/path/to/*.cfg --younger --warn=48h --crit=24h"
        }
```

If you are using the check_nrpe method, you will need a section in the services.cfg
file on the nagios server that looks similar to the following.
This assumes that you already have ssh key pairs configured.
```
 Define service for checking last modification time of a file 
define service{
        use                             generic-24x7-service
        host_name                       unix11
        service_description             file age myfile.txt
        check_command                   check_nrpe!check_unix_file_age -t 30 --file=/path/to/myfile.txt --warn=24h --crit=48h
        }
```

If using NRPE, you will also need a section defining the NRPE command in the /usr/local/nagios/nrpe.cfg file that looks like this:
```
command[check_file_age]=/usr/local/nagios/libexec/check_file_age
```


# Output
You will see output similar to one of the following messages
<img src=images/file_age.png>
