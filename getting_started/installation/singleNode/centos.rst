Single Node Install on CentOS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. note:: Appliance Package links are available at https://myenterpriselicense.hpe.com in the downloads section.

Quick Install
`````````````
#. Install the Appliance package and run ``sudo morpheus-ctl reconfigure``.

That is it. After the reconfigure completes, |morpheus| will start and be available at ``https://your_machine_name`` in a minute or few.

Step-by-step Install Instructions
`````````````````````````````````

#. Ensure the |morpheus| Appliance host meets the minimum :ref:`Requirements`

#. Download the target version ``.rpm`` package for installation in a directory of your choosing. The package can be removed after successful installation.

   .. code-block:: bash

    wget https://downloads.morpheusdata.com/path/to/morpheus-appliance-$version.rpm

#. Validate the package checksum matches source checksums. For example:

   .. code-block:: bash

    sha256sum morpheus-appliance-$version.rpm

#. Next install the rpm package

   .. code-block:: bash

    sudo rpm -ihv morpheus-appliance-x.x.x-1.x86_64.rpm

#. By default the appliance_url uses the machines hostname, ie ``https://your_machine_name``. The default url can be changed by editing ``/etc/morpheus/morpheus.rb`` and changing the value of ``appliance_url``. Additional Appliance configuration options are available below.

   .. toggle-header::
        :header: Appliance Configuration Options **Click to Expand/Hide**

              .. include:: /getting_started/additional/morpheusRb.rst

#. After all configuration options have been set, run:

   .. code-block:: bash

     sudo morpheus-ctl reconfigure

   .. note:: Configuration options can be updated after the initial reconfigure by editing ``/etc/morpheus/morpheus.rb`` and running ``sudo morpheus-ctl reconfigure`` again. Appliance and other services may need to be restarted depending on configuration changes.

#. Once the installation is complete the ``morpheus-ui`` service starts automatically. Open the appliance URL in your browser, refresh if needed, and wait for the UI loading screen to finish. When the first-time setup wizard (or login page) appears, the UI is ready.

   Only if the UI does not become available or the loading screen stalls, inspect the application log::

      morpheus-ctl tail morpheus-ui

   or open ``/var/log/morpheus/morpheus-ui/current``. Successful startup typically shows the |morpheus| ascii logo with the install version and start time.

There are additional install settings that can be viewed in the :ref:`additional_options` section.

Once the browser is pointed to the appliance a first time setup wizard will be presented. Please follow the on screen instructions by creating the master account. From there you will be presented with the license settings page where a license can be applied for use (if a license is required you may request one or purchase one by contacting your sales representative).

More details on setting up infrastructure can be found throughout this guide.

.. TIP:: If any issues occur it may be prudent to check the morpheus log for details at ``/var/log/morpheus/morpheus-ui/current``.

..   todo: add detailed reconfigure process steps link
