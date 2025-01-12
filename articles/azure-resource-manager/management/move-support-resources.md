---
title: Kaynak türüne göre işlem desteğini taşıma
description: Yeni bir kaynak grubuna veya aboneliğe taşınabilecek Azure Kaynak türlerini listeler.
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 3a15ddc62c19d74871de33003d46ec2fc5437f51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584695"
---
# <a name="move-operation-support-for-resources"></a>Kaynaklar için taşıma işlemi desteği

Bu makalede, bir Azure Kaynak türünün taşıma işlemini destekleyip desteklemediğini listelenmiştir. Ayrıca bir kaynağı taşırken göz önünde bulundurmanız gereken özel koşullar hakkında bilgi sağlar.

> [!IMPORTANT]
> Çoğu durumda, bir alt kaynak kendi üst kaynağından bağımsız olarak taşınamaz. Alt kaynakların biçiminde bir kaynak türü vardır `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Örneğin, `Microsoft.ServiceBus/namespaces/queues` bir alt kaynağıdır `Microsoft.ServiceBus/namespaces` . Üst kaynağı taşıdığınızda, alt kaynak otomatik olarak onunla birlikte taşınır. Bu makalede bir alt kaynak görmüyorsanız, ana kaynakla birlikte taşındığını varsayabilirsiniz. Üst kaynak taşımayı desteklemiyorsa, alt kaynak taşınamaz.

Kaynak sağlayıcısı ad alanına atlayın:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadihar](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. Apimanane](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. kanıtlama](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Azurestackhcı](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. Faturalandırma](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockzinciri](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. Biliveservices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. tüketim](#microsoftconsumption)
> - [Microsoft. Containerınstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. Contentmoderatör](#microsoftcontentmoderator)
> - [Microsoft. Cortanaanalizi](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. Customerınsights](#microsoftcustomerinsights)
> - [Microsoft. Customerkasası](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. Dbformarıdb](#microsoftdbformariadb)
> - [Microsoft. Dbformyısql](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. deneme](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Genomiks](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. Healthgelişme API 'leri](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ımportexport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. ıotcentral](#microsoftiotcentral)
> - [Microsoft. ıotspaces](#microsoftiotspaces)
> - [Microsoft. Keykasası](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. Machinöğrenim](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. Managedıdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Market](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Marketplacesıralaması](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrate](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. Notificationhub 'Lar](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. Operationalınsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. eşleme](#microsoftpeering)
> - [Microsoft. Poliyelei](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. Powerbiadanmış](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. hisse](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. Securityınsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. Servicefabrickafesi](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. Storagessync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. Storagesyncınt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. SYNAPSE](#microsoftsynapse)
> - [Microsoft. Timeseriesınsights](#microsofttimeseriesinsights)
> - [Microsoft. Token](#microsofttoken)
> - [Microsoft. Virtualmachineımages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. Vmwarechoparlör basit](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. Windowsıot](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | DomainServices | Hayır | Hayır |

## <a name="microsoftaadiam"></a>Microsoft. aadihar

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Hayır | Hayır |
> | diagnosticsettingscategories | Hayır | Hayır |
> | privatelinkforazuread | Yes | Yes |
> | Kira | Yes | Yes |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | destek sağlayıcıları | Hayır | Hayır |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Hayır | Hayır |
> | addsservices | Hayır | Hayır |
> | aracısını | Hayır | Hayır |
> | anonymousapiusers | Hayır | Hayır |
> | yapılandırma | Hayır | Hayır |
> | günlükler | Hayır | Hayır |
> | reports | Hayır | Hayır |
> | servicehealthölçümleri | Hayır | Hayır |
> | services | Hayır | Hayır |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | konfigürasyonları | Hayır | Hayır |
> | generatereyorumgeçişleri | Hayır | Hayır |
> | meta veriler | Hayır | Hayır |
> | Öneriler | Hayır | Hayır |
> | gizlemeleri | Hayır | Hayır |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | actionrules | Yes | Yes |
> | alerts | Hayır | Hayır |
> | alertslist | Hayır | Hayır |
> | alertsmetadata | Hayır | Hayır |
> | alertssummary | Hayır | Hayır |
> | alertssummarylist | Hayır | Hayır |
> | smartdetectoralertrules | Yes | Yes |
> | smartgroups | Hayır | Hayır |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | larý | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. Apimanane

> [!IMPORTANT]
> Tüketim SKU 'suna ayarlanmış bir API Management hizmeti taşınamaz.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Hayır | Hayır |
> | hizmet | Yes | Yes |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | configurationmağazaların | Yes | Yes |
> | configurationmağazaların/eventgridfilters | Hayır | Hayır |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | yay | Yes | Yes |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | apiapps | Hayır | Hayır |
> | appdentities | Hayır | Hayır |
> | geçidinin | Hayır | Hayır |

## <a name="microsoftattestation"></a>Microsoft. kanıtlama

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Yes | Yes |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Hayır | Hayır |
> | datatakma adlar | Hayır | Hayır |
> | denyasatamaları | Hayır | Hayır |
> | erişimi yükseltme | Hayır | Hayır |
> | findorphanroleatamalar | Hayır | Hayır |
> | kaynaktaki | Hayır | Hayır |
> | izinler | Hayır | Hayır |
> | poliyasatamaları | Hayır | Hayır |
> | PolicyDefinitions | Hayır | Hayır |
> | policysetdefinitions | Hayır | Hayır |
> | privatelinkassociations | Hayır | Hayır |
> | resourcemanagementprivatelinks | Hayır | Hayır |
> | roleassignments | Hayır | Hayır |
> | roleatamasussusageölçümleri | Hayır | Hayır |
> | roledefinitions | Hayır | Hayır |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> Runbook 'lar Otomasyon hesabıyla aynı kaynak grubunda bulunmalıdır.
>
> Daha fazla bilgi için bkz. [Azure Otomasyonu hesabınızı başka bir aboneliğe taşıma](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Yes | Yes |
> | automationaccounts/Configurations | Yes | Yes |
> | automationaccounts/runbook 'lar | Yes | Yes |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | privatebulutlar | Yes | Yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Yes | Yes |
> | b2ctenants | Hayır | Hayır |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | veri denetleyicileri | Hayır | Hayır |
> | hybriddatayöneticileri | Hayır | Hayır |
> | postgresınstances | Hayır | Hayır |
> | SQLInstances | Hayır | Hayır |
> | sqlmanagedınstances | Hayır | Hayır |
> | sqlserverınstances | Hayır | Hayır |
> | sqlserverkayıtları | Yes | Yes |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Hayır | Hayır |
> | kayıtlarında | Yes | Yes |

## <a name="microsoftazurestackhci"></a>Microsoft. Azurestackhcı

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Hayır | Hayır |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Yes | Yes |

## <a name="microsoftbilling"></a>Microsoft. Faturalandırma

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Hayır | Hayır |
> | billingdönemler | Hayır | Hayır |
> | billingpermissions | Hayır | Hayır |
> | billingproperty | Hayır | Hayır |
> | billingroleatamaları | Hayır | Hayır |
> | billingroledefinitions | Hayır | Hayır |
> | bölümlerinin | Hayır | Hayır |
> | kayıt sayısı | Hayır | Hayır |
> | faturalardan | Hayır | Hayır |
> | girişinde | Hayır | Hayır |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | mapapsıs | Hayır | Hayır |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | biztalk | Hayır | Hayır |

## <a name="microsoftblockchain"></a>Microsoft. Blockzinciri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Hayır | Hayır |
> | cordadmembers | Hayır | Hayır |
> | izleyicileri | Hayır | Hayır |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | tokenservices | Hayır | Hayır |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | şema tasmi | Hayır | Hayır |
> | Blueprint | Hayır | Hayır |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | botservices | Yes | Yes |

## <a name="microsoftcache"></a>Microsoft. Cache

> [!IMPORTANT]
> Redsıs örneği için Azure önbelleği bir sanal ağla yapılandırıldıysa, örnek farklı bir aboneliğe taşınamaz. Bkz. [ağ taşıma sınırlamaları](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Redis | Yes | Yes |
> | redisenterprise | Hayır | Hayır |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Hayır | Hayır |
> | calculateexchange | Hayır | Hayır |
> | calculateprice | Hayır | Hayır |
> | calculatepurchaseprice | Hayır | Hayır |
> | larına | Hayır | Hayır |
> | ticari vaalrezervler | Hayır | Hayır |
> | değişimi | Hayır | Hayır |
> | rezervler | Hayır | Hayır |
> | oluşturamaz | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | validatereservationorder | Hayır | Hayır |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Hayır | Hayır |
> | cdnwebapplicationfirewallpolicies | Yes | Yes |
> | edgenodes | Hayır | Hayır |
> | lerinize | Yes | Yes |
> | Profiller/uç noktalar | Yes | Yes |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sertifikadüzenleri | Yes | Yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | özellikler | Hayır | Hayır |
> | DomainNames | Yes | Hayır |
> | quotas | Hayır | Hayır |
> | resourcetypes | Hayır | Hayır |
> | validatesubscriptionmoveavaılabılıty | Hayır | Hayır |
> | virtualmachines | Yes | Yes |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Hayır | Hayır |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | özellikler | Hayır | Hayır |
> | expressroutecrossconnections | Hayır | Hayır |
> | expressroutecrossconnections/peerler | Hayır | Hayır |
> | gatewaysupporteddevices | Hayır | Hayır |
> | networksecuritygroups | Hayır | Hayır |
> | quotas | Hayır | Hayır |
> | rezervler | Hayır | Hayır |
> | virtualnetworks | Hayır | Hayır |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | disklerinden | Hayır | Hayır |
> | images | Hayır | Hayır |
> | osimages | Hayır | Hayır |
> | osplatformımages | Hayır | Hayır |
> | publicımages | Hayır | Hayır |
> | quotas | Hayır | Hayır |
> | storageaccounts | Yes | Hayır |
> | vmımages | Hayır | Hayır |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Bkz. [klasik dağıtım taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md). Klasik dağıtım kaynakları, bu senaryoya özel bir işlemle abonelikler arasında taşınabilir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | işlemler | Hayır | Hayır |

## <a name="microsoftcognitiveservices"></a>Microsoft. Biliveservices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ratecard | Hayır | Hayır |
> | usagetoplamaları | Hayır | Hayır |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Bkz. [sanal makine taşıma Kılavuzu](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Yes | Yes |
> | diskeriþler | Hayır | Hayır |
> | diskencryptionsets | Hayır | Hayır |
> | disklerinden | Yes | Yes |
> | Galeriler | Hayır | Hayır |
> | Galeriler/görüntüler | Hayır | Hayır |
> | Galeriler/resimler/sürümler | Hayır | Hayır |
> | hostgroups | Hayır | Hayır |
> | hostgroups/konaklar | Hayır | Hayır |
> | images | Yes | Yes |
> | proximityplacementgroups | Yes | Yes |
> | restorepointcollections | Hayır | Hayır |
> | restorepointcollections/restorepoints | Hayır | Hayır |
> | sharedvmextensions | Hayır | Hayır |
> | sharedvmımages | Hayır | Hayır |
> | sharedvmımages/sürümler | Hayır | Hayır |
> | anlık görüntüler | Yes | Yes |
> | sshpublickeys | Hayır | Hayır |
> | virtualmachines | Yes | Yes |
> | virtualmachines/uzantıları | Yes | Yes |
> | virtualmachinescalesets | Yes | Yes |

## <a name="microsoftconsumption"></a>Microsoft. tüketim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | aggregmalyt maliyeti | Hayır | Hayır |
> | dengeler | Hayır | Hayır |
> | bütçelerinin | Hayır | Hayır |
> | ücretleriyle | Hayır | Hayır |
> | costtags | Hayır | Hayır |
> | iler | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır |
> | hesaplansın | Hayır | Hayır |
> | oluş | Hayır | Hayır |
> | marketlerinden | Hayır | Hayır |
> | pricesheets | Hayır | Hayır |
> | ürün | Hayır | Hayır |
> | rezervde ayrıntıları | Hayır | Hayır |
> | reservationrecommendationdetails | Hayır | Hayır |
> | rezervationönerilere | Hayır | Hayır |
> | rezervlerin Özeti | Hayır | Hayır |
> | rezervlik işlemleri | Hayır | Hayır |
> | etiketler | Hayır | Hayır |
> | Kira | Hayır | Hayır |
> | larındaki | Hayır | Hayır |
> | UsageDetails | Hayır | Hayır |

## <a name="microsoftcontainerinstance"></a>Microsoft. Containerınstance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kapsayıcı grupları | Hayır | Hayır |
> | serviceassociationlinks | Hayır | Hayır |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kayıt | Yes | Yes |
> | kayıt defterleri/agentpools | Yes | Yes |
> | kayıt defterleri/BuildTasks | Yes | Yes |
> | kayıt defterleri/çoğaltmalar | Yes | Yes |
> | kayıt defterleri/görevler | Yes | Yes |
> | kayıt defterleri/Web kancaları | Yes | Yes |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | containerservices | Hayır | Hayır |
> | managedkümeler | Hayır | Hayır |
> | openshiftmanagedclusters | Hayır | Hayır |

## <a name="microsoftcontentmoderator"></a>Microsoft. Contentmoderatör

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Hayır | Hayır |

## <a name="microsoftcortanaanalytics"></a>Microsoft. Cortanaanalizi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | alerts | Hayır | Hayır |
> | billingaccounts | Hayır | Hayır |
> | bütçelerinin | Hayır | Hayır |
> | cloudbağlayıcıları | Hayır | Hayır |
> | bağlayıcılar | Yes | Yes |
> | bölümlerinin | Hayır | Hayır |
> | boyutlarına | Hayır | Hayır |
> | kayıt sayısı | Hayır | Hayır |
> | aktarımları | Hayır | Hayır |
> | externalbillingaccounts | Hayır | Hayır |
> | tahmin | Hayır | Hayır |
> | sorgu | Hayır | Hayır |
> | register | Hayır | Hayır |
> | reportconfigs | Hayır | Hayır |
> | reports | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |
> | showbackrules | Hayır | Hayır |
> | görünümler | Hayır | Hayır |

## <a name="microsoftcustomerinsights"></a>Microsoft. Customerınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hub'lar | Hayır | Hayır |

## <a name="microsoftcustomerlockbox"></a>Microsoft. Customerkasası

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | istekleri | Hayır | Hayır |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | içermektedir | Hayır | Hayır |
> | resourceproviders | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Çizelge | Hayır | Hayır |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | availablesku 'lar | Hayır | Hayır |
> | databoxedgedevices | Hayır | Hayır |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | çalışma alanı | Hayır | Hayır |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | larına | Yes | Yes |
> | veri katalogları | Hayır | Hayır |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | connectionyöneticileri | Hayır | Hayır |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | paketlerle | Hayır | Hayır |
> | Planlama | Hayır | Hayır |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | veri fabrikaları | Yes | Yes |
> | larının | Yes | Yes |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Hayır | Hayır |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Hayır | Hayır |
> | Hizmetler/Projeler | Hayır | Hayır |
> | Lara | Hayır | Hayır |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | backupkasaları | Hayır | Hayır |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |

## <a name="microsoftdbformariadb"></a>Microsoft. Dbformarıdb

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | larý | Yes | Yes |

## <a name="microsoftdbformysql"></a>Microsoft. Dbformyısql

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Flexibtaservers | Hayır | Hayır |
> | larý | Yes | Yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Flexibtaservers | Hayır | Hayır |
> | sunucu grupları | Hayır | Hayır |
> | larý | Yes | Yes |
> | serversv2 | Yes | Yes |
> | teksunucular | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | artifactsources | Yes | Yes |
> | piyasaya çıkarma | Yes | Yes |
> | servicetopolojileri | Yes | Yes |
> | servicetopolojileri/hizmetler | Yes | Yes |
> | servicetopolojileri/hizmetler/serviceunits | Yes | Yes |
> | adımlar | Yes | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Yes | Yes |
> | Ana bilgisayar havuzları | Yes | Yes |
> | çalışma alanı | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | elaun havuzları | Hayır | Hayır |
> | elaun havuzları/ıothubkiracılar | Hayır | Hayır |
> | ıothubs | Yes | Yes |
> | provisioningservices | Yes | Yes |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | düzenler | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | denetleyiciler | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | labcenters | Hayır | Hayır |
> | larda | Yes | Hayır |
> | Laboratuvarlar/ortamlar | Yes | Yes |
> | Labs/servicerunanlar | Yes | Yes |
> | Labs/virtualmachines | Yes | Hayır |
> | cağını | Yes | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | digitaltwınsınstances | Hayır | Hayır |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Hayır | Hayır |
> | veritabanı hesapları | Yes | Yes |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | etki alanları | Yes | Yes |
> | generatessorequest | Hayır | Hayır |
> | topleveldomains | Hayır | Hayır |
> | validatedomainregistrationınformation | Hayır | Hayır |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | etki alanları | Yes | Yes |
> | eventabonelikleri | Hayır-bağımsız olarak taşınamaz, ancak otomatik olarak abone olunan kaynakla taşınabilir. | Hayır-bağımsız olarak taşınamaz, ancak otomatik olarak abone olunan kaynakla taşınabilir. |
> | extensionkonuları | Hayır | Hayır |
> | partnernamespaces | Yes | Yes |
> | iş ortağı kayıtları | Hayır | Hayır |
> | iş ortağı konuları | Yes | Yes |
> | Sistem konuları | Yes | Yes |
> | konuları | Yes | Yes |
> | topictypes | Hayır | Hayır |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Yes | Yes |
> | öznitelikleri | Yes | Yes |
> | isteyin | Hayır | Hayır |

## <a name="microsoftexperimentation"></a>Microsoft. deneme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Hayır | Hayır |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | özellik sağlayıcıları | Hayır | Hayır |
> | özellikler | Hayır | Hayır |
> | sağlayıcılarla | Hayır | Hayır |
> | subscriptionfeatureregistrations | Hayır | Hayır |

## <a name="microsoftgenomics"></a>Microsoft. Genomiks

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Oto managedaccounts | Hayır | Hayır |
> | Oto managedvmconfigurationprofiles | Hayır | Hayır |
> | guestconfigurationatamaları | Hayır | Hayır |
> | yazılımıdır | Hayır | Hayır |
> | softwareupdateprofile | Hayır | Hayır |
> | softwareupdates | Hayır | Hayır |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hanaınstances | Hayır | Hayır |
> | sapizleyicileri | Hayır | Hayır |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ayrılmış atedhsms | Hayır | Hayır |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> HDInsight kümelerini yeni bir aboneliğe veya kaynak grubuna taşıyabilirsiniz. Bununla birlikte, HDInsight kümesiyle bağlantılı ağ kaynakları (sanal ağ, NIC veya yük dengeleyici gibi) için abonelikler arasında geçiş yapamazsınız. Ayrıca, küme için bir sanal makineye bağlı bir NIC 'ye yeni bir kaynak grubuna taşıyamazsınız.
>
> HDInsight kümesini yeni bir aboneliğe taşırken, önce diğer kaynakları (depolama hesabı gibi) taşıyın. Ardından, HDInsight kümesini kendi kendine taşıyın.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Yes | Yes |

## <a name="microsofthealthcareapis"></a>Microsoft. Healthgelişme API 'leri

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Yes | Yes |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | larla | Yes | Yes |
> | makineler/uzantılar | Yes | Yes |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | veri yöneticileri | Yes | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | cihazlar | Hayır | Hayır |
> | vnfs | Hayır | Hayır |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | bileşenleri | Hayır | Hayır |
> | networkscopes | Hayır | Hayır |

## <a name="microsoftimportexport"></a>Microsoft. ımportexport

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Çizelge | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!IMPORTANT]
> Yeni aboneliğe taşınmasının [abonelik kotalarını](azure-subscription-service-limits.md#azure-monitor-limits)aşmadığından emin olun.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | actiongroups | Yes | Yes |
> | activitylogalerts | Hayır | Hayır |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | temel | Hayır | Hayır |
> | bileşenleri | Yes | Yes |
> | datacollectionrules | Hayır | Hayır |
> | diagnosticsettings | Hayır | Hayır |
> | diagnosticsettingscategories | Hayır | Hayır |
> | eventcategories | Hayır | Hayır |
> | eventTypes | Hayır | Hayır |
> | extendeddiagnosticsettings | Hayır | Hayır |
> | guestdiagnosticsettings | Hayır | Hayır |
> | listmigrationdate | Hayır | Hayır |
> | logdefinitions | Hayır | Hayır |
> | günlüğe kaydetme profilleri | Hayır | Hayır |
> | günlükler | Hayır | Hayır |
> | metricalerts | Hayır | Hayır |
> | metrictemeller | Hayır | Hayır |
> | metricbatch | Hayır | Hayır |
> | MetricDefinitions | Hayır | Hayır |
> | metricnamespaces | Hayır | Hayır |
> | metrics | Hayır | Hayır |
> | migratealertrules | Hayır | Hayır |
> | migratetonewpricingmodel | Hayır | Hayır |
> | çalışma kitapları | Hayır | Hayır |
> | notificationgroups | Hayır | Hayır |
> | privatelinkscopes | Hayır | Hayır |
> | rollbacktolegacypricingmodel | Hayır | Hayır |
> | scheduledqueryrules | Yes | Yes |
> | topology | Hayır | Hayır |
> | işlemler | Hayır | Hayır |
> | vminsightsonboardingstatuses | Hayır | Hayır |
> | Web testleri | Yes | Yes |
> | Web testleri/gettestresultfile | Hayır | Hayır |
> | çalışma kitapları | Yes | Yes |
> | workbooktemplates | Yes | Yes |

## <a name="microsoftiotcentral"></a>Microsoft. ıotcentral

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | apptemplates | Hayır | Hayır |
> | ıotapps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. ıotspaces

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | çıkarılamıyor | Yes | Yes |

## <a name="microsoftkeyvault"></a>Microsoft. Keykasası

> [!IMPORTANT]
> Disk şifrelemesi için kullanılan anahtar kasaları aynı abonelikte veya abonelikler arasında bir kaynak grubuna taşınamaz.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | silinkaults | Hayır | Hayır |
> | hsmpools | Hayır | Hayır |
> | managedhsms | Hayır | Hayır |
> | kasaları | Yes | Yes |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | connectedkümeler | Yes | Yes |
> | registeredsubscriptions | Hayır | Hayır |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Hayır | Hayır |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Yes | Yes |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | labaccounts | Hayır | Hayır |
> | kullanıcılar | Hayır | Hayır |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Hayır | Hayır |
> | Tümleştirme hesapları | Yes | Yes |
> | ıntegrationserviceortamortamları | Yes | Hayır |
> | ıntegrationserviceortamortamları/managedap | Yes | Hayır |
> | ısotedenvironments | Hayır | Hayır |
> | sürdürülen | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. Machinöğrenim

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | commitmentplanlar | Hayır | Hayır |
> | Hizmetleri | Yes | Hayır |
> | çalışma alanı | Yes | Yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | operationalizationkümeleri | Hayır | Hayır |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |
> | teamaccounts | Hayır | Hayır |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | çalışma alanı | Hayır | Hayır |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | configurationatamalar | Hayır | Hayır |
> | maintenanceconfigurations | Yes | Yes |
> | güncelleştirmeler | Hayır | Hayır |

## <a name="microsoftmanagedidentity"></a>Microsoft. Managedıdentity

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | lerinizde | Hayır | Hayır |
> | userassignedıdentities | Hayır | Hayır |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | managednetworks | Hayır | Hayır |
> | managednetworks/managednetworkgroups | Hayır | Hayır |
> | managednetworks/managednetworkpeeringpolicies | Hayır | Hayır |
> | bildirim | Hayır | Hayır |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Pazar placeryumuristrationdefinitions | Hayır | Hayır |
> | registrationatamaları | Hayır | Hayır |
> | registrationdefinitions | Hayır | Hayır |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | getentities | Hayır | Hayır |
> | managementgroups | Hayır | Hayır |
> | managementgroups/Settings | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | starttenantbackfill | Hayır | Hayır |
> | tenantbackfillstatus | Hayır | Hayır |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Yes | Yes |
> | hesaplar/privateatlases | Yes | Yes |

## <a name="microsoftmarketplace"></a>Microsoft. Market

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sunar | Hayır | Hayır |
> | offertypes | Hayır | Hayır |
> | privategallergıtems | Hayır | Hayır |
> | privatestoreclient | Hayır | Hayır |
> | privatemağazaların | Hayır | Hayır |
> | ürün | Hayır | Hayır |
> | Publishers | Hayır | Hayır |
> | register | Hayır | Hayır |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Hayır | Hayır |

## <a name="microsoftmarketplaceordering"></a>Microsoft. Marketplacesıralaması

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | anlaşmalar | Hayır | Hayır |
> | offertypes | Hayır | Hayır |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | mediaservices | Yes | Yes |
> | mediaservices/liveevents | Yes | Yes |
> | mediaservices/streamingendpoints | Yes | Yes |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | appkümeler | Hayır | Hayır |

## <a name="microsoftmigrate"></a>Microsoft. Migrate

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Hayır | Hayır |
> | migrateprojects | Hayır | Hayır |
> | movecollections | Hayır | Hayır |
> | projeyle | Hayır | Hayır |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Hayır | Hayır |
> | objectunderstandingaccounts | Hayır | Hayır |
> | remoterenderingaccounts | Yes | Yes |
> | spatialanchorsaccounts | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Hayır | Hayır |
> | netappaccounts/capacityhavuzları | Hayır | Hayır |
> | netappaccounts/capacityhavuzları/birimleri | Hayır | Hayır |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Bkz. [ağ taşıma Kılavuzu](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | applicationgateway 'ler | Hayır | Hayır |
> | applicationgatewaywebapplicationfirewallpolicies | Hayır | Hayır |
> | applicationsecuritygroups | Yes | Yes |
> | azurefirewalls | Hayır | Hayır |
> | Savunma Konakları | Hayır | Hayır |
> | bgpservicecommunities | Hayır | Hayır |
> | bağlantının | Yes | Yes |
> | ddoscustompolicies | Yes | Yes |
> | ddosprotectionplanlar | Hayır | Hayır |
> | dnszones | Yes | Yes |
> | expressroutedevreleri | Hayır | Hayır |
> | expressroutegateway 'ler | Hayır | Hayır |
> | expressrouteserviceproviders | Hayır | Hayır |
> | firewallpolicies | Yes | Yes |
> | frontkapıların | Hayır | Hayır |
> | ipayırmalar | Yes | Yes |
> | ipgroups | Yes | Yes |
> | loadbalancers | Evet-temel SKU<br> Evet-standart SKU | Evet-temel SKU<br>Standart SKU yok |
> | localnetworkgateway 'ler | Yes | Yes |
> | natgateway 'ler | Hayır | Hayır |
> | networkexperimentprofiles | Hayır | Hayır |
> | networkıntpolicies ilkeleri | Yes | Yes |
> | NetworkInterfaces | Yes | Yes |
> | networkprofiles | Hayır | Hayır |
> | networksecuritygroups | Yes | Yes |
> | networkwatchers | Hayır | Hayır |
> | networkwatchers/connectionmonitörleri | Yes | Hayır |
> | networkwatchers/flowlogs | Yes | Hayır |
> | networkwatchers/pingkafesler | Yes | Hayır |
> | p2svpngateways | Hayır | Hayır |
> | privatednszones | Yes | Yes |
> | privatednszones/virtualnetworklinks | Yes | Yes |
> | privatednszonesinternal | Hayır | Hayır |
> | privateendpointredirectmaps | Hayır | Hayır |
> | privateendpoints | Hayır | Hayır |
> | privatelinkservices | Hayır | Hayır |
> | publicıpaddresses | Evet-temel SKU<br>Evet-standart SKU | Evet-temel SKU<br>Standart SKU yok |
> | publicıpöneklerini | Yes | Yes |
> | routefilters | Hayır | Hayır |
> | routetables | Yes | Yes |
> | securitypartnerproviders | Yes | Yes |
> | serviceendpointpolicies | Yes | Yes |
> | trafficmanagergeographichierarchies | Hayır | Hayır |
> | trafficmanagerprofiles | Yes | Yes |
> | trafficmanagerprofiles/ısı haritalarını | Hayır | Hayır |
> | trafficmanagerusermetricskeys | Hayır | Hayır |
> | virtualhub 'lar | Hayır | Hayır |
> | virtualnetworkgateways | Yes | Yes |
> | virtualnetworks | Yes | Yes |
> | virtualnetworktaps | Hayır | Hayır |
> | virtualyönlendiriciler | Yes | Yes |
> | virtualwan | Hayır | Hayır |
> | vpngateway (sanal WAN) | Hayır | Hayır |
> | vpnserverconfigurations | Hayır | Hayır |
> | vpnsites (sanal WAN) | Hayır | Hayır |

## <a name="microsoftnotificationhubs"></a>Microsoft. Notificationhub 'Lar

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |
> | ad alanları/notificationhub 'lar | Yes | Yes |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Yes | Yes |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Hiper sanal siteler | Hayır | Hayır |
> | ımportsites | Hayır | Hayır |
> | sunucusiteleri | Hayır | Hayır |
> | vmwaresites | Hayır | Hayır |

## <a name="microsoftoperationalinsights"></a>Microsoft. Operationalınsights

> [!IMPORTANT]
> Yeni bir aboneliğe geçiş için [abonelik kotalarını](azure-subscription-service-limits.md#azure-monitor-limits)aşmayacağından emin olun.
>
> Bağlı bir Otomasyon hesabına sahip olan çalışma alanları taşınamaz. Bir taşıma işlemine başlamadan önce, tüm otomasyon hesaplarının bağlantısını kaldırmayı unutmayın.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Hayır | Hayır |
> | deletedworkspaces | Hayır | Hayır |
> | bağlantı hedefleri | Hayır | Hayır |
> | storageınsii configs | Hayır | Hayır |
> | çalışma alanı | Yes | Yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | managementassociations | Hayır | Hayır |
> | managementconfigurations | Yes | Yes |
> | çözümler | Yes | Yes |
> | görünümler | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. eşleme

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Yasallıklar | Hayır | Hayır |
> | peerasns | Hayır | Hayır |
> | peeringlocations | Hayır | Hayır |
> | eşlemeleri | Hayır | Hayır |
> | peeringserviceülkeleriyle | Hayır | Hayır |
> | peeringserviceloing | Hayır | Hayır |
> | peeringserviceproviders | Hayır | Hayır |
> | peeringservices | Hayır | Hayır |

## <a name="microsoftpolicyinsights"></a>Microsoft. Poliyelei

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | poliyevents | Hayır | Hayır |
> | policystates | Hayır | Hayır |
> | policytrackedresources | Hayır | Hayır |
> | düzeltmeler | Hayır | Hayır |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> |  konsolları | Hayır | Hayır |
> | panolar | Yes | Yes |
> | UserSettings | Hayır | Hayır |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. Powerbiadanmış

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | kapasiteler | Yes | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Hayır | Hayır |
> | providerkayıtları | Hayır | Hayır |
> | piyasaya çıkarma | Hayır | Hayır |

## <a name="microsoftquantum"></a>Microsoft. hisse

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | çalışma alanı | Hayır | Hayır |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Bkz. [Kurtarma Hizmetleri taşıma Kılavuzu](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Hayır | Hayır |
> | kasaları | Yes | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Hayır | Hayır |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | lardır | Yes | Yes |
> | resourcechangedetails | Hayır | Hayır |
> | resourcechanges | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | resourceshistory | Hayır | Hayır |
> | subscriptionsstatus | Hayır | Hayır |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | childresources | Hayır | Hayır |
> | acil sorun sorunları | Hayır | Hayır |
> | etkinlikler | Hayır | Hayır |
> | meta veriler | Hayır | Hayır |
> | bildirimler | Hayır | Hayır |

## <a name="microsoftresources"></a>Microsoft. resources

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | dağıtımlar | Hayır | Hayır |
> | deploymentscripts | Hayır | Hayır |
> | deploymentscripts/Günlükler | Hayır | Hayır |
> | Köprü | Hayır | Hayır |
> | sağlayıcılarla | Hayır | Hayır |
> | ResourceGroups | Hayır | Hayır |
> | kaynaklar | Hayır | Hayır |
> | Aboneliklerin | Hayır | Hayır |
> | etiketler | Hayır | Hayır |
> | templatespec | Hayır | Hayır |
> | templatespec/sürümler | Hayır | Hayır |
> | Kira | Hayır | Hayır |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Yes | Hayır |
> | saasresources | Hayır | Hayır |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!IMPORTANT]
> Farklı bölgelerde çeşitli arama kaynaklarını tek bir işlemde taşıyamazsınız. Bunun yerine, bunları ayrı işlemlerde taşıyın.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Hayır | Hayır |
> | searchservices | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Hayır | Hayır |
> | advancedthreatprotectionsettings | Hayır | Hayır |
> | alerts | Hayır | Hayır |
> | allowedconnections | Hayır | Hayır |
> | applicationwhitedökümler | Hayır | Hayır |
> | assessmentmetadata | Hayır | Hayır |
> | değerlendirme | Hayır | Hayır |
> | Oto dissalertsrules | Hayır | Hayır |
> | akışlarını otomatikleştirin | Yes | Yes |
> | Oto provisioningsettings | Hayır | Hayır |
> | karmaşık anceresults | Hayır | Hayır |
> | uyumluluklarına | Hayır | Hayır |
> | datacollectionagents | Hayır | Hayır |
> | devicesecuritygroups | Hayır | Hayır |
> | discoveredsecuritysolutions | Hayır | Hayır |
> | externalsecuritysolutions | Hayır | Hayır |
> | ınformationprotectionpolicies | Hayır | Hayır |
> | ıotsecuritysolutions | Yes | Yes |
> | ıotsecuritysolutions/analiz ticsmodeller | Hayır | Hayır |
> | ıotsecuritysolutions/analticsmodeller/aggreggıt uyarıları | Hayır | Hayır |
> | ıotsecuritysolutions/analiz ticsmodeller/Aggreg, öneriler | Hayır | Hayır |
> | jağaccesspolicies | Hayır | Hayır |
> | ilkeler | Hayır | Hayır |
> | fiyatlandırmalar | Hayır | Hayır |
> | Reve daha karmaşık bakım standartları | Hayır | Hayır |
> | rekontrol ve Re, | Hayır | Hayır |
> | rekontrol ve Re, Re, Re, | Hayır | Hayır |
> | securitycontacts | Hayır | Hayır |
> | securitysolutions | Hayır | Hayır |
> | securitysolutionsreferencedata | Hayır | Hayır |
> | securitydurumlardan | Hayır | Hayır |
> | securitystatusessummaries | Hayır | Hayır |
> | sunucukullanılabilirliği | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |
> | subassessments | Hayır | Hayır |
> | Görevler | Hayır | Hayır |
> | anlatır | Hayır | Hayır |
> | çalışma alanı ayarları | Hayır | Hayır |

## <a name="microsoftsecurityinsights"></a>Microsoft. Securityınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | toplamaları | Hayır | Hayır |
> | alertrules | Hayır | Hayır |
> | alertrutatemplates | Hayır | Hayır |
> | automationrules | Hayır | Hayır |
> | yer işaretleri | Hayır | Hayır |
> | çalışmaların | Hayır | Hayır |
> | veri bağlayıcıları | Hayır | Hayır |
> | varlıklar | Hayır | Hayır |
> | entityqueries | Hayır | Hayır |
> | olaylar | Hayır | Hayır |
> | officeconsents | Hayır | Hayır |
> | ayarlar | Hayır | Hayır |
> | threatıntelligence | Hayır | Hayır |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | consoleservices | Hayır | Hayır |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | geçidinin | Hayır | Hayır |
> | düğümlerini | Hayır | Hayır |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | öznitelikleri | Yes | Yes |
> | premiummessagingregions | Hayır | Hayır |
> | isteyin | Hayır | Hayır |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Hayır | Hayır |
> | leriniz | Yes | Yes |
> | kapsayıcı grupları | Hayır | Hayır |
> | containergroupsets | Hayır | Hayır |
> | edgeclusters | Hayır | Hayır |
> | managedkümeler | Hayır | Hayır |
> | Mamak | Hayır | Hayır |
> | secretmağazaları | Hayır | Hayır |
> | volumes | Hayır | Hayır |

## <a name="microsoftservicefabricmesh"></a>Microsoft. Servicefabrickafesi

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | uygulamalar | Yes | Yes |
> | kapsayıcı grupları | Hayır | Hayır |
> | geçidinin | Yes | Yes |
> | Mamak | Yes | Yes |
> | kaynaklanır | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | piyasaya çıkarma | Hayır | Hayır |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | SignalR | Yes | Yes |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | hybriduseavantajlar | Hayır | Hayır |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Hayır | Hayır |
> | uygulamalar | Hayır | Hayır |
> | jistekleri | Hayır | Hayır |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Bir veritabanı ve sunucu aynı kaynak grubunda olmalıdır. Bir SQL sunucusunu taşıdığınızda, tüm veritabanları da taşınır. Bu davranış, Azure SQL veritabanı ve Azure SYNAPSE Analytics veritabanları için geçerlidir.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ınstancepools | Hayır | Hayır |
> | yerlerini | Yes | Yes |
> | ManagedInstances | Hayır | Hayır |
> | larý | Yes | Yes |
> | sunucular/veritabanları | Yes | Yes |
> | sunucular/veritabanları/backuplongtermretentionpolicies | Yes | Yes |
> | sunucular/elaun havuzları | Yes | Yes |
> | sunucular/jobaccounts | Yes | Yes |
> | sunucular/jobagents | Yes | Yes |
> | virtualkümeler | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Yes | Yes |
> | sqlvirtualmachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Yes | Yes |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | önbelleklerinde | Hayır | Hayır |

## <a name="microsoftstoragesync"></a>Microsoft. Storagessync

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Yes | Yes |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Hayır | Hayır |

## <a name="microsoftstoragesyncint"></a>Microsoft. Storagesyncınt

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Hayır | Hayır |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ilerinde | Hayır | Hayır |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Stream Analytics işleri çalışır durumda olduğunda taşınamaz.

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | leriniz | Hayır | Hayır |
> | streammingjobs | Yes | Yes |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | lý | Hayır | Hayır |
> | larında | Hayır | Hayır |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | Aboneliklerin | Hayır | Hayır |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | services | Hayır | Hayır |
> | destek biletleri | Hayır | Hayır |

## <a name="microsoftsynapse"></a>Microsoft. SYNAPSE

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | çalışma alanı | Hayır | Hayır |
> | çalışma alanları/bigdatapools | Hayır | Hayır |
> | çalışma alanları/sqlpools | Hayır | Hayır |

## <a name="microsofttimeseriesinsights"></a>Microsoft. Timeseriesınsights

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | lý | Yes | Yes |
> | ortamlar/EventSources | Yes | Yes |
> | ortamlar/referencedataset 'ler | Yes | Yes |

## <a name="microsofttoken"></a>Microsoft. Token

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | depolaya | Yes | Yes |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. Virtualmachineımages

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ımagetemplates | Hayır | Hayır |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Azure DevOps aboneliğini değiştirmek için, bkz. [faturalandırma için kullanılan Azure aboneliğini değiştirme](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | account | Hayır | Hayır |
> | Hesap/uzantı | Hayır | Hayır |
> | hesap/proje | Hayır | Hayır |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | arczones | Hayır | Hayır |
> | ResourcePools | Hayır | Hayır |
> | vCenter | Hayır | Hayır |
> | virtualmachines | Hayır | Hayır |
> | virtualmachinetemplates | Hayır | Hayır |
> | virtualnetworks | Hayır | Hayır |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. Vmwarechoparlör basit

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | ayrılmış cloudnodes | Hayır | Hayır |
> | ayrılmış CloudService | Hayır | Hayır |
> | virtualmachines | Hayır | Hayır |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | cihazlar | Hayır | Hayır |
> | vnfs | Hayır | Hayır |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | accounts | Hayır | Hayır |
> | Planlama | Hayır | Hayır |
> | registeredsubscriptions | Hayır | Hayır |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Bkz. [App Service taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | availableyığınları | Hayır | Hayır |
> | billingmeters | Hayır | Hayır |
> | sertifikalar | Hayır | Yes |
> | connectiongateway 'ler | Yes | Yes |
> | bağlantının | Yes | Yes |
> | customapsıs | Yes | Yes |
> | silinmi siteleri | Hayır | Hayır |
> | deploymentlocations | Hayır | Hayır |
> | coğrafi bölgeler | Hayır | Hayır |
> | hostingenvironments | Hayır | Hayır |
> | kubeortamları | Yes | Yes |
> | publishingusers | Hayır | Hayır |
> | Öneriler | Hayır | Hayır |
> | resourcehealthmetadata | Hayır | Hayır |
> | zamanları | Hayır | Hayır |
> | serverfarms | Yes | Yes |
> | sunucugrupları/eventgridfilters | Hayır | Hayır |
> | Siteler | Yes | Yes |
> | siteler/premieraddons | Yes | Yes |
> | siteler/yuvalar | Yes | Yes |
> | sourcecontrols | Hayır | Hayır |
> | staticsites | Hayır | Hayır |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | çoğulactivationkeys | Hayır | Hayır |

## <a name="microsoftwindowsiot"></a>Microsoft. Windowsıot

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | deviceservices | Hayır | Hayır |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | lerine | Hayır | Hayır |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Kaynak türü | Kaynak grubu | Abonelik |
> | ------------- | ----------- | ---------- |
> | bileşenleri | Hayır | Hayır |
> | componentssummary | Hayır | Hayır |
> | izleme örnekleri | Hayır | Hayır |
> | izleme ınstancessummary | Hayır | Hayır |
> | monitörün | Hayır | Hayır |

## <a name="third-party-services"></a>Üçüncü taraf hizmetleri

Üçüncü taraf hizmetler şu anda taşıma işlemini desteklemiyor.

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).

Aynı verileri bir virgülle ayrılmış değerler dosyası ile almak için [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)indirin.
