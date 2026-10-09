Adding and Removing Hosts
=========================

Adding a Host to an Existing Cluster
-------------------------------------

Hosts can be added to an existing HVM cluster to increase compute capacity or improve failure tolerance.

.. note:: The agent quorum, Corosync, DLM, and GFS2 details on this page apply to layouts 1.3 and 2.0. See :doc:`/infrastructure/clusters/mvm` for Legacy host management.

Prerequisites
^^^^^^^^^^^^^

Before adding a new host:

- The new host meets all requirements listed in :doc:`building_clusters` (OS, CPU, memory, network, storage)
- The new host has network connectivity to all existing cluster hosts on port 7443
- Shared storage (iSCSI targets) is accessible from the new host
- Sufficient license capacity is available

Procedure
^^^^^^^^^

#. Navigate to ``Infrastructure > Clusters > [Cluster]``
#. Click ``Action > Add HVM Hypervisor``
#. Provide the new host's SSH IP, hostname, and credentials
#. Click Complete`

What Happens Automatically
^^^^^^^^^^^^^^^^^^^^^^^^^^

When a host is added to a layout 1.3 or 2.0 cluster, |morpheus| performs the following automated operations:

#. **Package installation and host preparation** — The same provisioning phases run as during initial cluster creation (KVM, Corosync, DLM, layout-specific networking, firewall, libvirt)

#. **Corosync authkey distribution** — The cluster's Corosync authentication key is retrieved from Cypher and installed on the new host

#. **Corosync configuration update** — The new host's node block is added to ``corosync.conf`` on all existing hosts, the ``config_version`` is incremented, and ``corosync-cfgtool -R`` is run on each host to reload the configuration live (no restart required)

#. **DLM join** — The new host joins the DLM cluster and gains access to existing lockspaces

#. **GFS2 journal addition** — For each HPE Clustered Datastore (Shared LUN), a new journal is added using ``gfs2_jadd`` if the current journal count is less than the new total node count

#. **iSCSI target discovery** — All configured iSCSI targets are discovered and auto-logged-in on the new host

#. **Quorum list update** — |morpheus| sends updated ``quorumInfo`` to all agents in the cluster (including the new host). Each agent persists the updated member list to ``.quorum-nodes`` and begins peer-to-peer quorum pinging with the new host

#. **Storage pool creation** — libvirt storage pools are created on the new host for each existing datastore

Quorum Impact
^^^^^^^^^^^^^

Adding a host changes the quorum majority calculation:

.. list-table::
   :widths: 20 20 30 30
   :header-rows: 1

   * - Previous Size
     - New Size
     - New Majority Required
     - Failure Tolerance
   * - 3
     - 4
     - 3
     - 1 host
   * - 4
     - 5
     - 3
     - 2 hosts
   * - 5
     - 6
     - 4
     - 2 hosts
   * - 6
     - 7
     - 4
     - 3 hosts

.. NOTE:: Adding a 4th host to a 3-node cluster increases the majority requirement from 2 to 3 without improving failure tolerance. Consider adding hosts in pairs (3→5, 5→7) for optimal availability.

Verification After Adding
^^^^^^^^^^^^^^^^^^^^^^^^^^

After the new host is provisioned:

#. Navigate to ``Infrastructure > Clusters > [Cluster] > Summary > Quorum`` panel
#. Confirm the total node count has increased
#. Confirm the new host shows as ONLINE
#. On the new host, verify Corosync membership:

   .. code-block:: bash

      corosync-quorumtool -l

#. Verify DLM sees all members:

   .. code-block:: bash

      dlm_tool status -v

#. Compare physical-interface inventory with ``sudo hvmcli interfaces list --filter ethernet``. If an interface was added to the host after enrollment, follow the Netplan and UI refresh procedure in :ref:`hvm-host-prep` before assigning it to a Virtual Switch.

Removing a Host from an Existing Cluster
------------------------------------------

Hosts can be removed from an HVM cluster when decommissioning hardware, reducing cluster size, or replacing failed nodes.

