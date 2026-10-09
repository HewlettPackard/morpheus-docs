storage
-------

Manage storage pools, iSCSI targets, Fibre Channel HBAs, and multipath configuration.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``list``
     - List storage pools
   * - ``iscsi``
     - Manage iSCSI targets (configure, delete, list, multipath)
   * - ``fc``
     - Manage Fibre Channel HBAs (list, multipath)
   * - ``multipath``
     - Manage multipath configuration (status, validate, configure, apply)
   * - ``rescan``
     - Rescan iSCSI/FC storage for new LUNs

storage list
````````````

List all storage pools configured on the host.

.. code-block:: bash

   sudo hvmcli storage list

storage iscsi
`````````````

Manage iSCSI target connections.

**List iSCSI sessions:**

.. code-block:: bash

   sudo hvmcli storage iscsi --list

**Show iSCSI multipath status:**

.. code-block:: bash

   sudo hvmcli storage iscsi --multipath

**Configure a new iSCSI target:**

.. code-block:: bash

   sudo hvmcli storage iscsi configure --portal 10.0.0.1

**Configure a target with a specific initiator name:**

.. code-block:: bash

   sudo hvmcli storage iscsi configure --portal 10.0.0.1:3260 --initiator-name iqn.2024-01.com.example:host01

**Delete an iSCSI target:**

.. code-block:: bash

   sudo hvmcli storage iscsi delete --portal 10.0.0.1

Options:

- ``--list`` — List current iSCSI sessions
- ``--multipath`` — Show multipath status for iSCSI devices
- ``--portal <ip[:port]>`` — iSCSI target portal IP address, with an optional port (default: 3260)
- ``--initiator-name <iqn>`` — Override the host's iSCSI initiator name (IQN) used to discover and log in to the target

storage fc
``````````

Manage Fibre Channel HBA connections.

**List Fibre Channel HBAs:**

.. code-block:: bash

   sudo hvmcli storage fc --list

**Show FC multipath status:**

.. code-block:: bash

   sudo hvmcli storage fc --multipath

storage multipath
`````````````````

Manage multipath I/O configuration for both iSCSI and FC storage.

**Show multipath status:**

.. code-block:: bash

   sudo hvmcli storage multipath status

**Validate multipath configuration:**

.. code-block:: bash

   sudo hvmcli storage multipath validate

**Apply default multipath configuration:**

.. code-block:: bash

   sudo hvmcli storage multipath configure-default

**Force apply multipath configuration:**

.. code-block:: bash

   sudo hvmcli storage multipath apply --force

storage rescan
``````````````

Rescan iSCSI and Fibre Channel storage to discover new LUNs.

.. code-block:: bash

   sudo hvmcli storage rescan

By default, ``rescan`` scans both iSCSI and FC, then reloads multipath maps and reports any newly discovered devices. Use ``--iscsi`` or ``--fc`` to scope the rescan to a single transport:

.. code-block:: bash

   sudo hvmcli storage rescan --iscsi
   sudo hvmcli storage rescan --fc

Options:

- ``--iscsi`` — Rescan iSCSI sessions only
- ``--fc`` — Rescan Fibre Channel HBAs only
- ``--json`` — JSON output

.. tip:: Run ``storage rescan`` after provisioning new LUNs on your storage array to make them visible to the host.
