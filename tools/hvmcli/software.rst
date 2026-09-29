software
--------

Inspect installed software packages and manage OS updates.

Commands
````````

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Command
     - Description
   * - ``list``
     - List installed versions for qemu, libvirt, kernel, and hvmcli
   * - ``update``
     - Manage package and OS updates

software list
`````````````

Show installed versions of key HVM components.

.. code-block:: bash

   sudo hvmcli software list

software update
```````````````

Manage OS and package updates with pre-checks, rollback support, and history tracking.

**List available updates:**

.. code-block:: bash

   sudo hvmcli software update list

**Run pre-check before updating:**

.. code-block:: bash

   sudo hvmcli software update precheck
   sudo hvmcli software update precheck --interactive

**Check pre-check status:**

.. code-block:: bash

   sudo hvmcli software update precheck status

**Perform OS update:**

.. code-block:: bash

   sudo hvmcli software update os
   sudo hvmcli software update os --force
   sudo hvmcli software update os --interactive

Options:

- ``--force`` — Skip confirmation and proceed with update
- ``--interactive`` — Run in interactive mode with progress display

**Perform an offline (dark-site/air-gapped) OS update:**

On hosts without repository access, stage an offline update bundle and apply it with ``--offline --file``:

.. code-block:: bash

   sudo hvmcli software update os --offline --file /tmp/hvm-os-update-bundle.zip --dry-run
   sudo hvmcli software update os --offline --file /tmp/hvm-os-update-bundle.zip --force

Options:

- ``--offline`` — Update from a local bundle instead of a repository (requires ``--file``)
- ``--file <bundle.zip>`` — Path to the offline update bundle
- ``--dry-run`` — Simulate the update without making changes (offline updates only)
- ``--allow-unsigned`` — Accept a bundle that is not cryptographically signed
- ``--force`` — Skip confirmation prompts
- ``--json`` — JSON output (requires ``--dry-run`` or ``--force``, since interactive prompts cannot produce clean JSON)

**Check update status:**

.. code-block:: bash

   sudo hvmcli software update status

**View update history:**

.. code-block:: bash

   sudo hvmcli software update history
   sudo hvmcli software update history --count 5
   sudo hvmcli software update history --id 2026-04-07T12-15-25Z

Options:

- ``--count <n>`` — Limit to last N entries
- ``--id <timestamp>`` — Show details for a specific update

**Rollback an update:**

.. code-block:: bash

   sudo hvmcli software update rollback --dry-run
   sudo hvmcli software update rollback

Options:

- ``--dry-run`` — Preview what would be rolled back without making changes
- ``--force`` — Skip confirmation prompts
- ``--json`` — JSON output (requires ``--dry-run``, since interactive rollback cannot produce clean JSON)

**Roll back using an offline (dark-site/air-gapped) bundle:**

If the host has no repository access, source the rollback packages from the previous offline update bundle instead:

.. code-block:: bash

   sudo hvmcli software update rollback --offline --file /tmp/previous-hvm-os-update-bundle.zip --dry-run
   sudo hvmcli software update rollback --offline --file /tmp/previous-hvm-os-update-bundle.zip --force

- ``--offline`` — Roll back from a local bundle instead of a repository (requires ``--file``)
- ``--file <previous_bundle.zip>`` — Path to the previous offline update bundle

**Install a specific package:**

.. code-block:: bash

   sudo hvmcli software update package install --file /tmp/package.deb

Options:

- ``--file <path>`` — Path to the .deb package file to install

.. important:: Always run ``precheck`` before performing an OS update. The pre-check validates that the host is in a safe state for updates (no running VMs, cluster quorum intact, sufficient disk space).
