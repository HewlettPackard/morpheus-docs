virtswitch
----------

Manage Virtual Switches on the HVM host. Virtual Switches abstract host-level networking (bonds, bridges, VLANs) into a simple management model.

.. danger:: Direct use of ``hvmcli virtswitch`` commands can disrupt host networking and cause loss of connectivity. It is **highly recommended** to manage Virtual Switches through the Morpheus UI (Infrastructure > Clusters > Network > Virtual Switches) instead. Only use these CLI commands when directed by HPE support or when the Morpheus UI is unavailable.

.. important:: The ``virtswitch`` namespace is only available on **HVM OS Ubuntu 26.04+** with **Cluster Layout 2.0**. On HVM OS Ubuntu 24.04, this namespace is hidden and unavailable.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``list``
     - List managed Virtual Switch configurations
   * - ``list-route``
     - List Virtual Switch routes and default route mappings
   * - ``create``
     - Create a new Virtual Switch
   * - ``import``
     - Import an existing network interface into a managed Virtual Switch
   * - ``edit``
     - Edit Virtual Switch configuration
   * - ``add-segment``
     - Add a VLAN segment to an existing Virtual Switch
   * - ``edit-segment``
     - Edit a segment or manage static routes
   * - ``delete-segment``
     - Delete a VLAN segment from a Virtual Switch
   * - ``delete``
     - Delete a Virtual Switch and its associated metadata
   * - ``rename``
     - Rename a Virtual Switch (metadata only)
   * - ``export``
     - Export Virtual Switch config as YAML or JSON
   * - ``show``
     - Show detailed Virtual Switch view
   * - ``status``
     - Show real-time operational status

virtswitch list
```````````````

List all managed Virtual Switches on the host.

.. code-block:: bash

   sudo hvmcli virtswitch list

Use ``--filter`` to show specific Virtual Switch types:

.. code-block:: bash

   sudo hvmcli virtswitch list --filter general
   sudo hvmcli virtswitch list --filter data
   sudo hvmcli virtswitch list --filter sdn
   sudo hvmcli virtswitch list --filter iscsi
   sudo hvmcli virtswitch list --filter bond
   sudo hvmcli virtswitch list --filter network

virtswitch list-route
`````````````````````

List routes and default route mappings for managed Virtual Switches.

.. code-block:: bash

   sudo hvmcli virtswitch list-route
   sudo hvmcli virtswitch list-route --virtswitch-name vs0 --json

Options:

- ``--virtswitch-name <name>`` — Limit output to a single Virtual Switch
- ``--json`` — JSON output

