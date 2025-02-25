Check memory-usage
==================

Overview
--------

Displays the amount of free and used memory in the system and checks how much physical memory is left across platforms by using the ``available`` field.

Hints:

* Be aware of the differences in memory counting between different tools like top, htop, glances, GNOME System Monitor etc.
* Memory counting also changed between different Linux Kernel versions.


Fact Sheet
----------

.. csv-table::
    :widths: 30, 70
    
    "Check Plugin Download",                "https://github.com/Linuxfabrik/monitoring-plugins/tree/main/check-plugins/memory-usage"
    "Check Interval Recommendation",        "Once a minute"
    "Can be called without parameters",     "Yes"
    "Compiled for",                         "Linux, Windows"
    "3rd Party Python modules",             "``psutil``"


Help
----

.. code-block:: text

    usage: memory-usage [-h] [-V] [--always-ok] [-c CRIT] [-w WARN]

    Displays amount of free and used memory in the system, checks against used memory in percent.

    optional arguments:
      -h, --help            show this help message and exit
      -V, --version         show program's version number and exit
      --always-ok           Always returns OK.
      -c CRIT, --critical CRIT
                            Set the critical threshold for memory usage (in percent). Default: 95
      -w WARN, --warning WARN
                            Set the warning threshold for memory usage (in percent). Default: 90


Usage Examples
--------------

.. code-block:: bash

    ./memory-usage
    ./memory-usage --warning 90 --critical 95
    
Output:

.. code-block:: text

    67.2% - total: 15.2GiB, used: 7.3GiB, available: 5.0GiB, free: 792.1MiB
    shared: 2.5GiB, buffers: 310.8MiB, cached: 6.8GiB

    Top3 most memory consuming processes:
    1. rambox: 15.4%
    2. Web Content: 9.6%
    3. packagekitd: 4.2%


States
------

* WARN or CRIT if total memory usage is above a given threshold.


Perfdata / Metrics
------------------

.. csv-table::
    :widths: 25, 15, 60
    :header-rows: 1
    
    Name,                                       Type,               Description                                           
    available,                                  Bytes,              "The memory that can be given instantly to processes without the system going into swap. This is calculated by summing different memory values depending on the platform and it is supposed to be used to monitor actual memory usage in a cross platform fashion."
    "buffers",                                  Bytes,              "Cache for things like file system metadata  (Linux, BSD)."
    "cached",                                   Bytes,              "Cache for various things  (Linux, BSD)."
    free,                                       Bytes,              "Memory not being used at all (zeroed) that is readily available; note that this doesn't reflect the actual memory available (use ``available`` instead). ``total - used`` does not necessarily match ``free``."
    "shared",                                   Bytes,              "Memory that may be simultaneously accessed by multiple processes  (Linux, BSD)."
    total,                                      Bytes,              "Total physical memory (exclusive swap)."
    usage_percent,                              Percentage,         
    used,                                       Bytes,              "Memory used, calculated differently depending on the platform and designed for informational purposes only. ``total - free`` does not necessarily match ``used``."


Troubleshooting
---------------

This checks sometimes reports > 100% memory usage
    That's fine, the RES column in ``top`` says the same if you sum up all values for a process (attention: the values in top's RES column are KB by default), and compare process memory to total physical system memory. The machine does not swap, so this is kind of Linux memory management mystery.


Credits, License
----------------

* Authors: `Linuxfabrik GmbH, Zurich <https://www.linuxfabrik.ch>`_
* License: The Unlicense, see `LICENSE file <https://unlicense.org/>`_.
* Credits:  https://github.com/giampaolo/psutil/blob/master/scripts/free.py
