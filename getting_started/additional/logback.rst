logback config
--------------

.. note:: This doc is for 5.4.4+ versions that use ``logback.xml``. 5.4.3 and earlier versions use ``logback.groovy`` with a different syntax that is not compatible with this doc. Please refer to 5.4.3 and earlier documentation for ``logback.groovy`` configuration details.

The log output for the morpheus-ui service is configured in the logback.xml file. Log output levels can be updated when more or less log output is desired.

Setting Log Levels
^^^^^^^^^^^^^^^^^^
To change a log level, edit the logback configuration file in /opt/morpheus/conf/logback.xml and save. The changes will be reflected within the configured ``scanPeriod``, 30 seconds by default.

Levels:
 - **OFF** (no log output)
 - **ERROR** (includes error logs)
 - **WARN** (includes warn and error logs)
 - **INFO** (includes info, warn and error logs)
 - **DEBUG** (includes info, warn, error and debug logs)
 - **TRACE** (includes info, warn, error, debug and trace logs)

.. warning:: Use DEBUG and/or TRACE levels with caution. DEBUG & TRACE levels can produce many logs that can consume disk space quickly. Only use DEBUG and/or TRACE levels when needed and target them for specific services.

Example Logback Settings
^^^^^^^^^^^^^^^^^^^^^^^^
Below are sample log configuration settings. This is not a complete list. Additional log names/paths can typically be determined from the standard INFO, WARN and ERROR logs.

:ACI:
  .. code-block:: xml

     <logger name="com.morpheus.integration.NetworkServersController" level="DEBUG"/>
     <logger name="com.morpheus.network.AciNetworkService" level="DEBUG"/>
     <logger name="com.morpheus.network.AciUtility" level="DEBUG"/>
     <logger name="com.morpheus.network.NetworkService " level="DEBUG"/>

:Amazon:
  .. code-block:: xml

     <logger name="com.morpheus.compute.amazon.AmazonComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.AmazonComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.costing.AmazonCostingService" level="DEBUG"/>
     <logger name="com.morpheus.provision.AmazonProvisionService" level="DEBUG"/>

:Azure:
  .. code-block:: xml

     <logger name="com.morpheus.Azure.ServersController" level="DEBUG"/>
     <logger name="com.morpheus.AzureSqlServerProvisionService" level="DEBUG"/>
     <logger name="com.morpheus.compute.azure.AzureComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.AzureComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.compute.AzureCostingService" level="DEBUG"/>

:Chef:
  .. code-block:: xml

     <logger name="com.morpheus.automation.ChefClientService" level="DEBUG"/>
     <logger name="com.morpheus.automation.ChefService" level="DEBUG"/>
     <logger name="com.morpheus.automation.ChefTaskService" level="DEBUG"/>

:DNS:
  .. code-block:: xml

     <logger name="com.morpheus.dns.MicrosoftDnsService" level="DEBUG"/>

:General:
  .. code-block:: xml

     <logger name="com.morpheus.InstanceService" level="DEBUG"/>
     <logger name="com.morpheus.util.ApiUtility" level="DEBUG"/>
     <logger name="com.morpheus.AppService" level="DEBUG"/>
     <logger name="com.morpheus.MorpheusComputeService" level="DEBUG"/>
     <logger name="com.morpheus.RpcService" level="DEBUG"/>
     <logger name="com.morpheus.network.NetworkService " level="DEBUG"/>
     <logger name="com.morpheus.provision.AbstractProvisionService" level="DEBUG"/>
     <logger name="com.morpheus.provision.AbstractBoxProvisionService" level="DEBUG"/>
     <logger name="com.morpheus.compute.ProgressUpdater" level="DEBUG"/>

:Google:
  .. code-block:: xml

     <logger name="com.morpheus.compute.google.GoogleComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.GoogleComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.provision.GoogleProvisionService" level="DEBUG"/>


:Hyper-V:
  .. code-block:: xml

     <logger name="com.morpheus.compute.hyperv.HypervComputeService" level="DEBUG" />
     <logger name="com.morpheus.compute.HypervComputeUtility" level="DEBUG" />

:IBM Cloud:
  .. code-block:: xml

     <logger name="com.morpheus.compute.softlayer.SoftlayerComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.SoftlayerComputeUtility" level="DEBUG"/>

:Kubernetes:
  .. code-block:: xml

     <logger name="com.morpheus.app.KubernetesAppTemplateService" level="DEBUG"/>
     <logger name="com.morpheus.app.KubernetesResourceMappingService" level="DEBUG"/>
     <logger name="com.morpheus.compute.KubernetesComputeService" level="DEBUG"/>
     <logger name="com.morpheus.host.KubernetesHostService" level="DEBUG"/>
     <logger name="com.morpheus.provision.KubernetesProvisionService" level="DEBUG"/>
     <logger name="com.morpheus.storage.KubernetesStorageService" level="DEBUG"/>

:Monitoring:
  .. code-block:: xml

     <logger name="com.morpheus.monitoring.MonitorCheckService" level="DEBUG"/>

:Network:
  .. code-block:: xml

     <logger name="com.morpheusdata.infoblox.InfobloxProvider" level="DEBUG"/>
     <logger name="com.morpheus.network.InfobloxNetworkPoolService" level="DEBUG"/>
     <logger name="com.morpheus.network.NetworkService " level="DEBUG"/>
     <logger name="com.morpheus.network.PluginNetworkPoolService" level="DEBUG"/>

:Nutanix:
  .. code-block:: xml

     <logger name="com.morpheus.compute.nutanix.NutanixComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.NutanixComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.provision.NutanixProvisionService" level="DEBUG"/>

:Openstack:
  .. code-block:: xml

     <logger name="com.morpheus.compute.AbstractOpenStackComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.AbstractOpenStackComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.provision.OpenStackProvisionService" level="DEBUG"/>
     <logger name="com.morpheus.storage.OpenStackSFSStorageService" level="DEBUG"/>