virtswitch create
`````````````````

Create a new Virtual Switch with specified uplinks and traffic configuration.

.. code-block:: bash

   sudo hvmcli virtswitch create \
     --virtswitch-name vs0 \
     --type general \
     --uplink-name eth0 \
     --traffic-type management \
     --ip 10.0.0.10 \
     --netmask 24 \
     --gateway 10.0.0.1

**Create with bonded uplinks:**

.. code-block:: bash

   sudo hvmcli virtswitch create \
     --virtswitch-name vs0 \
     --type general \
     --uplink-name eth0,eth1 \
     --uplink-mode active-backup

**Create with multiple traffic types and advanced bridge options:**

.. code-block:: bash

   sudo hvmcli virtswitch create \
     --virtswitch-name vs0 \
     --type general \
     --uplink-name eth0,eth1 \
     --uplink-mode 802.3ad \
     --lacp-rate fast \
     --transmit-hash-policy layer3+4 \
     --traffic-type management --ip 10.0.0.10 --netmask 24 --gateway 10.0.0.1 \
     --traffic-type data-nfs --ip 172.16.0.10 --netmask 24 --vlan-id 100 \
     --allowed-vlans 100-200 \
     --native-vlan 1 \
     --mtu 9000

Options:

- ``--virtswitch-name <name>`` — Name for the Virtual Switch (max 32 characters; alphanumeric, underscore, dot, hyphen)
- ``--type <general|iscsi|sdn>`` — Virtual Switch type
- ``--uplink-name <nic>[,<nic>]`` — Physical NIC(s) to use as uplinks
- ``--uplink-mode <active-backup|802.3ad>`` — Bond mode when using two uplinks
- ``--vlan-id <2-4094>`` — VLAN ID for tagged traffic (top-level; applies when no ``--traffic-type`` block is given)
- ``--traffic-type <vm|management|data-nfs|live-migration|iscsi|sdn>`` — Starts a traffic-type block; ``--ip``, ``--netmask``, ``--gateway``, and ``--vlan-id`` that follow apply to this block. Repeat ``--traffic-type`` to configure multiple segments in one command.
- ``--ip <address>`` — IP address for the preceding traffic type's segment
- ``--netmask <prefix|netmask|cidr>`` — Subnet mask, CIDR prefix, or dotted-decimal mask for the preceding segment
- ``--gateway <address>`` — Gateway for the preceding segment (metadata only for non-management segments; see :doc:`../../infrastructure/clusters/hvm/virtual_switches`)
- ``--bridge-mode <linux-bridge|ovs>`` — Bridge implementation (default: ``linux-bridge`` on HVM OS 26.04+; use ``ovs`` for legacy OVS behavior on HVM OS 24.04)
- ``--no-vlan-filtering`` — Disable VLAN filtering on the bridge (linux-bridge mode only)
- ``--stp`` — Enable Spanning Tree Protocol on the bridge
- ``--allowed-vlans <range>`` — VLAN ranges permitted on the bridge (linux-bridge mode)
- ``--native-vlan <id>`` — Untagged/native VLAN ID for the bridge
- ``--lacp-rate <fast|slow>`` — LACP transmit rate when ``--uplink-mode 802.3ad`` is used
- ``--transmit-hash-policy <layer3+4|layer2|layer2+3>`` — Bond load-balancing hash policy
- ``--mtu <1500|9000>`` — MTU size
- ``--interface-counter <N>`` — Reuse a specific interface counter instead of auto-allocating one (used by Morpheus during add-node/reconcile; not typically needed for manual use)
- ``--dry-run`` — Preview the changes without applying them
- ``--json`` — JSON output

.. note:: Bridge, bond, and libvirt network interface names (``hvmbrN``/``hvmupN``/``hvmnetN``) are auto-assigned from a cluster-scoped counter and are independent of ``--virtswitch-name``.

virtswitch import
`````````````````

Import an existing network interface or bridge (e.g., a pre-configured management bridge) into a managed Virtual Switch.

.. code-block:: bash

   sudo hvmcli virtswitch import --bridge br-mgmt --virtswitch-name vs0

**Import a raw interface instead of an existing bridge:**

.. code-block:: bash

   sudo hvmcli virtswitch import --interface eth0 --type general --virtswitch-name vs0

**Register metadata only, without touching host networking:**

.. code-block:: bash

   sudo hvmcli virtswitch import --bridge br-mgmt --virtswitch-name vs0 --metadata-only

This is used during cluster creation to adopt existing management bridges without disrupting connectivity.

Options:

- ``--bridge <name>`` — Existing bridge to adopt as a Virtual Switch
- ``--interface <name>`` — Existing raw interface to adopt (alternative to ``--bridge``)
- ``--type <general|iscsi|sdn>`` — Virtual Switch type to assign
- ``--virtswitch-name <name>`` — Name to assign to the imported Virtual Switch
- ``--force`` — Skip confirmation prompts
- ``--metadata-only`` — Register the Virtual Switch in metadata without modifying host networking
- ``--json`` — JSON output

