Failure Scenarios
=================

This section documents the expected behavior and timelines for common failure scenarios in layout 1.3 and 2.0 HVM clusters.

Scenario 1: Single Host Failure
--------------------------------

A single host becomes unavailable due to hardware failure, OS crash, or power loss.

Timeline
^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - Host stops responding to quorum pings
   * - T+60s
     - Node marked unreachable (3 missed ping intervals)
   * - T+60–90s
     - Designated Coordinator issues ``fence_ack`` for the failed node
   * - T+140s
     - Heartbeat failure threshold reached (7 missed intervals)
   * - T+140s+
     - Coordinator assigns failed host's VMs to surviving hosts by available memory

**Recovery:** Automatic. Fix the underlying host issue and the node will rejoin on reboot.

If the host remains Offline (Unclean) or fenced, use the evidence, isolation, reboot, and validation workflow in :doc:`troubleshooting`. Do not use Pacemaker cleanup commands on layouts 1.3 or 2.0.

Scenario 2: Storage Partition (APD)
------------------------------------

A host loses all paths to shared storage while remaining network-reachable to other cluster nodes.

Timeline
^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - Storage paths fail, heartbeat writes begin failing
   * - ~T+2min
     - 6 consecutive failed heartbeat write cycles
   * - ~T+2min
     - APD protection activates: all non-pinned VMs on shared storage are shut down

**Recovery:** Restore storage connectivity to the affected host, then restart VMs. Investigate the root cause of the storage path failure.

Scenario 3: Network Partition
------------------------------

A network failure splits the cluster into two groups that cannot communicate with each other.

Timeline
^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - Network partition occurs
   * - T+60s
     - Nodes on each side detect unreachable peers
   * - T+60s
     - Majority side: retains quorum, continues operations
   * - T+60s
     - Minority side: loses quorum, self-fences
   * - Recovery
     - When network is restored, fenced nodes reboot and rejoin

**Recovery:** Fix the network issue. Fenced hosts will automatically recover via reboot.

Scenario 4: Stretch Cluster Site Failure
-----------------------------------------

An entire site becomes unavailable in a stretch cluster configuration.

Timeline
^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - All nodes at one site become unreachable
   * - T+60s
     - Site failure detected (all non-witness nodes at site unreachable)
   * - T+60s
     - Surviving site checks witness reachability
    * - T+60s
      - Arbitration: alphabetically first site name wins
    * - T+60s
      - Winner issues ``fence_ack``
    * - T+60s
      - Loser self-fences (stops DLM, Corosync)
    * - T+140s
      - VM failover begins for VMs from the failed site

**Recovery:** Automatic. After the failed site is restored, 3 consecutive healthy cycles (~3 minutes) must pass. Fenced nodes reboot and rejoin. Redistribute VMs across sites after recovery.

Two-Node Cluster Failures
-------------------------

A two-Host GFS2 cluster uses a Distributed Worker witness as its third quorum vote. Majority is 2 of 3. Do not apply stretch alphabetical site-winner logic to these failures. See :doc:`two_node_clusters` for the complete matrix, assignment order, and Worker URL checks.

One Host Down, Witness Reachable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - One Host becomes unreachable
   * - T+60s
     - Surviving Host marks the peer unreachable and retains 2 of 3 votes with the witness
   * - T+140s
     - Heartbeat failure threshold; eligible VMs can recover on the surviving Host

**Recovery:** Restore the failed Host. It rejoins Agent quorum when reachable again.

Witness Down, Both Hosts Reachable
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - Witness becomes unreachable
   * - T+60s
     - Both Hosts retain 2 of 3 votes
   * - Until restored
     - The cluster has no Host-failure tolerance; do not take a Host offline

**Recovery:** Restore witness service before any Host maintenance.

One Host and Witness Down
^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - One Host and the witness become unreachable
   * - T+60s
     - Remaining Host has 1 of 3 votes and cannot retain quorum
   * - T+60s
     - Remaining Host fences to protect GFS2

**Recovery:** Restore either the witness or the failed Host so the surviving Host can form 2 of 3 votes. Do not issue manual fence acknowledgements until the unreachable Host is isolated from shared storage.

Host-to-Host Partition
^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Time
     - Event
   * - T+0s
     - Hosts cannot reach each other
   * - T+60s
     - The Host that can reach the witness forms 2 of 3 votes
   * - T+60s
     - The isolated Host cannot retain quorum and fences

**Recovery:** Restore Host-to-Host connectivity. The isolated Host rejoins after fencing recovery. This is not stretch site arbitration.

Scenario 5: Single Node Reboot
-------------------------------

A single node is rebooted (planned or unplanned).

Fast Reboot (<140 seconds)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the node returns before the heartbeat failure threshold:

- No VM failover occurs
- Node rejoins quorum automatically

Slow Reboot (>140 seconds)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the reboot takes longer than the heartbeat failure threshold:

- VMs are moved to surviving hosts
- On boot, the node enters recovery mode
- Agent re-establishes quorum membership

**Recovery:** Automatic. Node rejoins cluster on boot.

Scenario 6: Layout 1.2 to 1.3 Upgrade
---------------------------------------

Upgrading from HVM layout 1.2 (Pacemaker-based) to layout 1.3 (agent-based quorum).

Process
^^^^^^^

#. Pacemaker services are disabled and removed
#. The |morpheus| agent's QuorumCheckService takes over quorum and fencing responsibilities
#. Corosync and DLM continue to operate (these components are shared between both layouts)

Verification After Upgrade
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Run the following commands to confirm the upgrade completed successfully:

.. code-block:: bash

   corosync-quorumtool -s
   dlm_tool status -v
   curl -k https://localhost:7443/quorum

Confirm:

- Corosync lists all expected compute Hosts
- DLM shows all nodes with ``member=1``
- Agent ``/quorum`` endpoint returns healthy status with all nodes ONLINE

.. NOTE:: Corosync's ``Quorate`` value does not determine Agent quorum for layout 1.3 or later and may be ``No`` in a healthy two-node or stretch configuration.

.. NOTE:: After upgrading, ``pcs`` commands are no longer available or applicable. Use the diagnostic commands listed in :doc:`troubleshooting` for all cluster operations.

Recovery Matrix
---------------

.. list-table::
   :widths: 25 20 55
   :header-rows: 1

   * - Scenario
     - Recovery Type
     - Action Required
   * - Single host failure
     - Automatic
     - Fix host issue; node rejoins on reboot
   * - Storage partition (APD)
     - Manual
     - Restore storage connectivity, restart VMs
   * - Network partition
     - Automatic
     - Fix network; fenced hosts auto-recover via reboot
   * - Stretch site failure
     - Automatic
     - Site restores; redistribute VMs after recovery
   * - DLM stuck
     - Automatic/Support
     - Agent manages fence acknowledgement; contact HPE Support if it remains blocked after isolation is verified
   * - GFS2 withdrawn
     - Manual
     - Unmount/remount or reboot affected host
