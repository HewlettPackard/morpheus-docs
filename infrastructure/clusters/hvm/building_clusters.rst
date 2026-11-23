Building New HVM Clusters
=========================

Prerequisites
-------------

Before creating an HVM cluster, identify the target layout and ensure the following requirements are met:

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - Requirement
     - Details
   * - Operating System
     - HVM OS/Ubuntu 24.04 for layout 1.3; HVM OS 26.04 for layout 2.0
   * - Minimum Hosts
     - 3 Hosts for a standard single-site cluster; 2 Hosts are supported with an external Distributed Worker witness when using an HPE Shared File System (GFS2) datastore (see :doc:`two_node_clusters`). 1 Host is also supported when using an HPE Shared File System (GFS2) datastore, but no HA is possible in this scenario.
   * - CPU
     - Hardware virtualization enabled (VT-x/AMD-V)
   * - Memory
     - 32 GB RAM minimum (4 GB reserved for system)
   * - Access
     - Root SSH or passwordless sudo
   * - Network
     - Static IP on management interface
   * - Firewall
     - Port 7443 open between all hosts
   * - Storage
     - Shared storage (iSCSI, FC) accessible by all hosts

Cluster Creation
----------------

#. Navigate to ``Infrastructure > Clusters``
#. Click :guilabel:`+ Add Cluster`
#. Select **HVM** as the cluster type
#. Select the required layout: **HVM 1.3** for HVM OS/Ubuntu 24.04 or **HVM 2.0** for HVM OS 26.04
#. Complete the following fields:

   .. list-table::
      :widths: 30 70
      :header-rows: 1

      * - Field
        - Description
      * - SSH Hosts
        - Comma-separated IP addresses of all hosts
      * - SSH Port
        - SSH port (default: 22)
      * - Username
        - SSH username with root or sudo access
      * - Password/Key
        - Authentication credential
      * - Data Device
        - Data device path (HCI only)
      * - Management Network Name
        - Network name for management traffic
      * - Storage Network Name
        - Network name for storage traffic
      * - Compute Network Name
        - Network name for compute traffic
      * - Overlay Network Name
        - Legacy/layout 1.3 underlay/tunnel network name used by the HVM networking plugin. Verify tunnel-interface reachability and MTU; layout 2.0 uses Virtual Switch networking instead
      * - Compute VLANs
        - VLAN IDs for compute networks
      * - CPU Architecture/Model
        - Processor architecture selection
      * - Witness
        - Distributed Worker witness for a two-node GFS2 cluster or stretch cluster. Configure it after cluster creation. For two-Host GFS2 with no Site Groups, see :doc:`two_node_clusters`. For stretch clusters with Site Groups, see :doc:`stretch_clusters`.

#. Click :guilabel:`Complete` to begin automated provisioning

.. _hvm-cluster-permissions:

Cluster Permissions and Provisioning Impact
-------------------------------------------

From the Clusters list, open :guilabel:`More` > :guilabel:`Permissions` for a cluster to scope where that cluster can be used:

- **Groups** control which Infrastructure Groups can use the cluster as a provisioning target. A user also needs role access to the Group and the relevant provisioning features.
- **Service Plans** limit the plans that can be selected when provisioning to the cluster. For example, selecting only a ``1 CPU, 2 GB`` plan means other plans are not offered after this cluster is selected, subject to the selected Instance layout and image requirements.
- **Default** retains the product's default permission behavior rather than creating an explicit Group or Service Plan allow-list. It does not mean that this cluster is the preferred provisioning target and does not override role, layout, image, or capacity filtering. Use an explicit selection when the cluster must be restricted.

Permission changes affect subsequent wizard choices. They do not resize, move, or otherwise alter existing VMs. After changing permissions, test with a non-administrator role: start an Instance deployment, select the intended Group and HVM target, and confirm that only the allowed clusters and Service Plans are shown. If an expected plan is absent, also check that the plan is active, uses the HVM/KVM provision type, and satisfies the selected layout and Virtual Image minimums.

Automated Provisioning Phases
-----------------------------

Once cluster creation is initiated, |morpheus| executes the following automated phases:

Phase 1: Package Installation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Installs required packages on all hosts:

- KVM/QEMU
- libvirt
- Corosync
- DLM
- GFS2 utilities
- Ceph libraries
- swtpm
- jq
- nfs-common

Phase 2: Host Networking
^^^^^^^^^^^^^^^^^^^^^^^^

Configures the networking model for the selected layout:

