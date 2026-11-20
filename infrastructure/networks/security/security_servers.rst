.. _security_servers:

Security Servers
----------------

``Infrastructure > Network > Integrations > (select Security Server)``

Overview
^^^^^^^^

Security servers provide centralized management of network security policies, firewall rules, and security groups within |morpheus|. These integrations connect to third-party security platforms (such as Cisco ACI or VMware NSX) to provide security orchestration alongside network provisioning workflows.

Security server integrations enable:

- Centralized firewall rule management
- Security group and policy enforcement
- Commit-based workflow for staged changes
- Integration with provisioning for automatic security policy application

.. NOTE:: Security servers are distinguished from network servers by their primary function. A single integration (e.g., Cisco ACI) may appear as both a network and security integration.

Viewing Security Servers
^^^^^^^^^^^^^^^^^^^^^^^^

#. Navigate to ``Infrastructure > Network > Integrations``
#. Security server integrations are listed alongside network integrations and are identified by their type

Adding a Security Server Integration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. Navigate to ``Infrastructure > Network > Integrations``
#. Click :guilabel:`+ ADD`
#. Select the desired security integration type from the **Security** category
#. Complete the integration-specific configuration fields (see individual integration guides)
#. Click :guilabel:`ADD NETWORK INTEGRATION`

Security Server Detail View
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Selecting a security server integration opens the detail view with tabs that may include:

- **Summary** — Overview of server status, connection details, and sync status
- **Firewall** — Firewall groups and rules management
- **Groups** — Security groups and endpoint groups
- **Scopes** — Scope/tenant segmentation

Commit Workflow
^^^^^^^^^^^^^^^

Some security server integrations support a commit-based workflow, where changes are staged locally before being pushed to the security platform. This provides a review step before policy changes take effect.

#. Make desired changes (create/edit/delete firewall rules, groups, etc.)
#. Review staged changes in the pending changes section
#. Click :guilabel:`COMMIT` to push all pending changes to the security platform
#. Alternatively, click :guilabel:`DISCARD` to abandon pending changes

.. IMPORTANT:: Changes made in a commit-based workflow do not take effect on the security platform until they are committed. This allows multiple changes to be batched and reviewed before deployment.

Required Role Permissions
^^^^^^^^^^^^^^^^^^^^^^^^^

Access to security server management requires the ``Infrastructure: Network Integrations`` permission with ``read`` or ``full`` access. Firewall rule management requires the ``Infrastructure: Network Firewalls`` permission (``infrastructure-network-firewalls``) with ``read``, ``managerules``, or ``full`` access.

API
^^^

Security servers are accessible through the |morpheus| API:

- ``GET /api/network-security-servers`` — List security servers
- ``GET /api/network-security-servers/:id`` — Get a specific security server
- ``GET /api/network-security-server-types`` — List available security server types
