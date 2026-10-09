HPE VM Essentials Maximums
==========================

This page is the canonical limits reference for HPE VM Essentials (VME) in this documentation release. It intentionally distinguishes published product constraints from sizing recommendations and values that HPE has not published. A UI input range, hypervisor theoretical limit, hardware capability, or successful lab test is not a supported VME maximum.

How to Use This Guide
---------------------

- Treat a numeric value as a product constraint only when the table labels it **Published limit**.
- Treat recommendations as design guidance, not as a guarantee that a workload will fit or perform.
- For every **Not published** value, contact HPE with the Manager version, HVM OS and cluster layout, hardware inventory, storage backend, network design, and expected workload before committing to a design.
- Provider and hardware limits can be lower than a VME limit. The lowest applicable limit governs.

Published Constraints
---------------------

.. list-table::
   :header-rows: 1
   :widths: 30 20 20 30

   * - Area
     - Value
     - Classification
     - Scope and qualification
   * - Virtual Switches per cluster
     - 8
     - Published limit
     - HVM layout 2.0. The default ``vs0`` counts toward the total. See :doc:`virtual_switches`.
   * - LUN-per-vDisk volumes per cluster
     - Approximately 800
     - Operational threshold
     - Alletra LUN-per-vDisk mode. This is a performance/management threshold, not a general GFS2 vDisk maximum. See :ref:`hvm-lun-per-vdisk-limits`.
   * - GFS2 datastores per cluster
     - 50
     - Published limit
     - HPE Shared File System (GFS2) datastores in a single HVM cluster.
   * - GFS2 datastores per stretch cluster
     - 25
     - Recommendation
     - Stretch clusters with Site Groups. Fewer datastores reduce DLM lock recovery load during site failover. See :doc:`stretch_clusters` and :ref:`hvm-stretch-guest-io-errors`.

Limits Not Published
--------------------

The following requested maxima are not published in the evidence maintained with this documentation. Contact HPE; do not derive values from libvirt/QEMU defaults, form validation, or underlying server and storage specifications.

.. list-table::
   :header-rows: 1
   :widths: 35 25 40

   * - Category
     - Published value
     - Planning note
   * - Physical CPUs or cores per host
     - Not published; contact HPE
     - Supply the server model, CPU model, and HVM OS/layout.
   * - VMs per host or cluster
     - Not published; contact HPE
     - Density depends on workload reservations, failure headroom, and hardware.
   * - Hosts per cluster
     - Not published; contact HPE
     - Include the HA, quorum, and storage design.
   * - Concurrent live migrations per host or cluster
     - Not published; contact HPE
     - The configurable migration setting is not a certified platform maximum.
   * - Disks per datastore
     - Not published; contact HPE
     - Include transport, array, multipath, and filesystem details.
   * - vCPUs, memory, disks, total disk capacity, or vNICs per VM
     - Not published; contact HPE
     - Plan and UI ranges are not support limits.
   * - Virtual disk or datastore size
     - Not published; contact HPE
     - Array, filesystem, image format, and migration constraints also apply.
   * - Networks per cluster
     - Not published; contact HPE
     - The Virtual Switch limit does not define a network-object maximum.
   * - VM or network throughput
     - Not published; contact HPE
     - Validate against the complete physical and virtual data path.
   * - Maximum VM size for live migration
     - Not published; contact HPE
     - CPU compatibility, memory change rate, bandwidth, and attached devices affect feasibility.
   * - Managed hosts, VMs, or total objects per Manager
     - Not published; contact HPE
     - Use a workload-specific Manager sizing review.
   * - UI or API scale and request-rate maximums
     - Not published; contact HPE
     - No generic object or request-rate ceiling is published here.
   * - BIOS/UEFI, TPM, nested virtualization, or device-passthrough scale
     - Not published; contact HPE
     - These are capability/configuration areas, not one shared numeric limit.

Capacity Is Not a Maximum
-------------------------

Use :doc:`capacity_planning` to reserve host-failure and maintenance headroom. Its formulas and recommendations help size a deployment but do not establish support maxima. Before a large deployment or migration, have HPE review the exact versioned design rather than extrapolating from examples.
