VM Advanced Options
===================

When provisioning or reconfiguring HVM virtual machines, the **Advanced Options** section provides controls for firmware, boot behavior, security, graphics, and virtualization settings. These options are found on the CONFIGURE tab during provisioning or through the :guilabel:`Actions` > :guilabel:`Reconfigure` menu on an existing VM.

.. NOTE:: Most Advanced Options are available at both provisioning and :guilabel:`Actions` > :guilabel:`Reconfigure`. A few remain provision-only or reconfigure-only; see the availability table. Changing chipset, firmware, boot, TPM, nested virtualization, graphics, QEMU arguments, or identity options on an existing VM is a cold reconfigure. You do not need to power the VM off first; |morpheus| powers it off if the change requires a restart, regenerates the configuration, and starts it again.

Changing Advanced Options on an existing VM
-------------------------------------------

#. Navigate to |ProIns| (or the HVM cluster VM detail page).
#. Open :guilabel:`Actions` > :guilabel:`Reconfigure`.
#. Expand **Advanced Options** and change Hardware Compatibility, UEFI, Secure Boot, BIOS Host Mode, Network Boot, TPM, QEMU Arguments, Nested Virtualization, Disable Emulated Graphics, Asset Tag, or the other reconfigure-capable fields in the table below.
#. Apply the reconfigure. If the change cannot be applied while the VM is running, |morpheus| powers the VM off, regenerates the definition, and starts it.

Hardware / Chipset
------------------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - Hardware Compatibility
     - Modern
      - Selects the emulated machine chipset type. **Modern** uses the q35 chipset, which supports PCIe and is recommended for most workloads. **Legacy** uses the i440fx chipset for guest operating systems that do not support VirtIO drivers (e.g., Windows XP, older Linux distributions). Changing this on an existing VM is a cold reconfigure (the VM is powered off if it is running) and can change disk and NIC device models (VirtIO on q35; IDE/e1000 on i440fx).

Firmware & Boot
---------------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - UEFI
     - Off
     - Enables UEFI firmware instead of legacy BIOS. Required for Secure Boot and recommended for modern operating systems. Toggling UEFI on an existing VM is a cold reconfigure; confirm the guest disk can boot the new firmware before applying. The VM is powered off automatically if it is running.
   * - Secure Boot
     - Off
     - Enables UEFI Secure Boot, which verifies the integrity of the boot chain. Requires UEFI. Enabling Secure Boot without UEFI is rejected. You can enable both in the same reconfigure.
   * - BIOS Host Mode
     - Off
     - Passes the physical host's SMBIOS/DMI data directly into the guest VM instead of using emulated values. When disabled (default), the guest sees the virtualized identity (manufacturer "Morpheus", product "MVM", and a generated serial number). When enabled, the guest sees the actual physical server's SMBIOS data (manufacturer, product name, serial number, BIOS version, etc.) as if it were running directly on the hardware. This is required for software that validates hardware identity through SMBIOS, such as certain enterprise licenses tied to physical serial numbers or hardware-aware monitoring agents.
   * - Network Boot
     - Off
     - Enables PXE/network boot for the VM. When enabled, the primary network interface is boot order 1; the root disk and any attached ISO devices follow it in boot order. The selected VM network must reach the DHCP/PXE services used by your environment.
   * - Boot to BIOS/UEFI
     - Enabled (9.1.0 and later)
     - Enabled by default for VMs provisioned on 9.1.0 and later. VMs provisioned on earlier versions remain disabled until this option is enabled. When enabled, the VM boots directly into the BIOS/UEFI firmware setup menu on every restart until this option is disabled. Useful for troubleshooting boot issues or changing firmware settings. *Reconfigure only.*
   * - Boot Delay (seconds)
     - 3
     - The number of seconds the boot menu waits before auto-selecting the default boot entry. Valid range is 1-30 seconds. Only visible when Boot to BIOS/UEFI is enabled. Available under Provisioning > Advanced Options and Reconfigure > Advanced Options.

Security
--------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - TPM
     - Off
     - Enables an emulated Trusted Platform Module (TPM) device for the VM using software TPM (swtpm). Required for Windows 11 and other operating systems that mandate TPM 2.0. TPM state is stored on shared storage and is automatically recovered during VM failover.

QEMU & Virtualization
---------------------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - QEMU Arguments
     - (empty)
     - Custom QEMU command-line arguments stored on the HVM VM and tokenized into individual arguments when the libvirt/QEMU definition is generated. Available during provisioning and reconfiguration. Use only arguments validated for the QEMU version on the HVM hosts.
   * - Enable Nested Virtualization
     - Off
      - Exposes VMX/SVM CPU flags to the guest, allowing it to run its own hypervisor inside the VM. Required for use cases such as running Docker with hardware virtualization, nested KVM, or Hyper-V inside a VM. Changing this on an existing VM is a cold reconfigure; the VM is powered off if it is running. Host Passthrough with nested virtualization enabled marks the VM non-migratable; see CPU Compatibility and Migration.

