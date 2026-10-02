Storage Lifecycle
=================

This section covers Day-2 storage operations on layout 1.3 and 2.0 HVM clusters, including adding and removing datastores, managing iSCSI targets, and expanding storage capacity.

Datastore Groups
----------------

Datastore Groups provide automated storage placement and optional Storage DRS rebalancing for HVM clusters. A Datastore Group is a logical datastore type that contains multiple shared, file-based datastores. It does not create another filesystem or libvirt storage pool.

Supported Members
^^^^^^^^^^^^^^^^^

A Datastore Group can contain these HVM datastore types:

- **NFS Datastore**
- **HPE Clustered Datastore (Shared LUN)**, which provides a shared GFS2 filesystem

Members must be active datastores in the same HVM cluster. Local datastores, LUN-per-vDisk datastores, inactive datastores, nested Datastore Groups, and datastores already assigned to another group are not offered for selection.

.. important::

   Datastore Groups and the Storage DRS behavior described here are specific to HVM clusters. Do not use this procedure for other cluster or Cloud types.

Service Provider Storage Tiers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

MSPs and service providers can use Datastore Groups to present storage service tiers without exposing the underlying datastore inventory. For example, create groups named **Gold**, **Premium**, or **Standard**, and assign datastores with the corresponding media, performance, protection, or operational characteristics.

Grant a Tenant or Group access to the Datastore Group while withholding access to its member datastores. The consumer selects the service-tier group during provisioning, and |morpheus| can still resolve that group to an eligible member datastore. The consumer does not need direct permission to each member selected behind the group.

To prevent datastore capacity from appearing in provisioning selectors, enable **Hide Datastore Stats On Selection** under :menuselection:`Administration --> Settings --> Provisioning`. Without this setting, users may see capacity information for datastore choices they are permitted to select.

.. note::

   A tier name is an administrative service definition, not an automatic quality-of-service guarantee. Keep each group's members operationally equivalent for the advertised tier, and enforce any IOPS, throughput, availability, encryption, or data-protection commitments through the underlying storage platform and service policy. Datastore Group placement uses capacity utilization; it does not benchmark media or validate service-level performance.

Creating a Datastore Group
^^^^^^^^^^^^^^^^^^^^^^^^^^

Users require **Infrastructure: Clusters** and **Infrastructure: Storage** permissions at the **Full** level.

#. Navigate to :menuselection:`Infrastructure --> Clusters` and open the HVM cluster.
#. Select the :guilabel:`Datastores` tab.
#. Click :guilabel:`Add`.
#. Enter a :guilabel:`Name` and select :guilabel:`Datastore Group` as the :guilabel:`Type`.
#. Configure the group:

   .. list-table::
      :widths: 25 75
      :header-rows: 1

      * - Field
        - Description
      * - Member Datastores
        - Select one or more eligible shared file-based datastores in this HVM cluster. A member can belong to only one Datastore Group.
      * - Space Threshold (%)
        - Utilization that marks a member as over threshold for Storage DRS evaluation. The allowed range is 70–95%; the default is 85%. A member must be strictly above the threshold before it is treated as overutilized.
      * - Automation Level
        - **Fully Automated** queues eligible storage migrations. **No Automation (recommendations only)** creates recommendations without moving disks. The default is **Fully Automated**.

#. Save the Datastore Group.

#. If the group is a consumer-facing storage tier, edit its datastore permissions to grant the intended Groups or Tenants access. Keep the member datastores private when their names and capacity should remain hidden.

The group reports aggregate capacity and free space from its members. The datastore type cannot be changed after creation. Edit the group to change its members, threshold, or active state.

Provisioning with a Datastore Group
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Select the Datastore Group as the datastore for a VM disk during provisioning. Before creating the disk, |morpheus| resolves the logical group to an active, online member that allows provisioning and has enough capacity.

Within the selected group, |morpheus| chooses the member with the lowest projected utilization after placing the disk. This normally favors the least-full member, which can differ from the datastore with the greatest number of free bytes when members have different capacities. The selected member's capacity is reserved while provisioning proceeds so concurrent requests do not all select the same free space.

The configured **Space Threshold (%)** does not reject initial placement. It controls post-provision Storage DRS evaluation. Initial placement can use a member above that threshold if the requested disk still fits.

When :guilabel:`Auto - Cluster` is available and selected instead of a named group, |morpheus| evaluates accessible Datastore Groups in the HVM cluster. It resolves the best member in each group, then selects the resulting member with the greatest free space.

