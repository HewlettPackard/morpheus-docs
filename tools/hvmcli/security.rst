security
--------

Security compliance operations including FIPS 140 mode management.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``fips``
     - Manage FIPS 140 mode (enable, disable, status)

security fips
`````````````

Manage Federal Information Processing Standards (FIPS) 140 compliance mode.

**Check FIPS status:**

.. code-block:: bash

   sudo hvmcli security fips status

.. code-block:: text

   FIPS Status
   ───────────────────────────────────
     Kernel FIPS mode:    ❌ DISABLED
     FIPS kernel package: not installed
     GRUB fips=1:         not configured

     Packages:
       ✗ ubuntu-fips — not-installed
       ✗ linux-fips — not-installed
       ✗ fips-initramfs — not-installed

.. code-block:: bash

   sudo hvmcli security fips status --json

**Enable FIPS mode (dry run):**

Preview what changes would be made without applying them:

.. code-block:: bash

   sudo hvmcli security fips enable --dry-run

**Enable FIPS mode:**

.. code-block:: bash

   sudo hvmcli security fips enable --force
   sudo hvmcli security fips enable --force --reboot

Options:

- ``--force`` — Skip confirmation prompts
- ``--reboot`` — Automatically reboot the node after enabling FIPS (required for kernel-level FIPS activation)
- ``--dry-run`` — Preview changes without applying

**Recover from an invalidated FIPS payload (offline enable):**

If an OS update invalidates the on-disk FIPS payload, re-stage it from an offline bundle before re-enabling FIPS. ``--offline`` and ``--file`` are only valid with ``enable`` and must be used together:

.. code-block:: bash

   sudo hvmcli security fips enable --offline --file /path/to/fips_bundle.zip --force

Options:

- ``--offline`` — Re-stage the FIPS payload from an offline bundle before enabling; requires ``--file``
- ``--file <bundle.zip>`` — Path to the offline bundle carrying the FIPS payload; use with ``--offline``

**Disable FIPS mode (dry run):**

.. code-block:: bash

   sudo hvmcli security fips disable --dry-run

**Disable FIPS mode:**

.. code-block:: bash

   sudo hvmcli security fips disable --force --reboot

Options:

- ``--force`` — Skip confirmation prompts
- ``--reboot`` — Automatically reboot after disabling FIPS
- ``--dry-run`` — Preview changes without applying

.. important:: Enabling or disabling FIPS mode requires a reboot for the kernel FIPS mode to take effect. Use ``--reboot`` to handle this automatically, or plan a manual reboot.

.. note:: FIPS 140 mode enforces the use of FIPS-validated cryptographic modules for all system-level encryption operations. This is required for compliance with certain government and regulatory standards.