virtswitch edit
```````````````

Edit an existing Virtual Switch configuration.

.. code-block:: bash

   sudo hvmcli virtswitch edit --virtswitch-name vs0 --mtu 9000 --force

**Change uplinks, bond mode, and load-balancing policy:**

.. code-block:: bash

   sudo hvmcli virtswitch edit \
     --virtswitch-name vs0 \
     --uplink-name eth0,eth1 \
     --uplink-mode 802.3ad \
     --lacp-rate fast \
     --transmit-hash-policy layer3+4

**Preview changes before applying:**

.. code-block:: bash

   sudo hvmcli virtswitch edit --virtswitch-name vs0 --mtu 9000 --dry-run --show-diff

Options:

- ``--virtswitch-name <name>`` — Name of the Virtual Switch to edit (required)
- ``--type <general|iscsi|sdn>`` — Change the Virtual Switch type
- ``--uplink-name <nic>[,<nic>]`` — Change the physical uplink(s)
- ``--uplink-mode <active-backup|802.3ad>`` — Change bond mode
- ``--lacp-rate <fast|slow>`` — LACP transmit rate when ``--uplink-mode 802.3ad`` is used
- ``--transmit-hash-policy <layer3+4|layer2|layer2+3>`` — Bond load-balancing hash policy
- ``--mtu <1500|9000>`` — New MTU value
- ``--force`` — Skip confirmation prompts
- ``--dry-run`` — Preview the changes without applying them
- ``--show-diff`` — Show a before/after diff of the configuration (typically paired with ``--dry-run``)
- ``--json`` — JSON output

virtswitch add-segment
``````````````````````

Add a VLAN segment to an existing general or iSCSI Virtual Switch.

.. code-block:: bash

   sudo hvmcli virtswitch add-segment \
     --virtswitch-name vs0 \
     --vlan-id 100 \
     --traffic-type data-nfs \
     --ip 172.16.0.10 \
     --netmask 24

**Register multiple traffic types on the same segment:**

.. code-block:: bash

   sudo hvmcli virtswitch add-segment \
     --virtswitch-name vs0 \
     --vlan-id 100 \
     --traffic-type data-nfs,live-migration \
     --ip 172.16.0.10 \
     --netmask 24

Options:

- ``--virtswitch-name <name>`` — Target Virtual Switch (required)
- ``--traffic-type <vm|management|data-nfs|live-migration|iscsi|sdn>`` — Traffic type; accepts a comma-separated list (e.g. ``data-nfs,live-migration``) to register multiple types on one segment
- ``--vlan-id <2-4094>`` — VLAN ID for the segment (required; pass an empty value ``''`` for untagged)
- ``--ip <address>`` — IP address for this segment (required for non-VM traffic types)
- ``--netmask <prefix|netmask|cidr>`` — Subnet mask, CIDR prefix, or dotted-decimal mask (required for non-VM traffic types)
- ``--gateway <address>`` — Gateway for this segment (metadata only for non-management segments)
- ``--mtu <1500|9000>`` — MTU size for this segment
- ``--metadata-only`` — Register the segment in metadata without modifying host networking
- ``--dry-run`` — Preview the changes without applying them
- ``--json`` — JSON output

virtswitch edit-segment
```````````````````````

Edit an existing segment's IP configuration, or manage static routes on it.

.. code-block:: bash

   sudo hvmcli virtswitch edit-segment \
     --virtswitch-name vs0 \
     --traffic-type data-nfs \
     --ip 172.16.0.20 \
     --force

**Change a segment's VLAN ID:**

.. code-block:: bash

   sudo hvmcli virtswitch edit-segment \
     --virtswitch-name vs0 \
     --traffic-type data-nfs \
     --new-vlan-id 150

**Preview changes before applying:**

.. code-block:: bash

   sudo hvmcli virtswitch edit-segment \
     --virtswitch-name vs0 \
     --traffic-type data-nfs \
     --gateway 172.16.0.1 \
     --dry-run --show-diff

Options:

- ``--virtswitch-name <name>`` — Target Virtual Switch (required)
- ``--traffic-type <vm|management|data-nfs|live-migration|iscsi|sdn>`` — Traffic type of the segment to edit (required)
- ``--ip <address>`` — New IP address for the segment
- ``--netmask <prefix|netmask|cidr>`` — New subnet mask, CIDR prefix, or dotted-decimal mask
- ``--gateway <address>`` — New gateway for the segment (metadata only for non-management segments)
- ``--new-vlan-id <new-id>`` — Change the segment's VLAN ID
- ``--force`` — Skip confirmation prompts
- ``--dry-run`` — Preview the changes without applying them
- ``--show-diff`` — Show a before/after diff of the configuration (typically paired with ``--dry-run``)
- ``--json`` — JSON output

virtswitch edit-segment add-route
``````````````````````````````````

Add a static route to a Virtual Switch segment.

.. code-block:: bash

   sudo hvmcli virtswitch edit-segment add-route \
     --virtswitch-name vs0 \
     --traffic-type data-nfs \
     --to 192.168.50.0/24 \
     --via 172.16.0.1