Automatic Storage Rebalancing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Storage DRS runs after a successful HVM cluster refresh. It evaluates each Datastore Group whose status is provisioned or warning:

#. Members strictly above the configured threshold are treated as sources.
#. |morpheus| identifies eligible VM disks that can be moved away from each source.
#. A target must have enough capacity and remain at or below the threshold after receiving the disk.
#. The target closest to the group's overall utilization is preferred.
#. With **Fully Automated**, persistent imbalance queues storage migrations. With **No Automation**, |morpheus| creates a Storage DRS recommendation instead.

Automatic mode requires the same source datastore to remain a migration candidate across consecutive evaluation cycles before migrations are queued. This avoids moving disks because of a single transient capacity reading. Only one Storage DRS cycle runs for an HVM cluster at a time, and a group is skipped while a member contains a VM already being resized.

Eligible Disks and Limitations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Storage DRS considers only active, provisioned VM disks with reported usage. It excludes:

- Disks with snapshots
- Multi-attach or shared disks
- CD-ROM and ISO volumes
- Volumes without a positive size or reported usage
- Volumes that are already participating in a resize operation

Storage DRS moves individual eligible disks rather than entire datastores. Each migration is executed through the VM resize workflow and is visible as a separate process.

.. warning::

   Before placing a member datastore into maintenance, remove it from the Datastore Group or disable provisioning on it. Member-level placement and Storage DRS eligibility do not currently exclude a datastore solely because its maintenance status changed.

Monitor every member's capacity and health independently. A Datastore Group is not a storage-redundancy mechanism and does not replace array protection, backups, multipathing, datastore maintenance procedures, or capacity planning.

Storage Design Boundaries
-------------------------

|morpheus| manages supported datastore and host operations but does not replace storage-array design. Customers remain responsible for array sizing, LUN presentation, zoning, target configuration, multipath policy, network loss/latency analysis, data protection, and vendor interoperability. Present shared block storage consistently to every cluster host and verify stable device identity and all expected paths before datastore creation or returning a host to service.

For Ethernet storage, separate traffic or provide sufficient redundant capacity when the failure analysis requires predictable storage behavior. Configure jumbo frames only across a validated end-to-end path. For Fibre Channel and iSCSI, test loss of each individual path and confirm the multipath device remains available. A successfully discovered device is not evidence that the design is redundant or adequately sized.

Datastore Details and File Explorer
-----------------------------------

Open an HVM datastore from either :menuselection:`Infrastructure --> Storage --> Data Stores` or the HVM Cluster's :guilabel:`Datastores` tab. The detail page provides Summary, Volumes, Virtual Machines, History, and, when supported, Files tabs. Use the Volumes and Virtual Machines tabs to assess placement before maintenance, migration, or removal.

The :guilabel:`Files` tab provides Datastore Explorer for shared, file-based HVM datastores with a configured path, principally NFS and HPE Clustered Datastores (GFS2). It requires an online HVM Host that can access the datastore.

- **Read access** — With **Infrastructure: Storage** and **Infrastructure: Storage Browser** set to **Read**, users can navigate directories, search the current directory, and download files.
- **Full access** — With both permissions set to **Full**, users can also upload files and recursively delete files or directories. Cluster-scoped access additionally requires **Infrastructure: Clusters** permission, and write operations require datastore ownership.

Datastore Explorer does not create empty directories or rename, move, copy, or edit files. It is not available for local, block, RBD, LUN-per-vDisk, or cloud-scoped datastores. Upload and delete are blocked during datastore maintenance; browsing and download remain available.

.. warning::

   Datastore Explorer operates directly on the shared filesystem. Do not modify or delete VM disks, snapshot backing files, image artifacts, heartbeat data, active process files, or unknown datastore content. Use supported VM, snapshot, and datastore actions for managed artifacts.

Adding a New Datastore
-----------------------

New HPE Clustered Datastores (Shared LUNs) can be added to a running cluster without downtime.

Prerequisites
^^^^^^^^^^^^^

- A shared storage LUN is provisioned and accessible to all cluster hosts via iSCSI or Fibre Channel
- The iSCSI target for the LUN has been added to the cluster (see `Adding iSCSI Targets`_ below)
- All hosts can see the block device (verify with ``lsblk`` on each host)

Procedure
^^^^^^^^^

