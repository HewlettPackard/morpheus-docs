.. _security_zones:

Security Zones
--------------

``Infrastructure > Network > Integrations > (select Security Server)``

Overview
^^^^^^^^

Security zones define logical boundaries within a network security integration that segment traffic and enforce security policies at zone boundaries. Zones are a fundamental concept in firewall architectures where traffic is classified based on its zone membership and policies are applied at inter-zone boundaries.

In |morpheus|, security zones are synced from supported security integrations and can be referenced when configuring firewall rules, security groups, and provisioning workflows.

Zone Concepts
^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Concept
     - Description
   * - Trust Level
     - Zones typically have an associated trust level (e.g., trusted, untrusted, DMZ) that influences default policy behavior
   * - Inter-zone Policy
     - Traffic moving between zones is subject to security policies; traffic within a zone may be implicitly permitted
   * - Zone Membership
     - Network interfaces, segments, or subnets are assigned to zones to classify traffic
   * - Default Policy
     - The action taken on traffic between zones when no explicit rule matches (typically deny)

Viewing Security Zones
^^^^^^^^^^^^^^^^^^^^^^

Security zones are visible within the context of their security server integration:

#. Navigate to ``Infrastructure > Network > Integrations``
#. Select the desired security server integration
#. Zones may be displayed in the **Summary** tab or accessible via a dedicated section depending on the integration type

Zone-Based Firewall Rules
^^^^^^^^^^^^^^^^^^^^^^^^^^

When creating firewall rules on integrations that support zone-based security, zones can be referenced as source or destination qualifiers:

#. Navigate to the **Firewall** tab of the security server integration
#. Create or edit a firewall rule
#. Select the source zone and destination zone for the rule
#. Define the action (allow/deny) and service criteria
#. Save the rule

.. NOTE:: Zone-based rule support varies by integration type. Check the specific integration guide for your security platform for details on zone configuration and management.

Integration-Specific Zone Support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

NSX-T
  NSX-T uses transport zones to define the scope of logical networks. Security groups and distributed firewall rules in NSX-T operate independently of transport zones but can reference zone-aware constructs.

Cisco ACI
  ACI uses the concept of security domains and contexts (VRFs) as zone equivalents, providing multi-tenant isolation.

Required Role Permissions
^^^^^^^^^^^^^^^^^^^^^^^^^

Access to security zone viewing requires the ``Infrastructure: Network Integrations`` permission with ``read`` or ``full`` access. Managing zone-based firewall rules requires the ``Infrastructure: Network Firewalls`` permission with ``managerules`` or ``full`` access.
