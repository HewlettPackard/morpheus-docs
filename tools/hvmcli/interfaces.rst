interfaces
----------

List network interfaces and manage SR-IOV virtual functions.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``list``
     - List network interfaces with optional filtering
   * - ``vf``
     - Manage SR-IOV virtual functions (list, create, delete, set)

interfaces list
```````````````

List all network interfaces on the host. By default shows all interfaces.

.. code-block:: bash

   sudo hvmcli interfaces list

.. code-block:: text

   Interface  Type      MAC                Speed    Link  Driver       Firmware  SR-IOV         Max VFs  Configured VFs  Manufacturer  LLDP
   ---------  --------  -----------------  -------  ----  ----------   --------  -------------  -------  --------------  ------------  ----
   ens1f0np0  Ethernet  b4:96:91:a0:12:34  25Gbps   up    mlx5_core   22.39     supported      64       0               Mellanox      sw1/Eth1/1
   ens2f0np0  Ethernet  b4:96:91:a0:12:35  25Gbps   up    mlx5_core   22.39     supported      64       0               Mellanox      sw1/Eth1/2
   enp3s0     Ethernet  52:54:00:86:9e:12  -        up    virtio_net  -         not supported  0        0               Red Hat       -

Use ``--filter`` to show specific interface types:

.. code-block:: bash

   sudo hvmcli interfaces list --filter ethernet
   sudo hvmcli interfaces list --filter fc
   sudo hvmcli interfaces list --filter network
   sudo hvmcli interfaces list --filter data
   sudo hvmcli interfaces list --filter sdn

Filter options:

- ``ethernet`` — Physical Ethernet interfaces only
- ``fc`` — Fibre Channel HBAs only
- ``network`` — Interfaces that are part of a Virtual Switch network
- ``general`` — General purpose interfaces
- ``data`` — Data network interfaces
- ``sdn`` — SDN network interfaces

interfaces vf
`````````````

Manage SR-IOV Virtual Functions (VFs) on supported interfaces.

**List VFs on an interface:**

.. code-block:: bash

   sudo hvmcli interfaces vf list --interface ens1f0np0

**Create VFs on an interface:**

.. code-block:: bash

   sudo hvmcli interfaces vf create --interface ens1f0np0 --count 4

**Delete VFs from an interface:**

.. code-block:: bash

   sudo hvmcli interfaces vf delete --interface ens1f0np0

**Set VF properties:**

.. code-block:: bash

   sudo hvmcli interfaces vf set --interface ens1f0np0 --vf 0 --mac 00:11:22:33:44:55

**Set VLAN, rate limit, and trust/anti-spoofing properties:**

.. code-block:: bash

   sudo hvmcli interfaces vf set --interface ens1f0np0 --vf 0 --vlan 100 --qos 500 --spoofchk off --trust on --state enable

Options:

- ``--interface <name>`` — Physical interface owning the VF (required)
- ``--vf <id>`` — VF index (required)
- ``--mac <mac>`` — MAC address to assign to the VF
- ``--vlan <0-4094>`` — VLAN ID to assign to the VF
- ``--qos <0-10000>`` — Rate-limit QoS value for the VF
- ``--spoofchk <on|off>`` — Enable or disable MAC/VLAN anti-spoofing checks
- ``--trust <on|off>`` — Mark the VF as trusted, allowing the guest to set otherwise-privileged settings (e.g. promiscuous mode)
- ``--state <auto|enable|disable>`` — Administrative link state of the VF
- ``--json`` — JSON output

.. note:: SR-IOV must be enabled in the BIOS and the NIC must support virtual functions. Use ``hvmcli interfaces list`` to check SR-IOV support status.
