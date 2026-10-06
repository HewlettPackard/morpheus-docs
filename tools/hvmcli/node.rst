node
----

Manage the physical HVM host node configuration.

All ``node`` commands require ``sudo`` — see :doc:`hvmcli` for ``hvmcli``'s root-privilege requirement.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``list``
     - List node hardware, OS, and configuration summary
   * - ``show-config``
     - Show current NTP, DNS, and proxy configuration
   * - ``configure``
     - Configure node settings (NTP, DNS, hostname, proxy, locale, keymap, timezone, log-forwarding)
   * - ``list-locales``
     - List available system locales
   * - ``list-keymaps``
     - List available console keymaps
   * - ``list-timezones``
     - List available timezones
   * - ``backup``
     - Backup node configuration
   * - ``restore``
     - Restore node configuration from backup
   * - ``reboot``
     - Reboot the node
   * - ``shutdown``
     - Shut down the node
   * - ``events``
     - Query recent system journal events

node list
`````````

Display a summary of the node's hardware, operating system, and basic configuration.

.. code-block:: bash

   sudo hvmcli node list

.. code-block:: text

   Hostname            Vendor    Model  Serial        CPUs  CPU Model                       CPU MHz   Memory(GB)  OS                  Kernel             Uptime
   ------------------  --------  -----  -----------   ----  ------------------------------  --------  ----------  ------------------  -----------------  ------------------------
   hvm-node-01         HPE       DL360  MXQ123456     64    AMD EPYC 9334 32-Core           2695 MHz  512.0 GB    Ubuntu 24.04.4 LTS  6.8.0-134-generic  up 5 days, 12 hours

node show-config
````````````````

Display the current NTP, DNS, proxy, and search domain configuration.

.. code-block:: bash

   sudo hvmcli node show-config

.. code-block:: text

     NTP Servers         time.google.com, ntp.ubuntu.com
     DNS Servers         10.227.1.91, 10.227.1.92
     DNS Search Domains  example.com
     HTTP Proxy          -

node configure
``````````````

Configure node settings. ``configure`` takes a target sub-command (``ntp``, ``dns``, ``hostname``, ``proxy``, ``locale``, ``keymap``, ``timezone``, or ``log-forwarding``), each with its own flags.

.. code-block:: text

   Usage: hvmcli node configure <ntp|dns|hostname|proxy|locale|keymap|timezone|log-forwarding> [options]

**NTP Configuration:**

NTP servers are added or removed incrementally (max 3 values per invocation):

.. code-block:: bash

   sudo hvmcli node configure ntp --add 0.pool.ntp.org --add 1.pool.ntp.org
   sudo hvmcli node configure ntp --remove 0.pool.ntp.org

**DNS Configuration:**

DNS servers and search domains are also added or removed incrementally (max 3 servers, 5 domains):

.. code-block:: bash

   sudo hvmcli node configure dns --add 10.0.0.1 --add 10.0.0.2
   sudo hvmcli node configure dns --remove 10.0.0.1
   sudo hvmcli node configure dns --search-domain example.com --search-domain corp.local
   sudo hvmcli node configure dns --remove-search-domain example.com

**Hostname:**

.. code-block:: bash

   sudo hvmcli node configure hostname --add hvm-node-01

**HTTP Proxy:**

Configures the package manager's proxy endpoint. Provide credentials with ``--username`` and, optionally, a password piped in on stdin:

.. code-block:: bash

   sudo hvmcli node configure proxy --url http://proxy.example.com:3128
   printf 'secret' | sudo hvmcli node configure proxy --url http://proxy.example.com:3128 --username proxyuser --password-stdin
   sudo hvmcli node configure proxy --remove

**Locale, Keymap, and Timezone:**

.. code-block:: bash

   sudo hvmcli node configure locale --set en_US.UTF-8
   sudo hvmcli node configure locale --remove fr_FR.UTF-8
   sudo hvmcli node configure keymap --set us
   sudo hvmcli node configure timezone --set America/New_York

.. note:: For locale, ``--set`` and ``--remove`` cannot be combined in one invocation. The active system locale and the built-in ``C``, ``C.UTF-8``, and ``POSIX`` locales cannot be removed. Use ``node list-locales``, ``node list-keymaps``, and ``node list-timezones`` to see the supported values, and ``node show-config`` to see the active settings.

**Log Forwarding:**

.. code-block:: bash

   sudo hvmcli node configure log-forwarding show
   sudo hvmcli node configure log-forwarding configure --destination syslog.example.com --protocol tcp --port 514 --format rfc5424
   sudo hvmcli node configure log-forwarding disable

Options for ``log-forwarding configure``:

- ``--destination <host|ip>`` — Remote syslog destination (required)
- ``--protocol <tcp|udp>`` — Transport protocol
- ``--port <1-65535>`` — Destination port
- ``--format <rfc5424|rfc3164>`` — Syslog message format
- ``--json`` — JSON output

node list-locales
`````````````````

List all available system locales.

.. code-block:: bash

   sudo hvmcli node list-locales

node list-keymaps
`````````````````

List all available console keymaps.

.. code-block:: bash

   sudo hvmcli node list-keymaps

node list-timezones
```````````````````

List all available timezones.

.. code-block:: bash

   sudo hvmcli node list-timezones

node backup
```````````

Create a backup of the node's current configuration.

.. code-block:: bash

   sudo hvmcli node backup

node restore
````````````

Restore node configuration from a previously created backup.

.. code-block:: bash

   sudo hvmcli node restore

node reboot
```````````

Reboot the node.

.. code-block:: bash

   sudo hvmcli node reboot

.. warning:: This will restart the host and all running VMs will be interrupted. Ensure VMs are properly shut down or migrated before rebooting.

node shutdown
`````````````

Shut down the node.

.. code-block:: bash

   sudo hvmcli node shutdown

.. warning:: This will power off the host. All running VMs will be forcefully stopped.

node events
```````````

Query recent system journal events.

.. code-block:: bash

   sudo hvmcli node events
