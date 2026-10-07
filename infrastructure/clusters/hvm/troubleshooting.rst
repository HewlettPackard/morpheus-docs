Troubleshooting & Diagnostics
==============================

Quick Health Check (UI)
-----------------------

Navigate to |InfClu|, open the cluster, and expand the **Summary** Quorum panel.

The Quorum panel displays:

- Quorum status (ACHIEVED / LOST)
- Total nodes / reachable / online / offline count
- Fenced node count
- Designated Coordinator
- Down hosts
- Lockspaces status
- Configured datastores
- Witness status
- Site information (stretch only; present when Site Groups exist)

Diagnostic Commands
-------------------

.. NOTE:: Layouts 1.3 and 2.0 do not use Pacemaker or ``pcs`` commands. Use the commands below for these layouts. Legacy clusters use the Pacemaker diagnostics in :doc:`/infrastructure/clusters/mvm`.

Corosync
^^^^^^^^

In layouts 1.3 and 2.0, Corosync provides the node membership list to DLM but is **not** used for quorum decisions. The |morpheus| Agent runs its own quorum system (via the ``morphd`` QuorumCheckService), which is how split quorum is achieved for two-node GFS2 clusters (:doc:`two_node_clusters`) and stretch clusters (:doc:`stretch_clusters`). Corosync's ``Quorate`` state has no impact on cluster operation.

List cluster members:

.. code-block:: bash

   corosync-quorumtool -l

Query Corosync configuration map:

.. code-block:: bash

   corosync-cmapctl

DLM
^^^

Check DLM status (verbose):

.. code-block:: bash

   dlm_tool status -v

List DLM lockspaces:

.. code-block:: bash

   dlm_tool ls

Fence acknowledgement is managed by the |morpheus| Agent. Do not acknowledge a fence manually unless HPE Support has verified host isolation and directs the operation.

Agent Quorum
^^^^^^^^^^^^

Query the agent quorum endpoint (returns JSON):

.. code-block:: bash

   curl -k https://localhost:7443/quorum

View cluster member configuration:

.. code-block:: bash

   cat /opt/morpheus-node/.quorum-nodes

View heartbeat configuration:

.. code-block:: bash

   cat /opt/morpheus-node/.hb

View mounted datastores:

.. code-block:: bash

   cat /opt/morpheus-node/.mounts

GFS2 Filesystem
^^^^^^^^^^^^^^^

Check active GFS2 mounts:

.. code-block:: bash

   mount | grep gfs2

Check for GFS2 withdrawal:

