
check_logfile - NRPE check for arbitrary logfiles.

Usage:

  check_logfile --name <name> --pattern <pattern> --glob <glob> --max-age <time>

Several log files may be watched at a time, by specifying several sets of those
four switches. Every log file *must* have all four. 

  --name <name>, -n <name>

    The name to use in the NRPE output, probably a single short word.

  --glob <glob>, -g <glob>

    A glob to use to list the possible log files. 

  --max-age [<seconds> | <hours>h | <days>d | <weeks>w]
  --a [<seconds> | <hours>h | <days>d | <weeks>w]

    Maximum age for the log file, in seconds, hours, days or weeks, respectively

  --pattern <perl regex>, -p <perl regex>

    Perl regex to case-sensitively check for.


The newest out of the set of files matching <glob> is inspected. If it is found
to be older than <max-age> then it is presumed that the process that writes the
log has (improperly) not run and so this returns critical.
If it is newer than <max-age>, the file is opened and read to the last line. If
the last line matches <pattern> then this is deemed a success and this check is
OK. If it does not match, it is critical.

For example, to check the daily, weekly and monthly MySQL backups in one go:

log-monitor --name daily --pattern "ERRORSUM: 0000" --glob "/var/log/mysql_backup/*daily" --max-age 1\
            -n weekly  -p "ERRORSUM:\s+0000" -g "/var/log/mysql_backup/*weekly" -a 1w
            -n monthly -p "ERRORSUM:\s+0000" -g "/var/log/mysql_backup/*monthly" -a 1m

Exits:
0:  All log files OK
2:  One or more log files is critical
4:  Program error; invocation or run-time

Set 'DEBUG' environment variable to '1' to get more useful input to STDERR.

