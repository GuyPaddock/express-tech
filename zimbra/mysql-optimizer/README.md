# mysql-optimizer
`optimize_zimbra_db` will automatically defragment all of the tables in a
Zimbra install using the `mysqlcheck` command.

## Disclaimer
- This script has been written and tested only for Zimbra 6. It may work for
  Zimbra 7 but has not been tested yet with it.

- Optimizing tables in MySQL involves creating a copy of each table and then
  copying its contents record-by-record into the new table. As with any such
  operation, you should consider making a back-up copy of your database or
  running the included script on a staging copy of your database to see how
  your server behaves without risking data loss.

- As a result of the fact that MySQL creates a copy of each table to optimize
  it, the included script is not recommended on a large production deployment.
  The optimization process will lock a table while it's being processed, which
  can cause the email system to be temporarily unavailable for users whose
  mailbox is included in the table that is being optimized. Additionally,
  your server will require enough space for twice the size of each table
  during the operation.

## Installation
Drop the `optimize_zimbra_db` script into a path in the system-wide path.
The recommended path is `/usr/local/sbin`, since that won't get overwritten
during Zimbra or package upgrades.

## Running interactively
You can run `optimize_zimbra_db` either as root or the zimbra user. The script
takes no arguments; it will automatically determine the root password for MySQL
from the Zimbra server.

When run interactively, the output you see is the direct output you will see
from `mysqlcheck` directly.

Since Zimbra uses InnoDB tables, you will see the message
"Table does not support optimize, doing recreate + analyze instead" frequently.
This is normal, and those tables are being optimized despite the warning.

## Running from Cron
For small-scale deployments, the script can be added to a nightly or weekly
cronjob to optimize mailbox performance regularly.

Here's how:

1. After installing the script, switch to the zimbra user
(`sudo -i -u zimbra`).

2. Open the zimbra crontab for editing (`EDITOR=nano crontab -e`).

3. Add a line like the following either **before the ZIMBRASTART** line or
   **after the ZIMBRAEND** line, but not anywhere in between (the section in
   the middle of the crontab is managed by Zimbra and is at risk for being
   overwritten):

        @daily /usr/local/sbin/optimize_zimbra_db 2>&1 >> /opt/zimbra/log/zimbra_db_optimize.log

Now, every night at midnight, the Zimbra MySQL database will automatically be
optimized and the results will be logged to
`/opt/zimbra/log/zimbra_db_optimize.log`. You may want to set-up log rotation
for that file.