:Option Types:
  .. code-block:: xml

     <logger name="com.morpheus.OptionSourceService" level="DEBUG"/>
     <logger name="com.morpheus.OptionTypeListService" level="DEBUG"/>
     <logger name="com.morpheus.OptionTypeService" level="DEBUG"/>

:Remote Console:
  .. code-block:: xml

     <logger name="com.morpheus.remote.MorpheusGuacamoleWebsocketHandler" level="DEBUG"/>

:SCVMM:
  .. code-block:: xml

     <logger name="com.morpheus.compute.scvmm.ScvmmComputeService" level="DEBUG"/>
     <logger name="com.morpheus.compute.ScvmmComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.provision.ScvmmProvisionService" level="DEBUG"/>

:ServiceNow:
  .. code-block:: xml

     <logger name="com.morpheus.cmdb.ServiceNowCmdbService" level="DEBUG"/>
     <logger name="com.morpheus.integrations.ServiceNowUtility" level="DEBUG"/>

:Tasks:
  .. code-block:: xml

     <logger name="com.morpheus.task.AnsibleTowerTaskService" level="DEBUG"/>
     <logger name="com.morpheus.task.TaskService" level="DEBUG"/>
     <logger name="com.morpheus.task.WinrmTaskService" level="DEBUG"/>

:Terraform:
  .. code-block:: xml

     <logger name="com.morpheus.app.AbstractResourceMappingService" level="DEBUG"/>
     <logger name="com.morpheus.app.TerraformAppTemplateService" level="DEBUG"/>
     <logger name="com.morpheus.app.TerraformAwsResourceMappingService" level="DEBUG"/>
     <logger name="com.morpheus.app.TerraformResourceMappingService" level="DEBUG"/>
     <logger name="com.morpheus.provision.TerraformProvisionService" level="DEBUG"/>

:Usage:
  .. code-block:: xml

     <logger name="com.morpheus.AccountPriceService" level="DEBUG"/>

:vCloud:
  .. code-block:: xml

     <logger name="com.morpheus.compute.vmware.VcloudDirectorComputeService" level="DEBUG"/>
     <logger name="com.morpheus.provision.VcloudDirectorProvisionService" level="DEBUG"/>
     <logger name="com.morpheus.compute.VcdComputeUtility" level="DEBUG"/>

:Veeam:
  .. code-block:: xml

     <logger name="com.morpheus.backup.VeeamBackupService" level="DEBUG"/>

:Vmware:
  .. code-block:: xml

     <logger name="com.morpheus.compute.VmwareComputeUtility" level="DEBUG"/>
     <logger name="com.morpheus.compute.vmware.VmwareComputeService" level="DEBUG"/>
     <logger name="com.morpheus.provision.VmwareProvisionService" level="DEBUG"/>

:vRO:
  .. code-block:: xml

     <logger name="com.morpheus.automation.VroService" level="DEBUG"/>


All core logger paths
^^^^^^^^^^^^^^^^^^^^^

Expand below to see all core |morpheus| logger paths set to INFO level.

