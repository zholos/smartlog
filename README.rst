smartlog
========

This program periodically runs ``smartctl`` to query information from all
available drives and saves the output in a compressed format.

Simply run ``smartlog`` at system startup to start collecting data. By default
the data is saved to ``/var/db/smartlog.sqlite`` in one-minute intervals. This
can be configured with command-line options.

The raw outputs can be extracted as a tar stream. Note that they are batched up
for compression so recent outputs can't be accessed immediately.

::

  # smartlog -x | tar xf -

Some SMART attributes are parsed and saved in numerical format. These can be
accessed with SQLite.

::

  # sqlite3 /var/db/smartlog.sqlite
  sqlite> .mode column
  sqlite> .headers on
  sqlite> select * from drives join attrs on drives.id=drive_id;
  sqlite> select model, serial, datetime(time,'unixepoch','localtime') as time, power_on, power_cycle, temperature, total_written, total_read from drives join attrs on drives.id=drive_id;
  sqlite> select model, serial, max(power_on), max(power_cycle), avg(temperature), max(total_written), max(total_read) from drives join attrs on drives.id=drive_id group by model, serial;
