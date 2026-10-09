health
------

Run host readiness and health checks.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``check``
     - Run host readiness checks
   * - ``watch``
     - Continuously run health checks at a polling interval

health check
````````````

Run a comprehensive set of host readiness checks including cluster state, VM health, NTP sync, resource availability, gateway reachability, agent status, uplink state, iSCSI connectivity, and multipath configuration.

.. code-block:: bash

   sudo hvmcli health check

.. code-block:: bash

   sudo hvmcli health check --json

The check covers:

- **Cluster** — Cluster membership and quorum state
- **VMs** — VM health and consistency
- **NTP** — Time synchronization status
- **Resources** — CPU, memory, and disk availability
- **Gateway** — Network gateway reachability
- **Agent** — Morpheus agent connectivity
- **Uplinks** — Physical NIC link state
- **iSCSI** — iSCSI session health (if configured)
- **Multipath** — Multipath path availability (if configured)

health watch
````````````

Continuously run health checks at a specified interval.

.. code-block:: bash

   sudo hvmcli health watch --interval 30

Options:

- ``--interval <seconds>`` — Polling interval in seconds (default: 30, minimum: 5)

.. tip:: Use ``health watch`` during maintenance operations to continuously monitor host health and detect issues early.