#. Navigate to :menuselection:`Infrastructure --> Storage --> Data Stores` and click :guilabel:`Add`. Alternatively, open the HVM Cluster's :guilabel:`Datastores` tab and click :guilabel:`Add`.
#. Select :guilabel:`HPE Clustered Datastore (Shared LUN)` as the type.
#. When using the global Data Stores page, select the Cloud and target HVM Cluster.
#. Configure the datastore:

   .. list-table::
      :widths: 30 70
      :header-rows: 1

      * - Field
        - Description
      * - Name
        - Descriptive name for the datastore
      * - Cluster
        - Select the target HVM cluster
      * - Block Device
        - The stable shared block device path. For multipath storage, use the WWN-based path, such as ``/dev/mapper/3<wwn>``; do not use a positional ``mpathX`` name.
      * - Heartbeat Target
        - Enable if this datastore should be used for heartbeat writes

#. Click :guilabel:`Save`

What Happens Automatically
^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a new HPE Clustered Datastore is created:

#. **GFS2 formatting** — ``mkfs.gfs2`` is run with lock_dlm protocol, 2048 resource groups, and 32 initial journals, with the lock table tied to the cluster
#. **Journal provisioning** — Journals are added (``gfs2_jadd``) if the initial journal count is less than the number of hosts
#. **Mount on all hosts** — The filesystem is mounted on each cluster host. Mount information is persisted to ``/opt/morpheus-node/.mounts``
#. **libvirt storage pool** — A virsh storage pool is created on each host, making the datastore available for VM disk placement
#. **Lock protocol** — The ``lock_dlm`` protocol coordinates file locking across all hosts through the existing DLM infrastructure

Adding iSCSI Targets
---------------------

iSCSI targets provide the block device access that underpins HPE Clustered Datastores.

Procedure
^^^^^^^^^

#. Navigate to ``Infrastructure > Clusters > [Cluster] > Storage > iSCSI``
#. Click :guilabel:`Add`
#. Enter the iSCSI target details:

   .. list-table::
      :widths: 30 70
      :header-rows: 1

      * - Field
        - Description
      * - Target IP
        - IP address of the iSCSI target portal
      * - Target Port
        - Port number (default: 3260)

#. Click :guilabel:`Save`

What Happens Automatically
^^^^^^^^^^^^^^^^^^^^^^^^^^^

When an iSCSI target is added:

#. **Discovery** — ``iscsiadm -m discovery -t st -p "<IP>:<port>"`` is run on all online cluster hosts
#. **Auto-login** — Sessions are configured for automatic startup and all available targets are logged in (``iscsiadm -m node --loginall=automatic``)
#. **Persistence** — Target connections persist across host reboots

.. NOTE:: iSCSI targets are automatically discovered on all hosts in the cluster, not just a single host. This ensures all hosts can access the shared storage simultaneously.

Removing iSCSI Targets
------------------------

.. WARNING:: Before removing an iSCSI target, ensure no datastores are using LUNs from that target. Removing a target with active datastores will cause storage failures.

Procedure
^^^^^^^^^

#. Migrate all VMs off datastores using the target's LUNs
#. Remove the datastore from |morpheus| (see `Removing a Datastore`_ below)
#. Navigate to ``Infrastructure > Clusters > [Cluster] > Storage > iSCSI``
#. Select the target to remove
#. Click :guilabel:`Remove`

What Happens Automatically
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The iSCSI discovery database entry is removed from all online cluster hosts using ``iscsiadm -m discoverydb -t sendtargets -p "<IP>:<port>" -o delete``.

Expanding Storage Capacity
--------------------------

To add additional capacity to an existing cluster:

#. Provision new LUNs on your storage array
#. Add iSCSI targets (if new portals) as described above
#. Rescan storage on every HVM Host using the storage-vendor and HVM-release-approved procedure. Morpheus 9.1.0 does not expose a datastore-level :guilabel:`Grow Filesystem` action.
#. Verify the new LUN is visible on the cluster's Storage tab
#. Create a new HPE Clustered Datastore using the new block device

Adding a new datastore is the lower-risk way to add capacity because it does not change the block device beneath an existing clustered filesystem.

Growing an Existing GFS2 Datastore
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use this procedure only for an HPE Clustered Datastore whose storage array supports online LUN expansion and after HPE Support or the storage owner confirms the exact HVM release, transport, device stack, and commands. The operation crosses the array/LUN, every host's paths, the shared block device, and GFS2. A size mismatch or use of the wrong device can affect every VM on the datastore.

