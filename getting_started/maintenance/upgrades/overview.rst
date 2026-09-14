Upgrading Overview
^^^^^^^^^^^^^^^^^^

Use this page for package sources, backup expectations, and shared requirements before you run a topology-specific upgrade. Then open the procedure that matches your deployment from :doc:`/getting_started/maintenance/upgrading`.

|morpheus| Packages
...................

|morpheus| release packages are obtained from `My HPE Software Center <https://myenterpriselicense.hpe.com>`_.

Upgrade Requirements
....................

.. warning::

   |morpheus| |morphver| contains new node and VM node packages that require about 3.5 GB of storage. After package installation and before reconfigure, you may clean old packages when free space is needed::

      sudo rm -Rf /var/opt/morpheus/package-repos/*

.. important::

   **Back up your database before the upgrade.** Use an appliance backup job in |morpheus|, download the backup, then upgrade. Keep a tested restore path if you need to roll back.

* For firewall, proxy, and ACL rules, Appliance, Supplemental, and Agent packages are served from ``https://downloads.morpheusdata.com`` (previously ``https://downloads.gomorpheus.com``). Allow the current domain when downloads are required.

* Confirm you meet the minimum upgrade version and rolling vs non-rolling rules for |morphver|: upgrades from **below** |minRollingUpgradeVer| require the non-rolling (downtime) procedure; rolling is supported from |minRollingUpgradeVer| or higher. See :doc:`/release_notes/compatibility` and the current release notes.

* Appliances moving from embedded Elasticsearch to OpenSearch (8.1.0+) should review :doc:`/getting_started/maintenance/opensearch_migration` when that migration applies to your path.

Next steps
..........

- **Manager VM / AIO or single-node:** :ref:`singleUpgrade`
- **3-Node HA:** :doc:`3node/overview`
- **Full HA:** :doc:`fullha/overview`
- **HVM after Manager upgrade:** :doc:`hvm_clusters`
