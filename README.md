#check_log

NRPE monitor for logfiles.

This was written for my mysql-xtrabackup and mysql-backup scripts but is general
enough that it might come in useful elsewhere.

It'll parse a logfile's last line and alert depending on the contents of that line,
as well as the age of the log.

The logfile itself is selected by passing a filename-expansion patter (a glob), from
which the youngest-matching file is selected. For a real-life example, here's how I 
keep tabs on my backups:

    log-monitor --name weekly --pattern "Exited OK" --glob "/var/log/mysql-xtrabackup/*weekly" --max-age 1w
                -n monthly -p "Exited OK" -g "/var/log/mysql-xtrabackup/*monthly" -a 1m

That is, one NRPE check keeps tabs on both those files. For the 'weekly' one it expands
the glob `/var/log/mysql-xtrabackup/*weekly` which and picks the most-recent, which will
be the last weekly backup run. If that's more than a week old, it exits critical. If the
file is younger than that, it reads down to the last line and checks this contains the
string "Exited OK", exiting critical if that doesn't exist and OK if it does.


## Exit statuses

0: All log files have been checked as OK
2: One or more log files are not OK
4: Error either in invocation or run-time

Set the `DEBUG` environment variable to `1` to get more useful output to STDERR.
