deploy
------

Deploy VME (VM Essentials) Manager or Worker virtual machines on the HVM host.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``manager``
     - Deploy, manage, or check status of the VME Manager VM
   * - ``worker``
     - Deploy, manage, or check status of a VME Worker VM

deploy manager
``````````````

Deploy or manage the VME Manager appliance virtual machine.

**Deploy a new Manager VM:**

.. code-block:: bash

   sudo hvmcli deploy manager \
     --ip 10.1.1.100 \
     --netmask 255.255.255.0 \
     --gateway 10.1.1.1 \
     --hostname vme-mgr \
     --image file:///mnt/iso/morpheus.qcow2 \
     --interface vs0 \
     --admin-user morphadmin \
     --admin-password-stdin

Required flags:

- ``--ip <address>`` — Static IP address for the Manager VM
- ``--netmask <mask>`` — Subnet mask (e.g. ``255.255.255.0``)
- ``--hostname <name>`` — Hostname for the VM
- ``--image <uri>`` — Path or URL to the QCOW2 image (``file://`` or ``http(s)://``)
- ``--interface <name>`` — virtSwitch name (e.g. ``vs0``) on Linux-bridge hosts, or a raw interface name on legacy OVS hosts
- ``--admin-user <username>`` — Administrator username for the appliance
- ``--admin-password-stdin`` — Read admin password from stdin (secure, no shell history)

Optional flags:

- ``--gateway <address>`` — Default gateway. Optional; omit on networks that route management traffic without a gateway.
- ``--dns <servers>`` — DNS servers, comma-separated
- ``--search-domain <domain>`` — DNS search domain; repeatable or comma-separated (max 5)
- ``--ntp <server>`` — NTP server; repeatable or comma-separated (max 7)
- ``--appliance-url <url>`` — Appliance URL (default: ``https://<ip>``)
- ``--size <S|M|L>`` — VM size (default: ``M``)
- ``--proxy <url>`` — HTTP proxy URL
- ``--no-proxy <list>`` — No-proxy list
- ``--vlan-id <id>`` — Optional VLAN; a VLAN matching the Management segment's VLAN is treated as Management, a different VLAN is treated as Compute
- ``--compute-interface <iface>`` — Compute VLAN interface (OVS mode only; hidden on Linux-bridge hosts)
- ``--compute-vlan-tag <tag>`` — Compute VLAN tag (OVS mode only; hidden on Linux-bridge hosts)
- ``--no-rollback`` — Keep the VM in place on failure instead of rolling back
- ``--background`` — Run the deployment in background mode
- ``--json`` — JSON output

**Run pre-deployment checks:**

.. code-block:: bash

   sudo hvmcli deploy manager precheck \
     --ip 10.1.1.100 \
     --interface vs0 \
     --image file:///mnt/iso/morpheus.qcow2 \
     --json

**Check Manager VM status:**

.. code-block:: bash

   sudo hvmcli deploy manager status
   sudo hvmcli deploy manager status --json

**View Manager deployment logs:**

.. code-block:: bash

   sudo hvmcli deploy manager logs
   sudo hvmcli deploy manager logs --lines 100 --diagnostics

Options:

- ``--lines <n>`` — Number of log lines to display
- ``--diagnostics`` — Include diagnostic information

deploy worker
`````````````

Deploy or manage a VME Worker virtual machine.

**Deploy a new Worker VM:**

.. code-block:: bash

   sudo hvmcli deploy worker \
     --ip 10.1.1.110 \
     --netmask 255.255.255.0 \
     --gateway 10.1.1.1 \
     --hostname vme-wkr1 \
     --image file:///mnt/iso/morpheus.qcow2 \
     --interface vs0 \
     --worker-url https://10.1.1.100 \
     --worker-key abc123 \
     --apikey def456 \
     --admin-password-stdin

Required flags:

- ``--ip <address>`` — Static IP address for the Worker VM
- ``--netmask <mask>`` — Subnet mask
- ``--hostname <name>`` — Hostname for the VM
- ``--image <uri>`` — Path or URL to the QCOW2 image
- ``--interface <name>`` — virtSwitch name (e.g. ``vs0``) on Linux-bridge hosts, or a raw interface name on legacy OVS hosts
- ``--worker-url <url>`` — URL of the Manager VM to register with
- ``--worker-key <key>`` — Worker registration key from the Manager
- ``--apikey <key>`` — API key for Manager authentication
- ``--admin-password-stdin`` — Read admin password from stdin

.. note:: Unlike ``deploy manager``, ``deploy worker`` does not take ``--admin-user`` or ``--appliance-url`` — the Worker registers against the Manager using ``--worker-url``, ``--worker-key``, and ``--apikey`` instead.

Optional flags: ``--gateway``, ``--dns``, ``--search-domain``, ``--ntp``, ``--size``, ``--proxy``, ``--no-proxy``, ``--vlan-id``, ``--compute-interface``/``--compute-vlan-tag`` (OVS mode only), ``--no-rollback``, ``--background``, and ``--json`` behave the same as their :ref:`deploy manager <deploy manager>` equivalents above.

**Check Worker VM status:**

.. code-block:: bash

   sudo hvmcli deploy worker status --json

.. tip:: Always run ``deploy manager precheck`` before deploying to validate that network connectivity, image accessibility, and host resources are adequate.
