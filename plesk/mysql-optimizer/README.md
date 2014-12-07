# mysql-optimizer for Plesk servers
`optimize_plesk_db` will automatically defragment all of the tables on the
local Plesk server using the `mysqlcheck` command.

## Disclaimer
- This script has been written and tested only with Plesk 12, but should work
  previous versions of Plesk as well, though it has not been tested with
  them.

- Optimizing tables in MySQL involves creating a copy of each table and then
  copying its contents record-by-record into the new table. As with any such
  operation, you should consider making a back-up copy of your database or
  running the included script on a staging copy of your database to see how
  your server behaves without risking data loss.

- As a result of the fact that MySQL creates a copy of each table to optimize
  it, the included script is not recommended on a large production deployment.
  The optimization process will lock a table while it's being processed, which
  can cause one or more sites to be temporarily unavailable when the tables
  for those sites are being optimized. Additionally, your server will require
  enough space for twice the size of each table during the operation.

## Installation
Drop the `optimize_plesk_db` script into a path in the system-wide path.
The recommended path is `/usr/local/sbin`, since that won't get overwritten
during package upgrades and clearly designates the script as being custom
to the local server.

## Running interactively
You must run `optimize_plesk_db` as root. The script takes no arguments; it
will automatically determine the admin password for MySQL from the Plesk
configuration.

When run interactively, the output you see is the direct output you will see
from `mysqlcheck` directly.

Plesk and many sites use InnoDB tables, so you will see the message
"Table does not support optimize, doing recreate + analyze instead" frequently.
This is normal, and those tables are being optimized despite the warning.

## Running from Cron
For small-scale deployments, the script can be added to a nightly or weekly
cronjob to optimize database performance regularly.

Here's how:

1. Install the script.

2. As `root`, open the root crontab for editing (`EDITOR=nano crontab -e`).

3. Add a line like the following:

        @daily /usr/local/sbin/optimize_plesk_db 2>&1 >> /var/log/plesk_db_optimize.log

4. Save the crontab file and exit the editor (CTRL+O, then CTRL+X).

Now, every night at midnight, the Plesk MySQL database will automatically be
optimized and the results will be logged to
`/var/log/plesk_db_optimize.log`. You may want to set-up log rotation for that
file.
