version
-------

Show the installed ``hvmcli`` version and build information.

.. code-block:: bash

   sudo hvmcli version

.. code-block:: text

   hvmcli 1.0.0 (build 20260719)

**JSON output:**

.. code-block:: bash

   sudo hvmcli version --json

.. code-block:: json

   {
     "errorCode": 0,
     "messages": "",
     "data": {
       "name": "hvmcli",
       "version": "1.0.0",
       "build": "20260719"
     }
   }

Options:

- ``--json`` — JSON output