Morpheus 9.1.0 does not provide a UI or API action to grow a datastore filesystem. Expanding the backing LUN does not automatically increase the mounted GFS2 filesystem. The :guilabel:`Grow Filesystem` workflow tracked by MORPH-15316 is planned for Morpheus 9.2.0 and must not be assumed available in 9.1.0.

.. warning:: Create and verify workload/application backups before the maintenance window. A VM snapshot on the datastore being grown is not an independent backup. Pause provisioning, migration, snapshots, backups, and other storage-changing jobs. Use one named coordinator; do not run the grow command concurrently from multiple hosts.

Preflight and stop conditions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Record the datastore name, mount point, filesystem ID, stable WWN-based multipath device, current array/LUN size, ``lsblk`` size, and ``df`` size. Confirm the device is the same on every host and is not a partition, LVM logical volume, or raw VM disk unless the approved runbook explicitly covers that stack.
#. Confirm cluster quorum, DLM lockspaces, datastore mounts, and all storage paths are healthy. Resolve withdrawn GFS2, failed paths, duplicate WWNs, or inconsistent device mappings before proceeding.
#. Confirm the array expansion is non-destructive, cannot shrink the LUN, preserves the LUN identity/WWN and host-set exports, and is visible to all cluster hosts. Take the array backup or recovery point required by the storage owner.
#. Schedule a maintenance window and identify a rollback/stop plan. LUN expansion and ``gfs2_grow`` are forward-only operations; restoring the previous size is not a normal rollback.

Stop and contact HPE Support before changing storage if the stable device cannot be proven, any host reports a different size or path set, the device stack includes an undocumented partition/LVM layer, the filesystem is withdrawn, or backups are not verified.

Coordinated growth sequence
~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. From the array, expand the existing LUN without changing its WWN or exports. Do not create a new LUN and present it under the old device identity.
#. Rescan SCSI/FC or iSCSI on **every** cluster host using the storage-vendor and HVM-release-approved method. Verify every host sees the new underlying path size.
#. Resize or reload the multipath map using the approved method. Verify the same WWN-based ``/dev/mapper/3<wwn>`` device reports the new size on every host and all expected paths remain active. Do not run ``gfs2_grow`` while hosts disagree.
#. If—and only if—the approved device stack contains a partition or LVM layer, extend that layer using its approved procedure and verify the resulting GFS2 block device on every host. Do not infer a partition/LVM command from the device name.
#. On the single designated coordinator host, confirm the intended GFS2 filesystem is mounted, then run the filesystem grow against the **mount point**:

   .. code-block:: bash

      sudo gfs2_grow <gfs2-mount-point>

   Do not run ``gfs2_grow`` on the raw block-device path or from multiple hosts.
#. Verify the new filesystem size with ``df -hT <gfs2-mount-point>`` on every host. Confirm the datastore remains mounted and not withdrawn, DLM and quorum remain healthy, multipath retains all expected paths, and the |morpheus| datastore capacity updates after cluster refresh.
#. Resume paused jobs in stages and monitor kernel, DLM, Agent, and storage-array events.

Stop without repeating commands if a rescan loses paths, multipath retains the old size, hosts report different sizes, ``gfs2_grow`` returns an error, GFS2 withdraws, or the UI capacity does not agree with ``df`` after refresh. Preserve command output and logs and contact HPE Support. Do not use ``fsck.gfs2``, recreate the filesystem, unmount it cluster-wide, or attempt to shrink the LUN as an improvised recovery.

Datastore Maintenance and Evacuation
------------------------------------

Datastore maintenance mode prevents new provisioning and evacuates eligible VM volumes before a datastore is retired, replaced, or serviced. This is separate from Host maintenance mode; see :doc:`host_maintenance` for evacuating an HVM Host.

Prerequisites
^^^^^^^^^^^^^

- **Infrastructure: Clusters** and **Infrastructure: Storage** permissions at the **Full** level
- Healthy source and destination storage with sufficient capacity
- No conflicting VM resize or storage migration operations
- Removal of VM snapshots for workloads that must be migrated
- A verified backup and recovery plan appropriate for the workloads

If the datastore is a member of a Datastore Group, remove it from the group or disable provisioning before maintenance. A member's maintenance state alone does not currently remove it from Datastore Group placement or Storage DRS target selection.

Enter Maintenance Mode
^^^^^^^^^^^^^^^^^^^^^^

