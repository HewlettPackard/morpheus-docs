tui
---

.. important:: The ``tui`` namespace is only available on **HVM OS Ubuntu 26.04+** with **Cluster Layout 2.0**. On HVM OS Ubuntu 24.04, this namespace is hidden; the separate ``hpe-vm`` TUI owns host networking and deployment on that release instead.

Launch the interactive, full-screen terminal user interface for managing the host without needing to know individual ``hvmcli`` commands.

.. code-block:: bash

   sudo hvmcli tui

.. note:: The TUI must be launched with ``sudo``. It refuses to start when run as a non-root user, since it performs privileged operations while rendering and cannot show a mid-screen ``sudo`` password prompt.

TUI Main Menu
`````````````

The main menu provides navigation to the following areas:

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Menu item
     - Description
   * - Host Information
     - Summary of host hardware, OS, and configuration
   * - Virtual Switch
     - Create, edit, and inspect Virtual Switches
   * - Virtual Machines
     - List and manage VMs on the host
   * - Keyboard Layout / TimeZone
     - Configure console keymap and system timezone
   * - Install VME Manager
     - Guided deployment of the VME Manager VM
   * - Install VME Worker
     - Guided deployment of a VME Worker VM
   * - Exit
     - Close the TUI and return to the shell

.. tip:: The TUI is useful for initial host bring-up or troubleshooting from the console when the Morpheus UI is not yet reachable. For scripting and automation, use the individual ``hvmcli`` namespace commands instead.