- **Layout 1.3:** Creates OVS bridges for management, compute, and overlay networks and configures the libvirt Management network
- **Layout 2.0:** Creates the default Virtual Switch and applies Linux bridge networking through ``hvmcli``

See :doc:`hvm_networks` for layout 1.3 networking or :doc:`virtual_switches` for layout 2.0 networking.

For layout 2.0, complete the scenario checklist in :doc:`/getting_started/installation/hvm_host_prep` before creating Virtual Switches. VLAN IDs are optional: use explicit VLAN IDs with an upstream trunk for a decoupled design without native VLANs, or leave a segment untagged only when the upstream port is intentionally configured for that traffic.

Phase 3: Host Preparation
^^^^^^^^^^^^^^^^^^^^^^^^^

Prepares each host for cluster operation:

- Firewall configuration
- libvirt configuration
- IOMMU enablement
- Hugepages configuration
- Workqueue tuning
- Directory structure creation

Phase 4: Cluster Services
^^^^^^^^^^^^^^^^^^^^^^^^^

Establishes cluster communication and coordination:

- SSH key generation and distribution via Cypher
- Corosync authkey generation and distribution via Cypher
- Corosync configuration with knet transport
- DLM configuration with fencing enabled
- Service ordering and startup (corosync → DLM)

Phase 5: iSCSI/Storage
^^^^^^^^^^^^^^^^^^^^^^^

Configures storage connectivity:

- iSCSI initiator name assignment
- Multipath configuration
- OOM protection for storage services

Phase 6: Storage Pools
^^^^^^^^^^^^^^^^^^^^^^

Creates local storage pools:

- Cloud-init ISO pool
- Local storage pool

Phase 7: Quorum Activation
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Activates the agent-based quorum system:

- Server sends quorumInfo to all agents
- Agents persist cluster member data to ``.quorum-nodes``
- Agents begin peer-to-peer quorum pinging

HPE Clustered Datastore (Shared LUN) Setup
-------------------------------------------

The HPE Clustered Datastore (GFS2) provides shared storage accessible by all cluster hosts simultaneously.

Adding iSCSI Targets
^^^^^^^^^^^^^^^^^^^^^

#. Navigate to ``Infrastructure > Clusters > [Cluster] > Storage > iSCSI``
#. Click :guilabel:`Add`
#. Enter the Target IP and Port (default: 3260)
#. Save the configuration

.. NOTE:: iSCSI targets are auto-discovered and auto-logged-in on all hosts in the cluster.

Creating the Datastore
^^^^^^^^^^^^^^^^^^^^^^

Create the HPE Clustered Datastore from :menuselection:`Infrastructure --> Storage --> Data Stores` or from the HVM Cluster's :guilabel:`Datastores` tab. See :doc:`storage_operations` for the canonical prerequisites, fields, resulting cluster operations, verification, and storage lifecycle procedures.

Configuring Heartbeat Datastore
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. Edit the shared datastore properties
#. Enable the :guilabel:`Heartbeat Target` option

.. IMPORTANT:: Configure one reliable GFS2 datastore as the heartbeat target. If that datastore becomes unhealthy, restore it or select another healthy GFS2 datastore before relying on automatic VM recovery.

Verification
------------

After cluster creation, verify the cluster is healthy:

**Corosync membership:**

.. code-block:: bash

   corosync-quorumtool -l

Confirm all expected compute Hosts are listed. Corosync provides membership to DLM but does not decide Agent quorum for layouts 1.3 and 2.0. Do not use the Corosync ``Quorate`` value as the HVM health decision, especially for two-node and stretch topologies.

**DLM status:**

.. code-block:: bash

   systemctl is-active dlm
   dlm_tool status -v

Confirm all nodes show ``member=1``.

**Agent quorum:**

.. code-block:: bash

   curl -k https://localhost:7443/quorum

**UI verification:**

Navigate to ``Infrastructure > Clusters > [Cluster] > Summary > Quorum`` panel and confirm:

- Quorum status: ACHIEVED
- All nodes: ONLINE
- Coordinator: assigned
- Lockspaces: OK

For a two-node GFS2 cluster, also confirm the Distributed Worker witness is listed and reachable. See :doc:`two_node_clusters`.

Cluster Sizing
--------------

.. list-table::
   :widths: 30 35 35
   :header-rows: 1

   * - Hosts
     - Failure Tolerance
     - Use Case
   * - 3
     - Tolerates 1 host failure
     - Minimum production cluster
   * - 5
     - Tolerates 2 host failures
     - Recommended for higher availability