#. Navigate to :menuselection:`Infrastructure --> Clusters`, open the HVM Cluster, select :guilabel:`Datastores`, and open the datastore.
#. Click :guilabel:`Enter Maintenance`.
#. Review the listed VMs and snapshot warnings.
#. To send all movable VM volumes to one datastore, select it under :guilabel:`Target Datastore`. Leave the field blank to allow |morpheus| to choose destinations automatically.
#. Click :guilabel:`Confirm`. If snapshots are reported, the confirmation changes to :guilabel:`Proceed Anyway`; those snapshot-bearing VMs are still skipped rather than forcibly migrated.

With an explicit target, |morpheus| validates that it is in the same HVM Cluster, active, online, available for provisioning, outside maintenance, and large enough for the movable volumes. A Datastore Group cannot be selected as the maintenance target.

With no explicit target, |morpheus| evaluates active, online datastores of the same type in the HVM Cluster. It places the largest VM storage sets first and can distribute different VMs across different targets. A target must remain below the automatic evacuation utilization limit after placement. If an otherwise movable VM cannot be assigned, maintenance entry stops before migration begins.

Migration Behavior
^^^^^^^^^^^^^^^^^^

Maintenance enters an **Entering** state and queues asynchronous storage migrations through the standard VM resize workflow. It later reaches **Maintenance**, or returns to **Available** if a fatal planning or execution failure prevents entry.

VMs with snapshots cannot be storage-migrated and are reported as skipped. A powered-off VM on local-only storage is also unmovable. Orphaned, infrastructure-owned, multi-attach, raw, or provider-specific volumes can have additional restrictions. Maintenance can complete with a warning when some VMs cannot migrate; review the datastore's Virtual Machines and Volumes tabs and the related processes before treating evacuation as complete.

There is no user-facing cancellation action after maintenance evacuation has been submitted. Do not retry or issue conflicting resize operations while migrations are active.

Leave Maintenance Mode
^^^^^^^^^^^^^^^^^^^^^^

#. Confirm that maintenance work is complete and the datastore is healthy and accessible from every expected HVM Host.
#. Open the cluster-scoped datastore detail page.
#. Click :guilabel:`Leave Maintenance`.
#. Confirm the operation.

The datastore transitions through **Exiting** and returns to **Available**, which re-enables provisioning. Leaving maintenance does not automatically move VMs back to the datastore.

Removing a Datastore
---------------------

.. WARNING:: All managed VM volumes must be evacuated and all remaining files or infrastructure volumes accounted for before removal. Removing a datastore with active or required data can cause data loss.

Procedure
^^^^^^^^^

#. Verify no VMs have disks on the datastore:

   - Navigate to :menuselection:`Infrastructure --> Storage --> Data Stores`, then open the datastore
   - Check the :guilabel:`Virtual Machines` tab
   - Check the :guilabel:`Volumes` tab and, for file-based datastores, inspect :guilabel:`Files` without modifying managed artifacts

#. Use datastore maintenance mode to evacuate eligible VMs, then resolve every skipped or unmovable workload.
#. Navigate to :menuselection:`Infrastructure --> Storage --> Data Stores`
#. Select the datastore to remove
#. Click :guilabel:`Remove`

What Happens
^^^^^^^^^^^^

- The GFS2 filesystem is unmounted on all cluster hosts
- The libvirt storage pool is destroyed on all hosts
- Mount information is removed from ``/opt/morpheus-node/.mounts``
- The datastore record is removed from |morpheus|

Heartbeat Datastore Configuration
-----------------------------------

The heartbeat datastore is used for host health detection. Each host writes ``hb.properties`` to the heartbeat datastore every 20 seconds, and the Designated Coordinator monitors these writes to detect host failures.

Changing the Heartbeat Datastore
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. Navigate to ``Storage > Datastores``
#. Edit the desired datastore's properties
#. Enable the :guilabel:`Heartbeat Target` option
#. Save changes

.. IMPORTANT:: Only one datastore may be configured as the heartbeat target at a time. If multiple datastores are marked as heartbeat targets, |morpheus| will automatically select one (preferring provisioned, non-failing datastores) and clear the heartbeat flag on the others. An alarm is raised when this automatic selection occurs.

Heartbeat Datastore Best Practices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Use a highly reliable datastore as the heartbeat target
- Ensure the heartbeat datastore is accessible from all cluster hosts
- If the heartbeat datastore fails, VM failover detection is impaired until a new heartbeat target is configured
- Monitor the heartbeat datastore's health in the Quorum panel (``Infrastructure > Clusters > [Cluster] > Summary``)

