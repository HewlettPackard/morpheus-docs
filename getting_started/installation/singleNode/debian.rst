Single Node Install on Debian/Ubuntu
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To get started installing |morpheus| on Ubuntu or Debian a few preparatory items should be addressed first.

#. First make sure the apt repository is up to date by running ``sudo apt-get update``. It is advisable to verify the assigned hostname of the machine is self-resolvable.

   .. IMPORTANT:: If the machine is unable to resolve its own hostname ``nslookup hostname`` some installation commands will be unable to verify service health during installation and fail.

 #. Next simply download the relevant ``.deb`` package for installation. This package can be acquired from https://myenterpriselicense.hpe.com downloads section.

   .. TIP:: You can get a ``curl`` command from My Software Center to directly download the package to your appliance server.

#. Next we must install the package onto the machine and configure the morpheus services:

   .. code-block:: bash

     sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
     sudo morpheus-ctl reconfigure


#. Once the installation is complete the web interface will automatically start up. By default it will be resolvable at ``https://your_machine_name`` and in many cases this may not be resolvable from your browser. The url can be changed by editing ``/etc/morpheus/morpheus.rb`` and changing the value of ``appliance_url``. After this has been changed simply run:

   .. code-block:: bash

     sudo morpheus-ctl reconfigure
     sudo morpheus-ctl stop morpheus-ui
     sudo morpheus-ctl start morpheus-ui

   Open (or refresh) the appliance URL in your browser and wait for the UI loading screen to finish. The ``morpheus-ui`` service can take a few minutes to become available. Only if the UI does not load or the loading screen stalls, run ``morpheus-ctl tail morpheus-ui`` or inspect ``/var/log/morpheus/morpheus-ui/current``.

There are additional install settings that can be viewed in the :ref:`additional_options` section.

Once the browser is pointed to the appliance a first time setup wizard will be presented. Please follow the on screen instructions by creating the master account. From there you will be presented with the license settings page where a license can be applied for use (if a license is required you may request one or purchase one by contacting your sales representative).

More details on setting up infrastructure can be found throughout this guide.

.. TIP:: If any issues occur it may be prudent to check the morpheus log for details at ``/var/log/morpheus/morpheus-ui/current``.