.. toggle-header::
    :header: All core logger paths **Click to Expand/Hide**

     .. code-block:: xml

        <logger name="com.bertramlabs.plugins.AccountsAuthService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.AccountsService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.ActiveDirectoryUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.AzureSamlUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.CustomApiUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.CustomExternalUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.DefaultUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.JumpCloudUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.LdapUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.OktaUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.OneLoginUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.PingUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.SamlUserService" level="INFO"/>
        <logger name="com.bertramlabs.plugins.UserSourceAuthenticationProvider" level="INFO"/>
        <logger name="com.morpheus.AbstractComputeService" level="INFO"/>
        <logger name="com.morpheus.AbstractPriceManagerService" level="INFO"/>
        <logger name="com.morpheus.AccountBudgetService" level="INFO"/>
        <logger name="com.morpheus.AccountIntegrationObjectService" level="INFO"/>
        <logger name="com.morpheus.AccountIntegrationService" level="INFO"/>
        <logger name="com.morpheus.AccountInvoiceService" level="INFO"/>
        <logger name="com.morpheus.AccountPriceService" level="INFO"/>
        <logger name="com.morpheus.AccountResourceService" level="INFO"/>
        <logger name="com.morpheus.AccountUsageService" level="INFO"/>
        <logger name="com.morpheus.ActivityService" level="INFO"/>
        <logger name="com.morpheus.analytics.AbstractAnalyticsService" level="INFO"/>
        <logger name="com.morpheus.analytics.AmazonConvertibleRiAnalyticsService" level="INFO"/>
        <logger name="com.morpheus.analytics.CostAnalyticsService" level="INFO"/>
        <logger name="com.morpheus.analytics.UtilizationAnalyticsService" level="INFO"/>
        <logger name="com.morpheus.analytics.WorkloadAnalyticsService" level="INFO"/>
        <logger name="com.morpheus.AnalyticsService" level="INFO"/>
        <logger name="com.morpheus.api.AbstractApiService" level="INFO"/>
        <logger name="com.morpheus.api.agent.CommandService" level="INFO"/>
        <logger name="com.morpheus.api.agent.DownloadService" level="INFO"/>
        <logger name="com.morpheus.api.agent.UploadService" level="INFO"/>
        <logger name="com.morpheus.app.AbstractAppTemplateService" level="INFO"/>
        <logger name="com.morpheus.app.AbstractResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.AppTemplateService" level="INFO"/>
        <logger name="com.morpheus.app.HelmAppTemplateService" level="INFO"/>
        <logger name="com.morpheus.app.KubernetesAppTemplateService" level="INFO"/>
        <logger name="com.morpheus.app.KubernetesResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.MorpheusAppTemplateService" level="INFO"/>
        <logger name="com.morpheus.app.ScribeResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.TerraformAppTemplateService" level="INFO"/>
        <logger name="com.morpheus.app.TerraformAwsResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.TerraformAzurermResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.TerraformGoogleResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.TerraformResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.app.TerraformVsphereResourceMappingService" level="INFO"/>
        <logger name="com.morpheus.ApplianceClientService" level="INFO"/>
        <logger name="com.morpheus.ApplianceDelayedJobService" level="INFO"/>
        <logger name="com.morpheus.ApplianceHealthService" level="INFO"/>
        <logger name="com.morpheus.ApplianceJobService" level="INFO"/>
        <logger name="com.morpheus.ApplianceLicenseService" level="INFO"/>
        <logger name="com.morpheus.ApplianceService" level="INFO"/>
        <logger name="com.morpheus.ApplianceStorageService" level="INFO"/>
        <logger name="com.morpheus.approval.ApprovalService" level="INFO"/>
        <logger name="com.morpheus.approval.RemedyApprovalService" level="INFO"/>
        <logger name="com.morpheus.approval.ServiceNowApprovalService" level="INFO"/>
        <logger name="com.morpheus.AppService" level="INFO"/>
        <logger name="com.morpheus.ArchiveService" level="INFO"/>
        <logger name="com.morpheus.AsyncService" level="INFO"/>
        <logger name="com.morpheus.AuditLogService" level="INFO"/>
        <logger name="com.morpheus.automation.AbstractConfigManagementService" level="INFO"/>
        <logger name="com.morpheus.automation.AnsibleService" level="INFO"/>
        <logger name="com.morpheus.automation.AnsibleTowerService" level="INFO"/>
        <logger name="com.morpheus.automation.ChefService" level="INFO"/>
        <logger name="com.morpheus.automation.ConfigManagementService" level="INFO"/>
        <logger name="com.morpheus.automation.HelmService" level="INFO"/>
        <logger name="com.morpheus.automation.PuppetService" level="INFO"/>
        <logger name="com.morpheus.automation.SaltStackService" level="INFO"/>
        <logger name="com.morpheus.automation.VroService" level="INFO"/>
        <logger name="com.morpheus.backup.AbstractBackupExecutionService" level="INFO"/>
        <logger name="com.morpheus.backup.AbstractBackupJobService" level="INFO"/>
        <logger name="com.morpheus.backup.AbstractBackupProviderService" level="INFO"/>
        <logger name="com.morpheus.backup.AbstractBackupRestoreService" level="INFO"/>
        <logger name="com.morpheus.backup.AbstractBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.AbstractReplicationService" level="INFO"/>
        <logger name="com.morpheus.backup.BackupExecutionInterface" level="INFO"/>
        <logger name="com.morpheus.backup.BackupInterface" level="INFO"/>
        <logger name="com.morpheus.backup.BackupJobInterface" level="INFO"/>
        <logger name="com.morpheus.backup.BackupJobService" level="INFO"/>
        <logger name="com.morpheus.backup.BackupProviderInterface" level="INFO"/>
        <logger name="com.morpheus.backup.BackupProviderService" level="INFO"/>
        <logger name="com.morpheus.backup.BackupRestoreInterface" level="INFO"/>
        <logger name="com.morpheus.backup.BackupRestoreService" level="INFO"/>
        <logger name="com.morpheus.backup.BackupService" level="INFO"/>
        <logger name="com.morpheus.backup.BackupStatus" level="INFO"/>
        <logger name="com.morpheus.backup.BackupStorageService" level="INFO"/>
        <logger name="com.morpheus.backup.DirectoryBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.FileBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.KarmanStorageProviderBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.LvmImageBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.LvmSnapshotBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.MorpheusApplianceBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.MorpheusBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.MorpheusContainerBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.MysqlBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.PluginBackupExecutionService" level="INFO"/>
        <logger name="com.morpheus.backup.PluginBackupJobService" level="INFO"/>
        <logger name="com.morpheus.backup.PluginBackupProviderService" level="INFO"/>
        <logger name="com.morpheus.backup.PluginBackupRestoreService" level="INFO"/>
        <logger name="com.morpheus.backup.PluginReplicationService" level="INFO"/>
        <logger name="com.morpheus.backup.ReplicationInterface" level="INFO"/>
        <logger name="com.morpheus.backup.ReplicationService" level="INFO"/>
        <logger name="com.morpheus.backup.SqlserverBackupService" level="INFO"/>
        <logger name="com.morpheus.backup.TarDirectoryBackupService" level="INFO"/>
        <logger name="com.morpheus.BootMacService" level="INFO"/>
        <logger name="com.morpheus.builds.AbstractBuildsService" level="INFO"/>
        <logger name="com.morpheus.builds.BuildsService" level="INFO"/>
        <logger name="com.morpheus.builds.JenkinsBuildsService" level="INFO"/>
        <logger name="com.morpheus.CapacityService" level="INFO"/>
        <logger name="com.morpheus.CatalogCartService" level="INFO"/>
        <logger name="com.morpheus.CatalogItemService" level="INFO"/>
        <logger name="com.morpheus.CatalogItemTypeService" level="INFO"/>
        <logger name="com.morpheus.certificate.AbstractCertificateService" level="INFO"/>
        <logger name="com.morpheus.certificate.MorpheusCertificateService" level="INFO"/>
        <logger name="com.morpheus.CertificateService" level="INFO"/>
        <logger name="com.morpheus.ChefClientService" level="INFO"/>
        <logger name="com.morpheus.cm.ChangeManagementService" level="INFO"/>
        <logger name="com.morpheus.cm.CherwellCmService" level="INFO"/>
        <logger name="com.morpheus.cmdb.AbstractCmdbService" level="INFO"/>
        <logger name="com.morpheus.cmdb.CmdbService" level="INFO"/>
        <logger name="com.morpheus.cmdb.RemedyCmdbService" level="INFO"/>
        <logger name="com.morpheus.cmdb.ServiceNowCmdbService" level="INFO"/>
        <logger name="com.morpheus.code.AbstractCodeService" level="INFO"/>
        <logger name="com.morpheus.code.CodeService" level="INFO"/>
        <logger name="com.morpheus.code.GitCodeService" level="INFO"/>
        <logger name="com.morpheus.code.GithubCodeService" level="INFO"/>
        <logger name="com.morpheus.compliance.NVDSyncService" level="INFO"/>
        <logger name="com.morpheus.compliance.PackageManagementService" level="INFO"/>
        <logger name="com.morpheus.compute.cisco.UcsComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.CloudPluginComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.ComputeApiService" level="INFO"/>
        <logger name="com.morpheus.compute.ComputeServiceInterface" level="INFO"/>
        <logger name="com.morpheus.compute.IpmiService" level="INFO"/>
        <logger name="com.morpheus.compute.KubernetesComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.MaasComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.ManualComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.OneviewComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.SelfManagedComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.standard.StandardComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.unmanaged.UnmanagedComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.vmware.VcloudDirectorComputeService" level="INFO"/>
        <logger name="com.morpheus.compute.vmware.VmwareComputeService" level="INFO"/>
        <logger name="com.morpheus.ComputeService" level="INFO"/>
        <logger name="com.morpheus.container.ActivemqContainerService" level="INFO"/>
        <logger name="com.morpheus.container.DockerContainerService" level="INFO"/>
        <logger name="com.morpheus.container.DockerContainerUpgradeService" level="INFO"/>
        <logger name="com.morpheus.container.ElasticsearchContainerService" level="INFO"/>
        <logger name="com.morpheus.container.JavaContainerService" level="INFO"/>
        <logger name="com.morpheus.container.MysqlContainerService" level="INFO"/>
        <logger name="com.morpheus.container.NodeContainerService" level="INFO"/>
        <logger name="com.morpheus.container.PostgresContainerService" level="INFO"/>
        <logger name="com.morpheus.container.RedisContainerService" level="INFO"/>
        <logger name="com.morpheus.container.SqlserverContainerService" level="INFO"/>
        <logger name="com.morpheus.ContainerScriptService" level="INFO"/>
        <logger name="com.morpheus.ContainerService" level="INFO"/>
        <logger name="com.morpheus.costing.AbstractCostingService" level="INFO"/>
        <logger name="com.morpheus.costing.CostingInterface" level="INFO"/>
        <logger name="com.morpheus.costing.CostingService" level="INFO"/>
        <logger name="com.morpheus.costing.StandardCostingService" level="INFO"/>
        <logger name="com.morpheus.CurrencyConversionService" level="INFO"/>
        <logger name="com.morpheus.cypher.CypherGORMDatastoreService" level="INFO"/>
        <logger name="com.morpheus.cypher.CypherService" level="INFO"/>
        <logger name="com.morpheus.DashboardService" level="INFO"/>
        <logger name="com.morpheus.DatastoreService" level="INFO"/>
        <logger name="com.morpheus.DataViewService" level="INFO"/>
        <logger name="com.morpheus.DbSchedulerService" level="INFO"/>
        <logger name="com.morpheus.deploy.AbstractDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.AbstractDeployTargetService" level="INFO"/>
        <logger name="com.morpheus.deploy.CloudFoundryAppDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.DefaultDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.DockerDeployTargetService" level="INFO"/>
        <logger name="com.morpheus.deploy.GrailsDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.IisDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.JbossDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.KubernetesDeployTargetService" level="INFO"/>
        <logger name="com.morpheus.deploy.NodeDeployService" level="INFO"/>
        <logger name="com.morpheus.deploy.ServerDeployTargetService" level="INFO"/>
        <logger name="com.morpheus.deploy.VmDeployTargetService" level="INFO"/>
        <logger name="com.morpheus.DeploymentService" level="INFO"/>
        <logger name="com.morpheus.discovery.AbstractDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.DatastoreCapacityDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.DiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.HostBalancingDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.HostCapacityDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.ReservationRecommendationDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.ShutdownDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.discovery.SizeDiscoveryService" level="INFO"/>
        <logger name="com.morpheus.dns.AbstractDnsService" level="INFO"/>
        <logger name="com.morpheus.dns.BindDnsService" level="INFO"/>
        <logger name="com.morpheus.dns.ConsulDnsService" level="INFO"/>
        <logger name="com.morpheus.dns.DNSProvider" level="INFO"/>
        <logger name="com.morpheus.dns.DnsService" level="INFO"/>
        <logger name="com.morpheus.dns.MicrosoftDnsService" level="INFO"/>
        <logger name="com.morpheus.dns.PluginDnsService" level="INFO"/>
        <logger name="com.morpheus.dns.PowerDnsService" level="INFO"/>
        <logger name="com.morpheus.ElasticCleanupService" level="INFO"/>
        <logger name="com.morpheus.EnvironmentService" level="INFO"/>
        <logger name="com.morpheus.EnvironmentVariableService" level="INFO"/>
        <logger name="com.morpheus.ExecuteScheduleTypeService" level="INFO"/>
        <logger name="com.morpheus.ExecutionRequestService" level="INFO"/>
        <logger name="com.morpheus.export.AccountInvoiceExportService" level="INFO"/>
        <logger name="com.morpheus.export.CodeRepositoryExportService" level="INFO"/>
        <logger name="com.morpheus.export.DeploymentExportService" level="INFO"/>
        <logger name="com.morpheus.export.ExecuteScheduleTypeExportService" level="INFO"/>
        <logger name="com.morpheus.export.ExportService" level="INFO"/>
        <logger name="com.morpheus.export.InstanceExportService" level="INFO"/>
        <logger name="com.morpheus.export.integrations.AdminIntegrationExportService" level="INFO"/>
        <logger name="com.morpheus.export.integrations.AutomationIntegrationExportService" level="INFO"/>
        <logger name="com.morpheus.export.integrations.BackupIntegrationExportService" level="INFO"/>
        <logger name="com.morpheus.export.integrations.CertificateIntegrationExportService" level="INFO"/>
        <logger name="com.morpheus.export.integrations.DeployIntegrationExportService" level="INFO"/>
        <logger name="com.morpheus.export.integrations.NetworkIntegrationExportService" level="INFO"/>
        <logger name="com.morpheus.export.LoadBalancerExpertService" level="INFO"/>
        <logger name="com.morpheus.export.LoadBalancerInstancesExportService" level="INFO"/>
        <logger name="com.morpheus.export.NetworkDomainExportService" level="INFO"/>
        <logger name="com.morpheus.export.NetworkExportService" level="INFO"/>
        <logger name="com.morpheus.export.NetworkGroupExportService" level="INFO"/>
        <logger name="com.morpheus.export.NetworkPoolExportService" level="INFO"/>
        <logger name="com.morpheus.export.NetworkRouterExportService" level="INFO"/>
        <logger name="com.morpheus.export.NetworkSecurityGroupExportService" level="INFO"/>
        <logger name="com.morpheus.export.PowerScheduleTypeExportService" level="INFO"/>
        <logger name="com.morpheus.export.ServerExportService" level="INFO"/>
        <logger name="com.morpheus.export.ServerGroupExportService" level="INFO"/>
        <logger name="com.morpheus.export.ServicePlanExportService" level="INFO"/>
        <logger name="com.morpheus.export.TaskExportService" level="INFO"/>
        <logger name="com.morpheus.export.ThresholdExportService" level="INFO"/>
        <logger name="com.morpheus.export.UserExportService" level="INFO"/>
        <logger name="com.morpheus.export.UserGroupExportService" level="INFO"/>
        <logger name="com.morpheus.export.WorkflowExportService" level="INFO"/>
        <logger name="com.morpheus.FileCopyRequestService" level="INFO"/>
        <logger name="com.morpheus.GlobalSearchService" level="INFO"/>
        <logger name="com.morpheus.host.AbstractHostService" level="INFO"/>
        <logger name="com.morpheus.host.DockerHostService" level="INFO"/>
        <logger name="com.morpheus.host.ExternalKubernetesHostService" level="INFO"/>
        <logger name="com.morpheus.host.KubernetesHostService" level="INFO"/>
        <logger name="com.morpheus.host.SwarmHostService" level="INFO"/>
        <logger name="com.morpheus.HttpClientService" level="INFO"/>
        <logger name="com.morpheus.hub.MorpheusHubQueueService" level="INFO"/>
        <logger name="com.morpheus.hub.MorpheusHubService" level="INFO"/>
        <logger name="com.morpheus.hub.MorpheusHubSyncService" level="INFO"/>
        <logger name="com.morpheus.imagebuild.ImageBuildService" level="INFO"/>
        <logger name="com.morpheus.ImageCacheService" level="INFO"/>
        <logger name="com.morpheus.instance.InstanceUpgradeService" level="INFO"/>
        <logger name="com.morpheus.InstanceService" level="INFO"/>
        <logger name="com.morpheus.InstanceTypeService" level="INFO"/>
        <logger name="com.morpheus.integration.AbstractIntegrationService" level="INFO"/>
        <logger name="com.morpheus.integration.CherwellIntegrationService" level="INFO"/>
        <logger name="com.morpheus.integration.GitRepoService" level="INFO"/>
        <logger name="com.morpheus.integration.RemedyIntegrationService" level="INFO"/>
        <logger name="com.morpheus.integration.RunDeckIntegrationService" level="INFO"/>
        <logger name="com.morpheus.integration.SalesForceIntegrationService" level="INFO"/>
        <logger name="com.morpheus.integration.ScribeService" level="INFO"/>
        <logger name="com.morpheus.integration.ServiceNowIntegrationService" level="INFO"/>
        <logger name="com.morpheus.integration.TerraformService" level="INFO"/>
        <logger name="com.morpheus.jobs.AbstractJobExecutorService" level="INFO"/>
        <logger name="com.morpheus.jobs.JobExecutor" level="INFO"/>
        <logger name="com.morpheus.jobs.KubernetesJobExecutorService" level="INFO"/>
        <logger name="com.morpheus.jobs.SecurityScanExecutorService" level="INFO"/>
        <logger name="com.morpheus.jobs.TaskJobExecutorService" level="INFO"/>
        <logger name="com.morpheus.jobs.WorkflowJobExecutorService" level="INFO"/>
        <logger name="com.morpheus.JobService" level="INFO"/>
        <logger name="com.morpheus.KeyPairService" level="INFO"/>
        <logger name="com.morpheus.library.LayoutService" level="INFO"/>
        <logger name="com.morpheus.LicenseService" level="INFO"/>
        <logger name="com.morpheus.LoadBalancerPriceManagerService" level="INFO"/>
        <logger name="com.morpheus.LocalizationService" level="INFO"/>
        <logger name="com.morpheus.LocalRepoService" level="INFO"/>
        <logger name="com.morpheus.log.AbstractLogService" level="INFO"/>
        <logger name="com.morpheus.log.LogRhythmLogService" level="INFO"/>
        <logger name="com.morpheus.log.SplunkLogService" level="INFO"/>
        <logger name="com.morpheus.log.SyslogLogService" level="INFO"/>
        <logger name="com.morpheus.LogService" level="INFO"/>
        <logger name="com.morpheus.maint.UpdateService" level="INFO"/>
        <logger name="com.morpheus.MarketplaceClientService" level="INFO"/>
        <logger name="com.morpheus.MarshallService" level="INFO"/>
        <logger name="com.morpheus.MetadataTagService" level="INFO"/>
        <logger name="com.morpheus.migration.AbstractMigrationService" level="INFO"/>
        <logger name="com.morpheus.migration.HypervisorMigrationService" level="INFO"/>
        <logger name="com.morpheus.migration.LvmMigrationService" level="INFO"/>
        <logger name="com.morpheus.migration.MigrationService" level="INFO"/>
        <logger name="com.morpheus.migration.WindowsMigrationService" level="INFO"/>
        <logger name="com.morpheus.monitoring.AlerterService" level="INFO"/>
        <logger name="com.morpheus.monitoring.AlertRuleService" level="INFO"/>
        <logger name="com.morpheus.monitoring.AvailabilityService" level="INFO"/>
        <logger name="com.morpheus.monitoring.CheckAgentService" level="INFO"/>
        <logger name="com.morpheus.monitoring.IncidentService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MonitorAppService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MonitorChartingService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MonitorCheckManagementService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MonitorCheckService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MonitoringService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MonitorService" level="INFO"/>
        <logger name="com.morpheus.monitoring.MorpheusMonitorService" level="INFO"/>
        <logger name="com.morpheus.monitoring.NewRelicService" level="INFO"/>
        <logger name="com.morpheus.monitoring.ServiceNowService" level="INFO"/>
        <logger name="com.morpheus.MorpheusComputeService" level="INFO"/>
        <logger name="com.morpheus.MorpheusPackageService" level="INFO"/>
        <logger name="com.morpheus.MorpheusSecurityService" level="INFO"/>
        <logger name="com.morpheus.MotdService" level="INFO"/>
        <logger name="com.morpheus.network.AbstractLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.AbstractNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.AbstractNetworkRegistryService" level="INFO"/>
        <logger name="com.morpheus.network.AbstractNetworkSecurityService" level="INFO"/>
        <logger name="com.morpheus.network.AbstractNetworkService" level="INFO"/>
        <logger name="com.morpheus.network.AciNetworkSecurityService" level="INFO"/>
        <logger name="com.morpheus.network.AciNetworkService" level="INFO"/>
        <logger name="com.morpheus.network.AviLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.BluecatNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.BootService" level="INFO"/>
        <logger name="com.morpheus.network.CitrixNetScalerLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.CloudPluginNetworkService" level="INFO"/>
        <logger name="com.morpheus.network.ConsulRegistryService" level="INFO"/>
        <logger name="com.morpheus.network.ConsulService" level="INFO"/>
        <logger name="com.morpheus.network.F5BigIpLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.F5LineRateLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.FirewallService" level="INFO"/>
        <logger name="com.morpheus.network.FortiADCLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.HaproxyLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.InfobloxNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.InternalLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.InternalNetworkSecurityService" level="INFO"/>
        <logger name="com.morpheus.network.InternalNetworkService" level="INFO"/>
        <logger name="com.morpheus.network.IPAMProvider" level="INFO"/>
        <logger name="com.morpheus.network.KubernetesRegistryService" level="INFO"/>
        <logger name="com.morpheus.network.LoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.LocalFirewallService" level="INFO"/>
        <logger name="com.morpheus.network.MorpheusNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.MorpheusRegistryService" level="INFO"/>
        <logger name="com.morpheus.network.NetScalerLoadBalancerService" level="INFO"/>
        <logger name="com.morpheus.network.NetworkConfigService" level="INFO"/>
        <logger name="com.morpheus.network.NetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.NetworkRegistryService" level="INFO"/>
        <logger name="com.morpheus.network.NetworkSecurityService" level="INFO"/>
        <logger name="com.morpheus.network.NetworkService" level="INFO"/>
        <logger name="com.morpheus.network.NetworkServicesService" level="INFO"/>
        <logger name="com.morpheus.network.NutanixNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.PhpipamNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.PluginNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.PxeService" level="INFO"/>
        <logger name="com.morpheus.network.SolarWindsNetworkPoolService" level="INFO"/>
        <logger name="com.morpheus.network.StealthNetworkSecurityService" level="INFO"/>
        <logger name="com.morpheus.NetworkDomainService" level="INFO"/>
        <logger name="com.morpheus.OauthProviderService" level="INFO"/>
        <logger name="com.morpheus.OperationEventService" level="INFO"/>
        <logger name="com.morpheus.OptionSourcePluginService" level="INFO"/>
        <logger name="com.morpheus.OptionSourceService" level="INFO"/>
        <logger name="com.morpheus.OptionTypeListService" level="INFO"/>
        <logger name="com.morpheus.OptionTypeService" level="INFO"/>
        <logger name="com.morpheus.os.LinuxOsService" level="INFO"/>
        <logger name="com.morpheus.os.WindowsOsService" level="INFO"/>
        <logger name="com.morpheus.PermissionService" level="INFO"/>
        <logger name="com.morpheus.plugin.AbstractPluginProviderManagerService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.BackupProviderPluginManagerService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusBackupImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusBackupJobImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusBackupRestoreImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusBackupResultImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusBackupTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusReplicationGroupImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusReplicationImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusReplicationSiteImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.backup.MorpheusReplicationTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.compute.MorpheusComputeServerInterfaceImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.compute.MorpheusComputeZoneFolderImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.compute.MorpheusDatastoreImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.costing.MorpheusAccountInvoiceImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.costing.MorpheusCostingImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.cypher.MorpheusCypherImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.integration.MorpheusAccountInventoryImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.integration.MorpheusIntegrationImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusAccountCredentialImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusAccountCredentialTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusCloudImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusComputePortImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusComputeServerImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusComputeTypeLayoutFactoryImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusComputeTypeSetImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusComputeZonePoolImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusContainerTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusContextImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusInstanceImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusMetadataTagImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusMetadataTagTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusOperationNotificationImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusOsTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusPermissionImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusProcessImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusReportImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusServicePlanImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusSnapshotImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusStatsImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusStorageControllerImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusStorageControllerTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusStorageVolumeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusStorageVolumeTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusTaskImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusUsageImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusVirtualImageImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusVirtualImageLocationImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.MorpheusWikiPageImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkDomainImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkDomainRecordImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkPoolImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkPoolIpImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkPoolRangeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkSubnetImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.network.MorpheusNetworkTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.PluginManagerService" level="INFO"/>
        <logger name="com.morpheus.plugin.PluginProviderManagerService" level="INFO"/>
        <logger name="com.morpheus.plugin.policy.MorpheusPolicyImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.policy.MorpheusPolicyTypeImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.provisioning.MorpheusProvisionImplService" level="INFO"/>
        <logger name="com.morpheus.plugin.web.MorpheusWebRequestImplService" level="INFO"/>
        <logger name="com.morpheus.policy.AbstractPolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.BackupStoragePolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.MotdPolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.NetworkPolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.PolicyServiceInterface" level="INFO"/>
        <logger name="com.morpheus.policy.StorageBucketQuotaPolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.StorageServerQuotaPolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.StorageShareQuotaPolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.TagCompliancePolicyService" level="INFO"/>
        <logger name="com.morpheus.policy.WorkflowPolicyService" level="INFO"/>
        <logger name="com.morpheus.PolicyService" level="INFO"/>
        <logger name="com.morpheus.PowerScheduleService" level="INFO"/>
        <logger name="com.morpheus.PowerScheduleTypeService" level="INFO"/>
        <logger name="com.morpheus.PriceManagerService" level="INFO"/>
        <logger name="com.morpheus.PricePlanService" level="INFO"/>
        <logger name="com.morpheus.ProcessService" level="INFO"/>
        <logger name="com.morpheus.ProfileService" level="INFO"/>
        <logger name="com.morpheus.project.ProjectService" level="INFO"/>
        <logger name="com.morpheus.provision.AbstractBoxProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.AbstractProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.CloudPluginProvisioningService" level="INFO"/>
        <logger name="com.morpheus.provision.DockerEngineProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.DockerProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.ExternalProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.HelmProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.IProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.KubernetesProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.MaasProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.ManualProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.OneviewProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.ScribeProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.SelfManagedProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.StandardProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.SwarmProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.TerraformProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.UcsProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.UnmanagedProvisionService" level="INFO"/>
        <logger name="com.morpheus.provision.WorkflowProvisionService" level="INFO"/>
        <logger name="com.morpheus.ProvisioningService" level="INFO"/>
        <logger name="com.morpheus.ProxyService" level="INFO"/>
        <logger name="com.morpheus.ReferenceService" level="INFO"/>
        <logger name="com.morpheus.report.AbstractReportService" level="INFO"/>
        <logger name="com.morpheus.report.AmazonCoverageReportService" level="INFO"/>
        <logger name="com.morpheus.report.AmazonSavingsReportService" level="INFO"/>
        <logger name="com.morpheus.report.AmazonUtilizationReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudAppCapacityReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudAppUsageReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudCapacityReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudInstanceTypeCapacityReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudInstanceTypeUsageReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudInventoryReportService" level="INFO"/>
        <logger name="com.morpheus.report.CloudUsageReportService" level="INFO"/>
        <logger name="com.morpheus.report.CostReportService" level="INFO"/>
        <logger name="com.morpheus.report.InventoryReportService" level="INFO"/>
        <logger name="com.morpheus.report.InvoiceReportService" level="INFO"/>
        <logger name="com.morpheus.report.MigrationReportService" level="INFO"/>
        <logger name="com.morpheus.report.PluginReportService" level="INFO"/>
        <logger name="com.morpheus.report.ReportService" level="INFO"/>
        <logger name="com.morpheus.report.TenantUsageReportService" level="INFO"/>
        <logger name="com.morpheus.report.TimeSeriesCostReportService" level="INFO"/>
        <logger name="com.morpheus.RoleService" level="INFO"/>
        <logger name="com.morpheus.RpcService" level="INFO"/>
        <logger name="com.morpheus.scale.AbstractScaleService" level="INFO"/>
        <logger name="com.morpheus.scale.MorpheusScaleService" level="INFO"/>
        <logger name="com.morpheus.ScaleService" level="INFO"/>
        <logger name="com.morpheus.scribe.ScribeLibraryService" level="INFO"/>
        <logger name="com.morpheus.ScriptConfigService" level="INFO"/>
        <logger name="com.morpheus.sdn.AbstractSdnService" level="INFO"/>
        <logger name="com.morpheus.sdn.MorpheusSdnService" level="INFO"/>
        <logger name="com.morpheus.sdn.OvsService" level="INFO"/>
        <logger name="com.morpheus.sdn.VethSdnService" level="INFO"/>
        <logger name="com.morpheus.security.AbstractSecurityScanService" level="INFO"/>
        <logger name="com.morpheus.security.ScapScanService" level="INFO"/>
        <logger name="com.morpheus.security.SecurityScanService" level="INFO"/>
        <logger name="com.morpheus.SecurityGroupService" level="INFO"/>
        <logger name="com.morpheus.SequenceService" level="INFO"/>
        <logger name="com.morpheus.ServerScriptService" level="INFO"/>
        <logger name="com.morpheus.ServerService" level="INFO"/>
        <logger name="com.morpheus.ServicePlanService" level="INFO"/>
        <logger name="com.morpheus.SettingsService" level="INFO"/>
        <logger name="com.morpheus.SetupService" level="INFO"/>
        <logger name="com.morpheus.SiteService" level="INFO"/>
        <logger name="com.morpheus.SnapshotPriceManagerService" level="INFO"/>
        <logger name="com.morpheus.SnapshotService" level="INFO"/>
        <logger name="com.morpheus.StatsService" level="INFO"/>
        <logger name="com.morpheus.StatusService" level="INFO"/>
        <logger name="com.morpheus.storage.AbstractStorageServerService" level="INFO"/>
        <logger name="com.morpheus.storage.AbstractStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.BasicStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.CephStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.EcsStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.IsilonStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.KubernetesStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.NfsStorageService" level="INFO"/>
        <logger name="com.morpheus.storage.QnapFileStationService" level="INFO"/>
        <logger name="com.morpheus.storage.StorageServerService" level="INFO"/>
        <logger name="com.morpheus.storage.StorageVolumeService" level="INFO"/>
        <logger name="com.morpheus.storage.ThreeParStorageService" level="INFO"/>
        <logger name="com.morpheus.StorageProviderService" level="INFO"/>
        <logger name="com.morpheus.SubAccountService" level="INFO"/>
        <logger name="com.morpheus.task.AbstractTaskService" level="INFO"/>
        <logger name="com.morpheus.task.AnsibleTaskService" level="INFO"/>
        <logger name="com.morpheus.task.AnsibleTowerTaskService" level="INFO"/>
        <logger name="com.morpheus.task.ChefTaskService" level="INFO"/>
        <logger name="com.morpheus.task.ContainerScriptTaskService" level="INFO"/>
        <logger name="com.morpheus.task.ContainerTemplateTaskService" level="INFO"/>
        <logger name="com.morpheus.task.EmailTaskService" level="INFO"/>
        <logger name="com.morpheus.task.ExecutableTaskInterface" level="INFO"/>
        <logger name="com.morpheus.task.GroovyTaskService" level="INFO"/>
        <logger name="com.morpheus.task.HttpTaskService" level="INFO"/>
        <logger name="com.morpheus.task.JavascriptTaskService" level="INFO"/>
        <logger name="com.morpheus.task.JRubyTaskService" level="INFO"/>
        <logger name="com.morpheus.task.LocalScriptTaskService" level="INFO"/>
        <logger name="com.morpheus.task.PuppetTaskService" level="INFO"/>
        <logger name="com.morpheus.task.PythonTaskService" level="INFO"/>
        <logger name="com.morpheus.task.RestartTaskService" level="INFO"/>
        <logger name="com.morpheus.task.ShellTaskService" level="INFO"/>
        <logger name="com.morpheus.task.TaskConfigService" level="INFO"/>
        <logger name="com.morpheus.task.TaskService" level="INFO"/>
        <logger name="com.morpheus.task.VroTaskService" level="INFO"/>
        <logger name="com.morpheus.task.WinrmTaskService" level="INFO"/>
        <logger name="com.morpheus.task.WriteAttributesTaskService" level="INFO"/>
        <logger name="com.morpheus.trust.AbstractCredentialService" level="INFO"/>
        <logger name="com.morpheus.trust.CredentialProvider" level="INFO"/>
        <logger name="com.morpheus.trust.CredentialService" level="INFO"/>
        <logger name="com.morpheus.trust.CypherCredentialService" level="INFO"/>
        <logger name="com.morpheus.trust.InternalCredentialService" level="INFO"/>
        <logger name="com.morpheus.trust.PluginCredentialService" level="INFO"/>
        <logger name="com.morpheus.UsageLimitService" level="INFO"/>
        <logger name="com.morpheus.UserGroupService" level="INFO"/>
        <logger name="com.morpheus.UserManagementService" level="INFO"/>
        <logger name="com.morpheus.vdi.VdiAppService" level="INFO"/>
        <logger name="com.morpheus.vdi.VdiGatewayService" level="INFO"/>
        <logger name="com.morpheus.vdi.VdiPoolService" level="INFO"/>
        <logger name="com.morpheus.VirtualImagePriceManagerService" level="INFO"/>
        <logger name="com.morpheus.VirtualImageService" level="INFO"/>
        <logger name="com.morpheus.WikiPageService" level="INFO"/>
        <logger name="com.morpheus.worker.DistributedWorkerService" level="INFO"/>
        <logger name="com.morpheus.ZoneFolderService" level="INFO"/>
        <logger name="com.morpheus.ZoneMarketplaceService" level="INFO"/>
        <logger name="com.morpheus.ZonePoolService" level="INFO"/>
        <logger name="com.morpheus.ZoneRegionService" level="INFO"/>
        <logger name="com.morpheus.ZoneService" level="INFO"/>