.. WARNING:: Use QEMU Arguments with caution. Invalid arguments can prevent the VM from starting. Consult KVM/QEMU documentation for valid options.

CPU Model (Cluster-Level Setting)
----------------------------------

The **CPU Model** determines how the host's CPU is presented to guest VMs. This is a **cluster-level** setting configured when creating or editing the cluster, and applies to all VMs in the cluster.

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Mode
     - Description
   * - Host Passthrough (default)
     - Exposes the exact host CPU model and all its features directly to the guest. Provides maximum performance and access to all CPU instructions (AES-NI, AVX-512, etc.). VMs see the real CPU model name.
   * - Host Model
     - Uses a CPU model that closely matches the host but is defined by the hypervisor's CPU compatibility library. Provides good performance while allowing limited heterogeneity between cluster hosts.
   * - Named Model
     - Specifies an exact CPU model name (e.g., ``Skylake-Server``, ``EPYC``). The guest sees only features defined by that model, regardless of what the host actually supports. Maximum portability.

CPU Compatibility and Migration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The default is **Host Passthrough**. For a VM without nested virtualization, |morpheus| marks this CPU definition as migratable. This flag does not establish that every pair of different physical CPU models is compatible; validate live migration between every host model admitted to the cluster.

A **Named Model** is emitted with exact model matching. Select a model that every source and destination host supports. The product implementation does not automatically select a universal "lowest" CPU model, so do not infer a model from processor age or marketing generation alone.

When nested virtualization is enabled with Host Passthrough, |morpheus| marks the CPU non-migratable so VMX/SVM features remain exposed. Treat that VM as ineligible for live migration.

See :doc:`vm_migration` for the mixed-host preflight checks.

ISO and Network Installation
----------------------------

For an interactive ISO installation, add an ISO Virtual Image as described in :doc:`/library/virtual_images/virtual_images`, provision the HVM VM from that image, and complete the guest installer from the console. After installation, eject the installation media and restart the VM so it boots from its installed disk.

For PXE/network installation, enable **Network Boot** and select a primary VM network that can reach the environment's DHCP and PXE services. The primary NIC is placed first in the boot order. After installation, disable **Network Boot** through :guilabel:`Actions` > :guilabel:`Reconfigure`; the root disk then returns to boot order 1.

.. important::

   VM network boot consumes an existing PXE service from a virtual NIC. It is not the |morpheus| bare-metal discovery and provisioning workflow documented under :doc:`/infrastructure/pxeboot/pxeboot`.

Drivers & Graphics
------------------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - Attach VirtIO Drivers
     - Off
     - Attaches a VirtIO drivers ISO to the VM as a secondary CD-ROM device. Enable this when provisioning Windows guests from an ISO to install VirtIO storage and network drivers during OS setup. See :doc:`guest_os_notes` for the installation procedure. *Provision only.*
   * - Disable Emulated Graphics
     - Off
     - Disables the emulated video/graphics adapter. Use this for headless VMs that do not require console access, such as dedicated appliances or GPU-passthrough workloads where the physical GPU provides the display.

Memory
------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - Huge Pages
     - Off
     - Enables huge pages (2 MB) memory backing for the VM. Huge pages reduce TLB (Translation Lookaside Buffer) misses and improve memory access performance for memory-intensive workloads such as databases and HPC applications. The host must have sufficient free huge pages allocated. *Provision only.*
   * - vNUMA Mode
     - Off
     - Controls virtual NUMA topology for the VM. See :doc:`vm_compute` for detailed documentation on vNUMA modes (Off, Auto, Manual).

Identity
--------

.. list-table::
   :widths: 20 15 65
   :header-rows: 1

   * - Option
     - Default
     - Description
   * - Asset Tag
     - (VM name)
     - Sets the SMBIOS asset tag visible to the guest operating system. If left blank, the VM name is used. This value can be read inside the guest using tools such as ``dmidecode`` and is useful for inventory tracking and automation scripts that need to identify the VM.

Option Availability Summary
---------------------------

.. list-table::
   :widths: 40 15 15
   :header-rows: 1

   * - Option
     - Provision
     - Reconfigure
   * - Hardware Compatibility
     - Yes
     - Yes
   * - UEFI
     - Yes
     - Yes
   * - Secure Boot
     - Yes
     - Yes
   * - BIOS Host Mode
     - Yes
     - Yes
   * - Network Boot
     - Yes
     - Yes
   * - Boot to BIOS/UEFI
     - No
     - Yes
   * - Boot Delay (seconds)
     - Yes
     - Yes
   * - TPM
     - Yes
     - Yes
   * - QEMU Arguments
     - Yes
     - Yes
   * - Nested Virtualization
     - Yes
     - Yes
   * - Attach VirtIO Drivers
     - Yes
     - No
   * - Disable Emulated Graphics
     - Yes
     - Yes
   * - Huge Pages
     - Yes
     - No
   * - vNUMA Mode
     - Yes
     - Yes
   * - Asset Tag
     - Yes
     - Yes