.. code-block:: bash

   cat /sys/fs/gfs2/*/withdraw

Check for hanging mounts:

.. code-block:: bash

   ps -aux | grep mount

Multipath
^^^^^^^^^

For layout 2.0, collect read-only storage state with:

.. code-block:: bash

   sudo hvmcli storage multipath validate
   sudo hvmcli storage multipath status
   sudo hvmcli storage fc --multipath
   sudo hvmcli storage iscsi --multipath

Confirm the expected WWN-based device is present and at least one path remains active. Do not create a datastore or return a host to service while all paths are failed. See :doc:`storage_operations` for the canonical multipath and stable-device guidance.

Log Files
^^^^^^^^^

.. list-table::
   :widths: 50 50
   :header-rows: 1

   * - Log Location
     - Contents
   * - ``/var/log/dlm_controld/dlm_controld.log``
     - DLM control daemon log
   * - ``/var/log/kern.log``
     - Kernel messages (GFS2/DLM events)
   * - ``journalctl -u dlm``
     - DLM systemd journal
   * - ``dmesg``
     - Kernel ring buffer (recent events)

Common Issues
-------------

HVM OS Installer Media or Disk Error
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If HVM OS installation reports a checksum, storage, or unknown error, preserve evidence before changing the host:

#. Record the complete error and installation stage, and collect the installer logs.
#. Verify the ISO checksum against the value published for that release. If it differs, obtain the ISO again from HPE and recreate or remount the installation media.
#. Check management-controller virtual-media events and network reachability when the ISO is mounted by URL.
#. Run the server and storage-controller hardware diagnostics. Confirm the intended OS disk and RAID or HBA configuration are healthy and supported.
#. Confirm that required data on every candidate disk is backed up and recoverable.

Disk erasure is a destructive troubleshooting step, not a routine installation prerequisite and not a general checksum fix. Consider it only when media integrity and hardware checks pass, the selected installation disk contains an unwanted prior layout, and HPE Support or the server/storage owner directs reinitialization. Use the hardware vendor's documented erase or logical-drive recreation procedure. Verify disk identifiers before proceeding; erasing or recreating a logical drive permanently removes its partitions and data. Afterward, rerun hardware diagnostics and restart installation with verified media.

If the error persists, stop repeated installation or erase attempts and provide HPE Support with the ISO checksum, host model and firmware, storage-controller configuration and diagnostics, media method, exact error, and installer logs.

Legacy ``hpe-vm console`` Exit or Save Prompt
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Current HVM hosts use ``hvmcli`` commands and the |morpheus| Virtual Switch workflow for supported host configuration. Use the legacy interactive ``hpe-vm console`` only when the documentation for the installed HVM OS release or HPE Support explicitly directs it; do not repeat network or time-zone configuration solely because installation is complete.

When using a legacy console, distinguish **Save** from **Apply**. After editing networking, accept the disconnect warning only when you have alternate console access, apply the change, and verify the resulting address and connectivity before exiting. If an exit dialog still reports unsaved changes, cancel the exit and verify whether the change was applied rather than assuming that **Save** made it active or choosing to discard it.

If the terminal colors or cursor placement remain incorrect after leaving the legacy full-screen console, run ``reset``. If ``reset`` is unavailable, run ``clear`` to redraw the screen; reconnect the terminal if display state remains incorrect. These commands repair terminal display state and do not apply network configuration.

Node Unreachable
^^^^^^^^^^^^^^^^

**Symptoms:** Node shows as OFFLINE in the Quorum panel.

**Resolution:**

#. Verify the |morpheus| agent is running on the affected host
#. Check network connectivity between hosts
#. Confirm port 7443 is open and accessible
#. Check agent logs for connectivity errors

Offline, Fenced, or Unclean Node
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First identify the cluster layout. For layouts 1.3 and 2.0, use the Quorum panel and Agent quorum endpoint—not ``pcs``—to record quorum, reachable nodes, ``fenced``, ``waitFencing``, lockspaces, and datastore status. Also collect Corosync membership, DLM status, GFS2 mounts, storage-path health, and agent/kernel logs before taking action.

Before recovery, verify why the host lost membership and confirm it cannot concurrently access shared storage from an isolated network path. Restore the failed management, quorum, or storage path before allowing the host to rejoin.

For layouts 1.3 and 2.0, reboot the fenced or unclean host. The Agent re-enters its quorum cycle after boot. Do not issue ``pcs resource cleanup`` or manually acknowledge a DLM fence as a general unclean-state recovery step.

After reboot, confirm all of the following before placing workloads on the host:

- The host is ONLINE and the Agent quorum endpoint reports the expected membership and quorum state
- Corosync and DLM list the expected members and no lockspace is waiting on fencing
- Every expected GFS2 datastore is mounted and not withdrawn
- FC or iSCSI multipath reports the expected active paths
- VMs are not duplicated between the recovered host and failover hosts

If the host cannot be isolated, storage paths remain ambiguous, quorum is not achieved, a lockspace remains blocked, or the host does not rejoin cleanly after reboot, stop and contact HPE Support. Legacy layouts use the separate Pacemaker procedures in :doc:`/infrastructure/clusters/mvm`.

DLM Wait Fencing
^^^^^^^^^^^^^^^^^

**Symptoms:** DLM operations stall, waiting for fence acknowledgement.

**Resolution:**

- The |morpheus| agent should automatically issue ``fence_ack`` after confirming the fenced node cannot access storage
- If automatic fencing is not resolving, retain the collected quorum, DLM, storage, and Agent evidence and contact HPE Support. Do not bypass fencing while host isolation is uncertain.

GFS2 Withdrawn
^^^^^^^^^^^^^^

**Symptoms:** GFS2 filesystem enters withdrawn state, I/O operations fail.

**Resolution:**

#. Attempt to unmount and remount the filesystem
#. If remount fails, reboot the affected host
#. Investigate the cause (storage connectivity, DLM issue) before the host rejoins

Different ``df -h`` Utilization Across Hosts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Symptoms:** Hosts mounting the same HPE Clustered Datastore report different used space, available space, or utilization percentages in ``df -h``.

Temporary differences are normal for GFS2 because each host's space-accounting changes are synchronized periodically. A difference alone is not evidence of filesystem corruption. Confirm the datastore and mount point, then compare readings again after storage activity settles. See :ref:`hvm-gfs2-space-reporting` for details and when further investigation is appropriate.

GFS2 Capacity Does Not Increase After LUN Expansion
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Array capacity, host path capacity, the multipath map, any intermediate block layer, and the GFS2 filesystem are separate layers. Do not rerun grow commands until each host reports the same stable WWN device and size. Collect array/LUN identity, ``lsblk``, multipath status, active mounts, ``df -hT``, DLM/quorum state, and kernel logs, then follow the coordinated procedure and stop points in :doc:`storage_operations`. Never recreate or shrink the filesystem to correct a size mismatch.

Corosync Shows Not Quorate
^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Symptoms:** ``corosync-quorumtool -s`` shows ``Quorate: No``.

**Impact:** In layouts 1.3 and 2.0, this has **no impact** on cluster operation. The |morpheus| Agent manages its own quorum independently of Corosync's quorate state. Corosync is used only to provide a node list to DLM, not for quorum decisions.

**When this occurs:**

- One or more nodes are down or a network partition has occurred
- This is expected in two-node clusters and stretch cluster configurations where Corosync alone cannot achieve majority quorum

**Resolution:**

- No action is required for the Corosync quorate state itself
- Check the |morpheus| Agent quorum endpoint (``curl -k https://localhost:7443/quorum``) to verify the actual cluster quorum status
- If the Agent quorum also shows issues, investigate network connectivity between hosts and ensure the |morpheus| Agent is running on all nodes

APD Activated
^^^^^^^^^^^^^

**Symptoms:** VMs on shared storage have been shut down automatically.

**Resolution:**

#. Restore storage connectivity to the affected host
#. Restart the affected VMs once storage is confirmed healthy
#. Investigate the root cause of the storage path failure

.. _hvm-stretch-guest-io-errors:

Guest I/O Errors After Stretch Site Failover
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Symptoms:** After a site or array node failure, some VMs on the surviving Hosts report I/O errors even though storage paths remain available:

- Running processes fail with ``Bus error`` (``SIGBUS``, exit code 135)
- Commands such as ``sudo`` or ``dmesg`` return ``Input/output error``
- A guest filesystem becomes read-only or shuts down. On XFS this appears as:

  .. code-block:: text

     XFS (vdb): log I/O error -5
     XFS (vdb): Log I/O error ... Shutting down filesystem

- After a guest-only reboot, the disk may fail to mount (``can't read superblock``) and ``xfs_repair`` fails with ``Input/output error``. This reflects stuck lock state on the Host, not on-disk corruption.

On the Host running the VM, the kernel log shows DLM lock errors shortly after DLM recovery completes:

.. code-block:: bash

   journalctl -k | grep -E 'validate_lock_args -22|lm_lock ret -22'

**Cause:** A GFS2/DLM defect in the HVM OS 24.04 kernel (6.8). After DLM recovery, GFS2 issues downward lock conversions with the ``QUECVT`` flag, DLM rejects them with ``EINVAL``, and GFS2 returns ``EIO`` to QEMU, which passes it to the guest. Any guest filesystem can be affected; XFS makes the failure most visible because it shuts down immediately on a log write error. No data loss has been identified.

The upstream fix (kernel commit ``b6900ce15191``, Linux 6.13) is included in the HVM OS 26.04 kernel and is believed to resolve the issue. Validation is pending.

The issue is intermittent. It has been observed mainly on clusters at or near the maximum of 50 GFS2 datastores under heavy write load. Keep stretch clusters at or below the recommended 25 GFS2 datastores (see :doc:`maximums`).

**Resolution:** A guest-only reboot does not clear the condition. Clear the GFS2 lock state on the Host:

#. Shut down the affected VMs.
#. Place the Host in maintenance mode: ``Infrastructure > Servers > [Host] > Actions > Enter Maintenance``.
#. Reboot the Host (recommended), or unmount and remount the affected GFS2 datastore.
#. Confirm the Host has rejoined the cluster and the datastore is mounted, then exit maintenance mode.
#. Optionally, run ``qemu-img check`` against the powered-off virtual disks to confirm the images are not corrupt.
#. Start the affected VMs and confirm the guest filesystems mount and replay their journals cleanly.

.. WARNING:: Do not run ``xfs_repair -L`` in the guest before the Host has been recovered. Zeroing the log can discard valid metadata.

Agent Not Sending Quorum
^^^^^^^^^^^^^^^^^^^^^^^^

**Symptoms:** Agent is running but quorum data is not being sent or received.

**Resolution:**

#. Check ``/opt/morpheus-node/.quorum-nodes`` exists and contains valid cluster member data
#. Verify agent connectivity to other cluster hosts on port 7443
#. Restart the agent if configuration is correct but quorum is not functioning

Emergency Procedures
--------------------

.. WARNING:: Emergency procedures should only be used when automated recovery has failed. Incorrect use can lead to data loss or split-brain conditions.

For a failed host that must be permanently removed, use the UI workflow and stop conditions in :doc:`managing_hosts`. Do not substitute shell-level cluster removal commands.

Force Rejoin a Fenced Node
^^^^^^^^^^^^^^^^^^^^^^^^^^

Reboot the fenced node. Upon boot, the agent will re-enter the quorum cycle and rejoin the cluster when healthy.

Clear Stuck DLM
^^^^^^^^^^^^^^^

Do not clear DLM fencing manually as a general recovery procedure. If the Agent does not resolve the fence after the failed host is demonstrably isolated, contact HPE Support with the collected quorum, DLM, storage-path, and Agent evidence.

Force Restart Cluster Services
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   systemctl restart corosync
   systemctl restart dlm

.. WARNING:: Use extreme caution restarting cluster services in production. This will temporarily disrupt cluster communication and may trigger fencing of the restarted node.
