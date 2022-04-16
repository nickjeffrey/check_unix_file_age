# check_unix_file_age
nagios check for file last modification date on UNIX-like operating systems

# Requirements
perl

# Overview
This is a nagios check for last file modification date for a specified file on UNIX-like operating systems.
Used for checking things like the last time a backup logfile was updated.

This script is executed remotely on a monitored system by the NRPE or check_by_ssh methods available in nagios.

If you are using the check_by_ssh method, you will need a section in the services.cfg file on the nagios server that looks similar to the following. This assumes that you already have ssh key pairs configured.
```
# Define service for checking last modification time of a file
define service{
        use                             generic-24x7-service
        host_name                       unix11
        service_description             file age /path/to/myfile.txt
        check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_unix_file_age --file=/path/to/myfile.txt --warn=24h --crit=48h"
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