Datastore Health Monitoring
----------------------------

|morpheus| continuously monitors datastore health:

- **Mount status** — Each host's agent reports mount status (MOUNTED, WARNING, FAILED)
- **GFS2 withdrawal** — If the GFS2 filesystem enters a withdrawn state (due to journal errors or storage issues), the datastore location status is updated to ``warning`` or ``failed``
- **virsh pool status** — If a libvirt storage pool is missing on a host, |morpheus| recreates it automatically during the next sync cycle

Datastore Status Indicators
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Status
     - Description
   * - Provisioned
     - Datastore is healthy and operational on all hosts
   * - Warning
     - One or more hosts report mount issues; investigate the affected host(s)
   * - Failed
     - All hosts report mount failures; datastore is not operational
   * - Provisioning
     - Datastore is being created or mounted

For troubleshooting datastore issues, see :doc:`troubleshooting`.

Fibre Channel Configuration
-----------------------------

HVM clusters support Fibre Channel (FC) as a storage transport for HPE Clustered Datastores in addition to iSCSI. FC provides lower latency and higher throughput than iSCSI and is the preferred choice for production workloads.

Host Requirements
^^^^^^^^^^^^^^^^^^

Each HVM host that will access FC storage must have:

- One or more Fibre Channel HBA (Host Bus Adapter) ports connected to the SAN fabric
- Proper zoning configured on the SAN switch to allow the host HBA WWPNs to communicate with the storage array ports
- ``multipathd`` running (installed automatically during HVM cluster provisioning)

On layout 2.0 hosts, verify multipath before creating a datastore and after any path change:

.. code-block:: bash

   sudo hvmcli storage multipath validate
   sudo hvmcli storage multipath status
   sudo hvmcli storage fc --list
   sudo hvmcli storage fc --multipath

For iSCSI, use ``sudo hvmcli storage iscsi --list`` and ``sudo hvmcli storage iscsi --multipath``. A healthy device reports its expected paths as active with no failed path. During a single-path failure, the multipath device and its WWN-based mapper name must remain available through another active path. If the multipath device disappears or all paths fail, stop datastore creation or host return-to-service and restore storage connectivity.

FC targets do not need to be manually added in the |morpheus| UI (unlike iSCSI). When a storage plugin provisions a LUN and exports it to the cluster hosts' WWPNs, the hosts automatically discover the new device after a SCSI rescan.

How FC Storage Works with GFS2
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. The storage plugin (e.g., HPE Alletra MP) creates a LUN on the array and exports it to a **host-set** containing the FC WWPNs of all cluster hosts
#. A SCSI rescan is triggered on each host to discover the new block device
#. The block device is identified by its **WWN** through multipath — the device path is always ``/dev/mapper/3<volumeWwn>`` (WWN-stable, not positional ``/dev/mapper/mpathX``)
#. GFS2 filesystem is formatted on the device and mounted on all cluster hosts simultaneously
#. The device is registered as a virsh storage pool for VM provisioning

.. IMPORTANT:: Device paths in HVM always use WWN-based multipath names (``/dev/mapper/3<wwn>``), never positional names like ``/dev/mapper/mpathX``. Positional names differ across hosts and would cause mount failures on other cluster members.

NVMe over TCP
--------------

HVM clusters support **NVMe over TCP (NVMe/TCP)** as a storage transport for HPE Clustered Datastores. NVMe/TCP provides significantly lower latency and higher IOPS than iSCSI while using the same standard Ethernet infrastructure — no specialized HBAs or SAN fabric required.

.. NOTE:: NVMe/TCP support was validated in Morpheus 9.0.0 with the removal of Pacemaker. The |morpheus| Agent-based quorum system is fully compatible with NVMe/TCP-backed GFS2 datastores.

Host Requirements
^^^^^^^^^^^^^^^^^^

Each HVM host must have:

- Linux kernel 5.15+ (included in the HVM OS versions used by layouts 1.3 and 2.0)
- The ``nvme-tcp`` kernel module loaded
- The ``nvme-cli`` package installed (for ``nvme connect`` and discovery)
- Network connectivity to the NVMe/TCP target on the configured port (default: 4420)

Connecting NVMe/TCP Targets
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

NVMe/TCP targets must be connected on **all cluster hosts** before creating a GFS2 datastore. On each host:

#. Discover available subsystems:

   .. code-block:: bash

      sudo nvme discover -t tcp -a <target-ip> -s <port>

