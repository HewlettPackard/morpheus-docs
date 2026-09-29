Getting Started
---------------

Installation
````````````

``hvmcli`` is pre-installed on all HPE HVM hosts. No manual installation is required.

To verify the installed version:

.. code-block:: bash

   sudo hvmcli version

.. code-block:: text

   hvmcli 1.0.0 (build 20260719)

Usage
`````

The general syntax for ``hvmcli`` commands is:

.. code-block:: bash

   sudo hvmcli <namespace> <command> [options]

For example:

.. code-block:: bash

   sudo hvmcli vm list
   sudo hvmcli node list
   sudo hvmcli virtswitch list

To get help on any namespace:

.. code-block:: bash

   sudo hvmcli <namespace> help

Global Options
``````````````

The following options are available for all commands:

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Option
     - Description
   * - ``--json``
     - Output results in JSON format. Useful for scripting and automation.
   * - ``--debug``
     - Enable verbose debug logging to stderr for troubleshooting.
   * - ``--help``
     - Show help for the current namespace or command.

JSON Output
```````````

All commands support ``--json`` output for automation and integration with other tools. JSON responses follow a standard envelope format:

.. code-block:: json

   {
     "errorCode": 0,
     "messages": "",
     "data": { ... }
   }

- ``errorCode``: ``0`` indicates success, non-zero indicates an error.
- ``messages``: Empty on success, contains error description on failure.
- ``data``: The command-specific response payload.

Namespace Availability
``````````````````````

Not all namespaces are available on every HVM version:

.. list-table::
   :header-rows: 1
   :widths: 25 35 40

   * - Namespace
     - HVM OS (Ubuntu 24.04)
     - HVM OS (Ubuntu 26.04+)
   * - ``virtswitch``
     - Hidden
     - Available
   * - ``deploy``
     - Hidden
     - Available
   * - ``tui``
     - Hidden
     - Available

All other namespaces are available on both versions.

.. note:: The ``virtswitch`` namespace also requires **Cluster Layout 2.0**. It is not available on clusters using earlier layout versions regardless of HVM OS version.

Namespaces
``````````

``hvmcli`` is organized into the following namespaces:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Namespace
     - Description
   * - ``vm``
     - Manage virtual machines (list, start, stop, restart)
   * - ``node``
     - Manage the physical node (configuration, backup, reboot)
   * - ``interfaces``
     - List network interfaces and manage SR-IOV virtual functions
   * - ``virtswitch``
     - Manage Virtual Switches (create, edit, delete, import)
   * - ``network``
     - List virtual networks and run connectivity tests
   * - ``storage``
     - Manage storage pools, iSCSI, Fibre Channel, and multipath
   * - ``cluster``
     - Show cluster state and quorum information
   * - ``hardware``
     - Inspect host hardware (CPU, memory, PCI, boot volume, IPMI)
   * - ``software``
     - Inspect installed packages and manage OS updates
   * - ``logs``
     - View node, cluster, and agent logs
   * - ``health``
     - Run host readiness and health checks
   * - ``top``
     - Real-time host and VM resource monitoring
   * - ``security``
     - Security compliance operations (FIPS 140)
   * - ``deploy``
     - Deploy VME Manager or Worker virtual machines
   * - ``tui``
     - Interactive terminal user interface
   * - ``version``
     - Show installed ``hvmcli`` version and build information