|

Audit logs
^^^^^^^^^^

#. To set up CEF/SIEM auditing export, add the below appender above or below the other appenders in the logback.xml configuration file:

   .. code-block:: xml

      <appender name="AUDIT" class="ch.qos.logback.core.rolling.RollingFileAppender">
          <file>/var/log/morpheus/morpheus-ui/audit.log</file>
          <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
              <fileNamePattern>audit.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
                <maxFileSize>50MB</maxFileSize>
                <maxHistory>30</maxHistory>
          </rollingPolicy>
          <encoder>
              <pattern>[%d] [%thread] %-5level %logger{15} - %maskedMsg %n</pattern>
          </encoder>
      </appender>


    .. note:: ``maxFileSize`` and ``maxHistory`` values can be updated as needed.

#. Add the below logger above or below the other loggers in the logback.xml configuration file (make sure it is below, not above, the appender from the previous step or an error will occur):

   .. code-block:: xml

      <logger name="com.morpheus.AuditLogService" level="INFO" additivity="false">
          <appender-ref ref="AUDIT" />
      </logger>

#. Once you have done this, you need to restart the |morpheus| Application server:

   .. code-block:: bash

      morpheus-ctl stop morpheus-ui

   .. NOTE:: Please be aware this will stop the web interface for |morpheus|.

#. Once the service has stopped enter the following at the xml prompt to restart (if the service does not stop, replace stop with graceful-kill and retry)

   .. code-block:: bash

      morpheus-ctl start morpheus-ui

#. To know when the UI is up and running you can run the following command

   .. code-block:: bash

      morpheus-ctl tail morpheus-ui

   Once you see the ASCI art show up you will be able to log back into the User Interface. A new audit file will have been created called audit.log and will found in the default |morpheus| log path which is ``/var/log/morpheus/morpheus-ui/``

This is only an example and other configurations are possible, such as creating an appender definition for your SIEM audit database product.
