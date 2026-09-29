HVMCLI
======

.. versionadded:: 9.1

``hvmcli`` is the command-line interface for managing HPE HVM hypervisor hosts. It provides a comprehensive set of tools for managing virtual machines, networking, storage, hardware inspection, cluster operations, and system maintenance directly from the host console.

``hvmcli`` is pre-installed on layout 2.0 HVM hosts and requires ``sudo`` privileges to execute most commands.

.. important:: The HVM cluster workflows in this reference apply to layout 2.0 Hosts running HVM OS 26.04. Legacy and layout 1.3 clusters use their layout-specific Linux, OVS, and cluster commands.

.. toctree::
   :maxdepth: 2

   getting_started
   version
   vm
   node
   interfaces
   virtswitch
   network
   storage
   cluster
   hardware
   software
   logs
   health
   top
   security
   deploy
   tui
