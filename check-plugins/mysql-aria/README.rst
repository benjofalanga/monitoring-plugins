Check mysql-aria
================

Overview
--------

Checks some metrics of the crash-safe, non-transactional Aria Storage Engine in MariaDB. Aria is used for internal temporary tables in MariaDB and not shipped with MySQL or Percona Server. The logic is taken from `MySQLTuner script <https://github.com/major/MySQLTuner-perl>`_:mariadb_aria(), v1.9.8.

Hints:

* See `additional notes for all mysql monitoring plugins <https://github.com/Linuxfabrik/monitoring-plugins/blob/main/PLUGINS-MYSQL.rst>`_
* Requires a user account with high privileges to access schemas like INFORMATION_SCHEMA. `For most INFORMATION_SCHEMA tables, each MySQL user has the right to access them, but can see only the rows in the tables that correspond to objects for which the user has the proper access privileges. <https://dev.mysql.com/doc/refman/5.7/en/information-schema-introduction.html#information-schema-privileges>`_. `So you can't grant permission to INFORMATION_SCHEMA directly, you have to grant permission to the tables on your own schemas, and as you do, those tables will start showing up in INFORMATION_SCHEMA queries <https://stackoverflow.com/questions/60499772/cannot-grant-mysql-user-access-to-information-schema-database>`_. Then this check provide correct results.


Fact Sheet
----------

.. csv-table::
    :widths: 30, 70
    
    "Check Plugin Download",                "https://github.com/Linuxfabrik/monitoring-plugins/tree/main/check-plugins/mysql-aria"
    "Check Interval Recommendation",        "Once an hour"
    "Can be called without parameters",     "No"
    "Compiled for",                         "Linux, Windows"
    "Requirements",                         "User with no privileges, locked down to ``127.0.0.1`` - for example ``monitoring\@127.0.0.1``. Usernames in MySQL/MariaDB are limited to 16 chars in specific versions."
    "3rd Party Python modules",             "``pymysql``"


Help
----

.. code-block:: text

    usage: mysql-aria3 [-h] [-V] [--always-ok] [--defaults-file DEFAULTS_FILE]
                       [--defaults-group DEFAULTS_GROUP] [--timeout TIMEOUT]

    Checks some metrics of the Aria Storage Engine in MySQL/MariaDB.

    options:
      -h, --help            show this help message and exit
      -V, --version         show program's version number and exit
      --always-ok           Always returns OK.
      --defaults-file DEFAULTS_FILE
                            Specifies a cnf file to read parameters like user,
                            host and password from (instead of specifying them on
                            the command line), for example
                            `/var/spool/icinga2/.my.cnf`. Default:
                            /var/spool/icinga2/.my.cnf
      --defaults-group DEFAULTS_GROUP
                            Group/section to read from in the cnf file. Default:
                            client
      --timeout TIMEOUT     Network timeout in seconds. Default: 3 (seconds)


Usage Examples
--------------

.. code-block:: bash

    ./mysql-aria --defaults-file=/var/spool/icinga2/.my.cnf

Output:

.. code-block:: text

    Aria pagecache size / total Aria indexes: 128.0MiB/328.0KiB, 97.2% Aria pagecache hit rate (1.1K cached / 30.0 reads)


States
------

* WARN if ``aria_pagecache_buffer_size`` < ``total_aria_indexes`` and ``pct_aria_keys_from_mem`` < 95%.
* WARN if ``aria_pagecache_read_requests`` > 0 and ``pct_aria_keys_from_mem`` < 95%.


Perfdata / Metrics
------------------

.. csv-table::
    :widths: 25, 15, 60
    :header-rows: 1
    
    Name,                                       Type,               Description                                           
    mysql_aria_pagecache_buffer_size,           Bytes,              The size of the buffer used for index and data blocks for Aria tables.
    mysql_total_aria_indexes,                   Bytes,              Sum of all Aria Indexes.
    mysql_pct_aria_keys_from_mem,               Percentage,         aria_pagecache_reads / aria_pagecache_read_requests \* 100
    mysql_aria_pagecache_read_requests,         Number,             The number of requests to read something from the Aria page cache.
    mysql_aria_pagecache_reads,                 Number,             The number of Aria page cache read requests that caused a block to be read from the disk.


Credits, License
----------------

* Authors: `Linuxfabrik GmbH, Zurich <https://www.linuxfabrik.ch>`_
* License: The Unlicense, see `LICENSE file <https://unlicense.org/>`_.
* Credits:

    * heavily inspired by MySQLTuner (https://github.com/major/MySQLTuner-perl)
