3-Node HA Upgrade
^^^^^^^^^^^^^^^^^

Current 3-Node HA appliances use three application nodes with RabbitMQ and Elasticsearch services clustered across those nodes. The Transactional Database Tier remains outside the application nodes; all three application nodes use the same external MySQL cluster or supported MySQL service. Do not convert this topology to co-located MySQL as part of an upgrade.

|morpheus| Packages
```````````````````
|morpheus| Release Package urls can be obtained from `https://myenterpriselicense.hpe.com <https://myenterpriselicense.hpe.com>`_

Refer to :ref:`compatibility` for any 3-node variations using externalized MySQL, Elasticsearch and/or RabbitMQ version requirements.

Upgrade Instructions
````````````````````

.. toctree::
   :maxdepth: 3

   deb.rst
   rpm.rst

.. WARNING:: Upgrades can add additional storage load on database nodes. Please refer to database storage requirements within :ref:`3nodeinstall` before upgrading.