Options:

- ``--virtswitch-name <name>`` — Target Virtual Switch (required)
- ``--traffic-type <vm|management|data-nfs|live-migration|iscsi|sdn>`` — Traffic type of the segment (required)
- ``--to <destination-cidr|default>`` — Destination network in CIDR notation, or ``default`` for a default route (required)
- ``--via <gateway>`` — Next-hop gateway address (required)
- ``--force`` — Skip confirmation prompts
- ``--json`` — JSON output

virtswitch edit-segment delete-route
```````````````````````````````````````

Remove a static route from a Virtual Switch segment.

.. code-block:: bash

   sudo hvmcli virtswitch edit-segment delete-route \
     --virtswitch-name vs0 \
     --traffic-type data-nfs \
     --to 192.168.50.0/24 \
     --via 172.16.0.1

Options:

- ``--virtswitch-name <name>`` — Target Virtual Switch (required)
- ``--traffic-type <vm|management|data-nfs|live-migration|iscsi|sdn>`` — Traffic type of the segment (required)
- ``--to <destination-cidr|default>`` — Destination network in CIDR notation, or ``default`` for the default route (required)
- ``--via <gateway>`` — Next-hop gateway address of the route to remove (required)
- ``--force`` — Skip confirmation prompts
- ``--json`` — JSON output

virtswitch delete-segment
`````````````````````````

Remove a VLAN segment from a Virtual Switch.

.. code-block:: bash

   sudo hvmcli virtswitch delete-segment \
     --virtswitch-name vs0 \
     --traffic-type data-nfs

Options:

- ``--virtswitch-name <name>`` — Target Virtual Switch (required)
- ``--traffic-type <type>`` — Traffic type of the segment to delete (required)
- ``--dry-run`` — Preview the changes without applying them
- ``--show-diff`` — Show a before/after diff of the configuration (typically paired with ``--dry-run``)
- ``--json`` — JSON output

virtswitch delete
`````````````````

Delete a Virtual Switch and all associated host networking configuration.

.. code-block:: bash

   sudo hvmcli virtswitch delete --virtswitch-name vs0 --force

Options:

- ``--virtswitch-name <name>`` — Virtual Switch to delete (required)
- ``--force`` — Skip confirmation prompts
- ``--dry-run`` — Preview the changes without applying them
- ``--json`` — JSON output

.. warning:: Deleting a Virtual Switch removes all associated bridges, bonds, and VLAN configurations from the host.

virtswitch rename
`````````````````

Rename a Virtual Switch. This only updates metadata — no network changes are applied.

.. code-block:: bash

   sudo hvmcli virtswitch rename --virtswitch-name vs0 --new-name mySwitch

Options:

- ``--virtswitch-name <name>`` — Current Virtual Switch name (required)
- ``--new-name <name>`` — New name (required). Unlike ``virtswitch create``, ``rename`` does not enforce a maximum length — only alphanumeric characters, underscore, dot, and hyphen are allowed. To stay consistent with names created through ``virtswitch create`` or the UI, keep renamed switches to 32 characters or fewer.

virtswitch export
`````````````````

Export a Virtual Switch configuration as YAML or JSON.

.. code-block:: bash

   sudo hvmcli virtswitch export --virtswitch-name vs0 --format json
   sudo hvmcli virtswitch export --virtswitch-name vs0 --format yaml

Options:

- ``--virtswitch-name <name>`` — Virtual Switch to export (required)
- ``--format <yaml|json>`` — Output format (default: yaml)

virtswitch show
```````````````

Show a detailed view of a Virtual Switch including segments, uplinks, and bridge information.

.. code-block:: bash

   sudo hvmcli virtswitch show --virtswitch-name vs0
   sudo hvmcli virtswitch show --virtswitch-name vs0 --json

Options:

- ``--virtswitch-name <name>`` — Virtual Switch to show (required)
- ``--json`` — JSON output

virtswitch status
`````````````````

Show real-time operational status of Virtual Switches including link state and sync status.

.. code-block:: bash

   sudo hvmcli virtswitch status
   sudo hvmcli virtswitch status --virtswitch-name vs0 --json

Options:

- ``--virtswitch-name <name>`` — Limit output to a single Virtual Switch
- ``--json`` — JSON output