.. WARNING:: Removing a host reduces the cluster's failure tolerance. Ensure the remaining cluster size still meets your availability requirements before proceeding.

Prerequisites
^^^^^^^^^^^^^

Before removing a host:

- Confirm the cluster layout and ensure the resulting cluster will still have quorum (at minimum 3 nodes for a single-site cluster)
- Confirm the Quorum panel reports ACHIEVED, all surviving hosts are reachable, lockspaces are healthy, and shared datastores are mounted with healthy paths
- Evacuate all VMs from the host using maintenance mode (see :doc:`host_maintenance`)
- Verify no VMs are pinned to the host that cannot be moved
- Confirm no local or raw-device VM remains on the host and that remaining hosts have the required VM networks and storage access

Procedure
^^^^^^^^^

#. Place the host in maintenance mode to evacuate VMs (see :doc:`host_maintenance`)
#. Navigate to ``Infrastructure > Clusters > [Cluster] > Hosts``
#. Select the host to remove
#. Click ``Delete``
#. Confirm Deletion
#. Wait for the removal operation to finish; do not manually edit Corosync or Agent quorum files

What Happens Automatically
^^^^^^^^^^^^^^^^^^^^^^^^^^

When a host is removed from a layout 1.3 or 2.0 cluster, |morpheus| performs the following:

#. **GFS2 unmount** — All HPE Clustered Datastores (GFS2 filesystems) are unmounted on the departing host

#. **Cluster services stop** — DLM and Corosync are stopped and disabled on the departing host

#. **Corosync configuration update** — The departing host's node block is removed from ``corosync.conf`` on all remaining hosts. The configuration is archived (backed up to ``/etc/corosync/archive/``), ``config_version`` is incremented, and ``corosync-cfgtool -R`` reloads the configuration live on each remaining host

#. **Datastore cleanup** — Each datastore's location reference to the removed host is cleaned up

#. **Quorum list update** — Updated ``quorumInfo`` is sent to all remaining agents. The departing host is removed from ``.quorum-nodes`` on all cluster members

Quorum Impact When Removing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 20 20 30 30
   :header-rows: 1

   * - Previous Size
     - New Size
     - New Majority Required
     - Failure Tolerance
   * - 7
     - 6
     - 4
     - 2 hosts
   * - 6
     - 5
     - 3
     - 2 hosts
   * - 5
     - 4
     - 3
     - 1 host
   * - 4
     - 3
     - 2
     - 1 host

.. IMPORTANT:: Never reduce a single-site cluster below 3 nodes. A 2-node cluster cannot achieve quorum if either node fails, resulting in a complete cluster outage.

Handling Offline Host Removal
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the host to be removed is offline (powered off or unreachable):

- First confirm the host is powered off or otherwise cannot access shared storage. If isolation cannot be proven, do not remove it; contact HPE Support
- Use the same UI :guilabel:`Remove` action. On layout 1.3 and later, |morpheus| removes the node from the Corosync ring using surviving online hosts and updates Agent quorum membership
- If the departing node is still active in the Corosync ring, or the updated Corosync configuration cannot be written consistently to all survivors, |morpheus| aborts the reload and raises an alarm rather than risk partitioning the ring
- If no other hosts are online, cleanup cannot proceed and |morpheus| raises an **Unable to remove host** alarm

Do not publish or use direct host-shell removal commands as a substitute for this workflow. If the UI operation raises an alarm or cannot establish a safe ring departure, retain the host record and contact HPE Support.

Post-removal Verification
^^^^^^^^^^^^^^^^^^^^^^^^^

#. Confirm the host no longer appears in the cluster Hosts list
#. Confirm the Quorum panel reports ACHIEVED with the new member count and no removed host under down, fenced, or unclean members
#. Confirm Corosync and DLM list only the remaining members and lockspaces are healthy
#. Confirm every shared datastore is mounted and healthy on every remaining host
#. Confirm evacuated or failed-over VMs run once, on hosts with their required networks and storage
