Full HA Debian / Ubuntu Upgrade
```````````````````````````````

The following covers upgrading the |morpheus| App nodes in Full HA Architecture configurations to |morphver|.

.. important:: The following is only for Full HA Architecture configurations, where MySQL, Elasticsearch and RabbitMQ services are external to the App nodes. The following steps assume 3 app nodes, adjust accordingly.

|morpheus| Packages
...................
|morpheus| Release Package urls can be obtained from `https://myenterpriselicense.hpe.com <https://myenterpriselicense.hpe.com>`_

Below |minRollingUpgradeVer| -> |morphver| (non-rolling)
.......................................................

.. important::

   From any release **below** |minRollingUpgradeVer|, use this non-rolling procedure. Stop ``morpheus-ui`` on all app nodes first; the upgrade requires downtime. Rolling upgrades apply only when every app node is already on |minRollingUpgradeVer| or higher.

.. important:: It is important to stop the morpheus-ui service on all app nodes prior to upgrade. Failure to do so will result in a flood of log errors due to previous message serialization conflict. The messages will eventually expire and the logs will clear.

.. warning:: |morpheus| |morphver| contains new node and VM node packages that require 3.5GB of storage. It is safe to run ``sudo rm -Rf /var/opt/morpheus/package-repos/*`` after |morphver| package installation and before reconfigure to clean old node and vm node packages from the package-repo when room is needed.

#. Starting with App Node 3, on **All** App Nodes, stop the morpheus-ui services via ``morpheus-ctl stop morpheus-ui``. If you receive a timeout, run ``morpheus-ctl graceful-kill morpheus-ui``.

   .. code-block:: bash

    [root@app-server-3 ~]# morpheus-ctl stop morpheus-ui

   .. code-block:: bash

    [root@app-server-2 ~]# morpheus-ctl stop morpheus-ui

   .. code-block:: bash

    [root@app-server-1 ~]# morpheus-ctl stop morpheus-ui

#. Upgrade the deb package on App Node 1, then run a Reconfigure on App Node 1

   .. code-block:: bash

    [root@app-server-1 ~]# sudo wget https://packageUrl.morpheus-appliance_x.x.x-x_amd64.deb
    [root@app-server-1 ~]# sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
    [root@app-server-1 ~]# sudo morpheus-ctl stop morpheus-ui
    [root@app-server-1 ~]# sudo morpheus-ctl reconfigure
    [root@app-server-1 ~]# sudo morpheus-ctl start morpheus-ui

   After the reconfigure has succeeded, refresh your browser and wait for the UI loading screen to complete. Only if the UI does not become available or the loading screen stalls, tail the logs with ``morpheus-ctl tail morpheus-ui``.

#. Once App Node 1 upgrade has completed and the ui is available, upgrade the deb package on App Node 2, then run a Reconfigure on App Node 2.

   .. code-block:: bash

    [root@app-server-2 ~]# sudo wget https://packageUrl.morpheus-appliance_x.x.x-x_amd64.deb
    [root@app-server-2 ~]# sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
    [root@app-server-1 ~]# sudo morpheus-ctl stop morpheus-ui
    [root@app-server-1 ~]# sudo morpheus-ctl reconfigure
    [root@app-server-1 ~]# sudo morpheus-ctl start morpheus-ui

    After the reconfigure has succeeded, refresh your browser and wait for the UI loading screen to complete. Only if the UI does not become available or the loading screen stalls, tail the logs with ``morpheus-ctl tail morpheus-ui``.

#. Once App Node 2 upgrade has completed and the ui is available, upgrade the deb package on App Node 3, and run a Reconfigure on App Node 3

   .. code-block:: bash

    [root@app-server-3 ~]# sudo wget https://packageUrl.morpheus-appliance_x.x.x-x_amd64.deb
    [root@app-server-3 ~]# sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
    [root@app-server-3 ~]# sudo morpheus-ctl reconfigure

#. The upgrade is complete and the |morpheus|-ui services should be running on the three allocated nodes.

.. important:: If reconfigure after a rpm package upgrade stalls or hangs on starting a local service it is possible the ``morpheus-runsvdir`` service did not start or a process it was managing was manually shutdown or killed. To resolve, run ``systemctl stop morpheus-runsvdir`` then ``systemctl start morpheus-runsvdir``, then run reconfigure again, ``morpheus-ctl reconfigure``.

|

|minRollingUpgradeVer| or higher -> |morphver| (rolling)
.......................................................

.. NOTE:: Rolling upgrades (no full UI outage) are supported only when every app node is already on |minRollingUpgradeVer| or higher before upgrading to |morphver|. From any earlier version, use the non-rolling procedure above — that path requires downtime.

.. warning:: |morpheus| |morphver| contains new node and VM node packages that require 3.5GB of storage. It is safe to run ``sudo rm -Rf /var/opt/morpheus/package-repos/*`` after |morphver| package installation and before reconfigure to clean old node and vm node packages from the package-repo when room is needed.

#. Starting with App Node 3, on **All** App Nodes, stop the morpheus-ui services via ``morpheus-ctl stop morpheus-ui``. If you receive a timeout, run ``morpheus-ctl graceful-kill morpheus-ui``.

#. Upgrade the deb package on App Node 1, then run a Reconfigure on App Node 1

   .. code-block:: bash

    [root@app-server-1 ~]# sudo wget https://packageUrl.morpheus-appliance_x.x.x-x_amd64.deb
    [root@app-server-1 ~]# sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
    [root@app-server-1 ~]# sudo morpheus-ctl stop morpheus-ui
    [root@app-server-1 ~]# sudo morpheus-ctl reconfigure
    [root@app-server-1 ~]# sudo morpheus-ctl start morpheus-ui

   After the reconfigure has succeeded, refresh your browser and wait for the UI loading screen to complete before proceeding to the next node. Only if the UI does not become available or the loading screen stalls, tail the logs with ``morpheus-ctl tail morpheus-ui``.

#. Once App Node 1 upgrade has completed and the ui is available, upgrade the deb package on App Node 2, then run a Reconfigure on App Node 2.

   .. code-block:: bash

    [root@app-server-2 ~]# sudo wget https://packageUrl.morpheus-appliance_x.x.x-x_amd64.deb
    [root@app-server-2 ~]# sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
    [root@app-server-2 ~]# sudo morpheus-ctl stop morpheus-ui
    [root@app-server-2 ~]# sudo morpheus-ctl reconfigure
    [root@app-server-2 ~]# sudo morpheus-ctl start morpheus-ui

   After the reconfigure has succeeded, refresh your browser and wait for the UI loading screen to complete before proceeding to the next node. Only if the UI does not become available or the loading screen stalls, tail the logs with ``morpheus-ctl tail morpheus-ui``.

#. Once App Node 2 upgrade has completed and the ui is available, upgrade the deb package on App Node 3, and run a Reconfigure on App Node 3

   .. code-block:: bash

    [root@app-server-3 ~]# sudo wget https://packageUrl.morpheus-appliance_x.x.x-x_amd64.deb
    [root@app-server-3 ~]# sudo dpkg -i morpheus-appliance_x.x.x-1_amd64.deb
    [root@app-server-3 ~]# sudo morpheus-ctl stop morpheus-ui
    [root@app-server-3 ~]# sudo morpheus-ctl reconfigure
    [root@app-server-3 ~]# sudo morpheus-ctl start morpheus-ui

   After the reconfigure has succeeded, refresh your browser and wait for the UI loading screen to complete before proceeding to the next node. Only if the UI does not become available or the loading screen stalls, tail the logs with ``morpheus-ctl tail morpheus-ui``.

#. The upgrade is complete and the |morpheus|-ui services should be running on the three allocated nodes.

.. important:: If reconfigure after a rpm package upgrade stalls or hangs on starting a local service it is possible the ``morpheus-runsvdir`` service did not start or a process it was managing was manually shutdown or killed. To resolve, run ``systemctl stop morpheus-runsvdir`` then ``systemctl start morpheus-runsvdir``, then run reconfigure again, ``morpheus-ctl reconfigure``.