#. Connect to the target subsystem:

   .. code-block:: bash

      sudo nvme connect -t tcp -a <target-ip> -s <port> -n <subsystem-nqn>

#. Verify the NVMe namespace is visible:

   .. code-block:: bash

      sudo nvme list

#. Confirm the block device appears (e.g., ``/dev/nvme0n1``):

   .. code-block:: bash

      lsblk

For **persistent connections** that survive host reboots, create a discovery controller entry or use ``/etc/nvme/discovery.conf``:

.. code-block:: bash

   echo "-t tcp -a <target-ip> -s <port>" | sudo tee -a /etc/nvme/discovery.conf
   sudo systemctl enable --now nvme-connect@.service

.. NOTE:: Unlike iSCSI, NVMe/TCP does not use multipath device-mapper (``/dev/mapper/``). NVMe namespaces appear as ``/dev/nvmeXnY`` devices. For multipath with multiple paths to the same namespace, use the native NVMe multipath (``/sys/module/nvme_core/parameters/multipath`` set to ``Y``), which presents a single ``/dev/nvmeXnY`` device aggregating all paths.

Creating a GFS2 Datastore on NVMe/TCP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once the NVMe namespace is visible on all cluster hosts:

#. Navigate to the cluster detail page > Storage tab
#. Click :guilabel:`+ Add` to create a new HPE Clustered Datastore
#. Select the NVMe block device (e.g., ``/dev/nvme0n1``) as the block device
#. Complete the datastore creation as normal

GFS2 treats the NVMe namespace identically to any other block device — formatting, mounting, and DLM locking work the same as with iSCSI or FC-backed datastores.

Performance Considerations
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 25 25 25 25
   :header-rows: 1

   * - Transport
     - Latency
     - Infrastructure
     - Best For
   * - iSCSI
     - Higher (~100-200μs)
     - Standard Ethernet
     - General workloads, legacy arrays
   * - Fibre Channel
     - Low (~50-100μs)
     - Dedicated SAN fabric + HBAs
     - Enterprise production, existing FC infrastructure
   * - NVMe/TCP
     - Lowest (~30-80μs)
     - Standard Ethernet (25GbE+ recommended)
     - Latency-sensitive workloads, all-flash arrays, new deployments

Pluggable Storage Backend
--------------------------

The HVM storage layer is **pluggable** — storage array management is handled by dedicated plugins rather than being built into the platform. This allows |morpheus| to support different storage backends without modifications to the core cluster code.

HPE Alletra MP Plugin
^^^^^^^^^^^^^^^^^^^^^^

The primary storage plugin for HVM clusters is the **HPE Alletra Block Storage** plugin. This plugin manages the full lifecycle of LUNs on HPE Alletra MP arrays:

- Creates and deletes LUNs (volumes) via Alletra REST APIs
- Exports LUNs to cluster hosts via host-sets (FC WWPNs or iSCSI IQNs)
- Handles LUN discovery, multipath device resolution, and online resize
- Supports snapshots, clones, and synchronous replication (remote copy groups)
- Generates storage-specific support bundle diagnostics

LUN-Per-vDisk Mode
^^^^^^^^^^^^^^^^^^^

In addition to the standard model where VMs store their virtual disks as files on a GFS2 shared filesystem, the Alletra MP plugin supports a **LUN-per-vDisk** mode where each virtual disk is backed by its own dedicated LUN on the storage array.

**Use cases:**

- Raw Device Mapping (RDM) workloads requiring direct block device access
- High-performance VMs that need dedicated I/O paths without filesystem overhead
- Applications that require consistent low-latency storage (databases, real-time analytics)

**How it works:**

- When a VM disk is created with the Alletra storage provider selected, the plugin provisions an individual LUN on the array
- The LUN is exported to **all hosts in the cluster** (not just the host currently running the VM) to support live migration and failover
- The block device is attached directly to the VM via its WWN-based multipath path

**Performance limits and considerations:**

.. WARNING:: Limit LUN-per-vDisk usage to approximately **800 vDisks per cluster**. Beyond this threshold, multipath management overhead saturates and storage performance degrades significantly. This limit applies to the total number of LUN-per-vDisk volumes across the entire cluster, regardless of cluster size.

- Because LUNs are exported to all hosts for failover support, the per-cluster limit is independent of the number of hosts — adding more hosts does not increase the maximum
- Use LUN-per-vDisk selectively for workloads that genuinely require it, not as a default for all VMs
- Standard GFS2-backed datastores are recommended for the majority of workloads and have no practical per-cluster vDisk limit
- Monitor the storage array's host-set export count and multipath device count on each host

.. list-table::
   :widths: 30 35 35
   :header-rows: 1

   * - Storage Mode
     - Best For
     - Considerations
   * - GFS2 Shared Datastore
     - General workloads, templates, most VMs
     - No per-vDisk limit, shared filesystem overhead, standard performance
   * - LUN-per-vDisk (Alletra)
     - RDM, high-performance VMs, dedicated I/O
     - ~800 vDisk cluster limit, higher array management overhead, direct block performance

Raw Device Block Mapping (RDBM)
-------------------------------

Raw Device Block Mapping (RDBM) allows attaching a raw host block device directly to an HVM virtual machine without an intermediate virtual disk file or clustered filesystem layer. This is analogous to VMware's Raw Device Mapping (RDM) feature and is ideal for performance-sensitive workloads, direct SCSI passthrough, or cluster applications requiring shared storage access.

When an RDBM volume is attached, the system creates a ``<disk type="block" device="disk">`` entry in the VM's libvirt configuration, with the host device path as the source.

Prerequisites
^^^^^^^^^^^^^

- The raw block device must be visible on the HVM host
- The device must not be part of an existing datastore or clustered filesystem
- A stable device path is recommended (e.g., ``/dev/disk/by-id/wwn-0x...``)

Attaching an RDBM Volume
^^^^^^^^^^^^^^^^^^^^^^^^^

#. Navigate to the HVM cluster detail page (:menuselection:`Infrastructure --> Clusters` > select cluster)
#. Select the **Storage Volumes** tab
#. Locate the raw block device volume to attach
#. Click :guilabel:`Attach` on the volume row
#. Select the target VM from the prompt
#. Confirm the attachment

   .. NOTE:: Volumes that are already backing a Datastore are excluded from the available list.

Hot-attach is supported for running VMs without requiring a reboot.

Enabling support for SCSI-3 Persistent Reservations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SCSI-3 Persistent Reservations (PR) are required for certain cluster-aware applications to coordinate access to shared storage. To enable SCSI-3 PR for an RDBM volume:

#. Ensure the underlying storage supports SCSI-3 PR
#. Navigate to the Instance with the mapped disk
#. Make sure the VM is powered off
#. Click the :guilabel:`Actions` dropdown and select :guilabel:`Reconfigure`
#. Click on the `+` sign to the right of the *Storage Controllers* section
#. Select **SCSI VirtIO** as the storage controller type in the dropdown
#. On the disk row, select **SCSI X:Y** to attach the disk to the newly created SCSI VirtIO controller
#. Check the **Persistent Reservations** checkbox under the disk
#. Save the changes

Detaching an RDBM Volume
^^^^^^^^^^^^^^^^^^^^^^^^^

RDBM volumes can be detached from a VM in two ways:

**From the Cluster Storage Volumes tab:**

#. Navigate to the HVM cluster detail page (:menuselection:`Infrastructure --> Clusters` > select cluster)
#. Select the **Storage Volumes** tab
#. Locate the attached RDBM volume
#. Click :guilabel:`Detach` on the volume row
#. Confirm the detachment

**From the VM Reconfigure action:**

#. Navigate to the VM detail page
#. Click the :guilabel:`Actions` dropdown and select :guilabel:`Reconfigure`
#. Remove the RDBM volume from the disk configuration
#. Save the reconfiguration

Hot-detach is supported for running VMs without requiring a reboot.

Limitations
^^^^^^^^^^^

.. list-table::
   :widths: 30 70
   :header-rows: 1

   * - Limitation
     - Description
   * - Host Pinning
     - VMs with RDBM volumes are pinned to the current host. Dynamic Placement will not migrate these VMs.
   * - No Cross-Host Migration
     - Even if the same SAN LUN is visible on multiple hosts, RDBM VMs cannot be live migrated in this release.
   * - Maintenance Mode
     - RDBM VMs are skipped during host maintenance mode evacuation with a warning. They must be manually shut down or moved.
   * - Move Operations Blocked
     - Placement move and manage operations are blocked for VMs with RDBM volumes, returning a clear error message.

.. NOTE:: Volumes with ``diskType=block`` that **do** have a Datastore association (such as Alletra shared LUNs) remain moveable and are not subject to these limitations. Only raw block devices without a Datastore association are treated as pinned RDBM volumes.
