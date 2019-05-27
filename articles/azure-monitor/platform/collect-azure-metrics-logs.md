---
title: Collecter les journaux de service Azure et des métriques dans l’espace de travail Analytique de journal | Microsoft Docs
description: Configurer les diagnostics sur des ressources Azure pour écrire des journaux et métriques pour l’espace de travail Analytique de journal dans Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129719"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Collecter les journaux de service Azure et des métriques dans l’espace de travail Analytique de journal dans Azure Monitor

Il existe quatre façons différentes de collecter des journaux d’activité et des métriques pour les services Azure :

1. Diagnostics Azure directement dans l’espace de travail Analytique de journal dans Azure Monitor (*Diagnostics* dans le tableau suivant)
2. Diagnostics Azure vers le stockage Azure pour l’espace de travail Analytique de journal dans Azure Monitor (*stockage* dans le tableau suivant)
3. Connecteurs pour les services Azure (*Connecteurs* dans le tableau suivant)
4. Des scripts pour collecter puis publier des données dans l’espace de travail Analytique de journal dans Azure Monitor (vide dans le tableau suivant et pour les services qui ne sont pas répertoriés)


| de diffusion en continu                 | Type de ressource                           | Journaux d’activité        | Mesures     | Solution |
| --- | --- | --- | --- | --- |
| Passerelles d’application    | Microsoft.Network/applicationGateways   | Diagnostics | Diagnostics | [Azure Application Gateway Analytics](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application Insights    |                                         | Connecteur   | Connecteur   | [Connecteur Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (version préliminaire) |
| Comptes Automation     | Microsoft.Automation/AutomationAccounts | Diagnostics |             | [Plus d’informations](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Comptes Batch          | Microsoft.Batch/batchAccounts           | Diagnostics | Diagnostics | |
| Services cloud classiques  |                                         | Stockage     |             | [Plus d’informations](azure-storage-iis-table.md) |
| Cognitive services      | Microsoft.CognitiveServices/accounts    |             | Diagnostics | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostics |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Diagnostics |             | |
| Espace de noms Event Hub     | Microsoft.EventHub/namespaces           | Diagnostics | Diagnostics | |
| IoT Hubs                | Microsoft.Devices/IotHubs               |             | Diagnostics | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostics |             | [KeyVault Analytics](../insights/azure-key-vault.md) |
| Équilibreurs de charge          | Microsoft.Network/loadBalancers         | Diagnostics |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostics | Diagnostics | |
| Network Security Group | Microsoft.Network/networksecuritygroups | Diagnostics |             | [Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| Coffres de récupération         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (version préliminaire)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Services de recherche         | Microsoft.Search/searchServices         | Diagnostics | Diagnostics | |
| Espace de noms Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnostics | Diagnostics | [Service Bus Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Stockage     |             | [Service Fabric Analytics (version préliminaire)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostics | [Azure SQL Analytics (version préliminaire)](../insights/azure-sql.md) |
| Stockage                 |                                         |             | Script      | [Azure Storage Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | Extension   | Extension <br> Diagnostics  | |
| Groupes de machines virtuelles identiques | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostics | |
| Batteries de serveurs web        | Microsoft.Web/serverfarms               |             | Diagnostics | |
| Sites web               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostics | [Azure Web Apps Analytics (version préliminaire)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Pour l’analyse des machines virtuelles (Linux et Windows), nous vous recommandons d’installer l’[extension de machine virtuelle Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md). L’agent vous fournit les informations collectées à partir de vos machines virtuelles. Vous pouvez également utiliser l’extension pour les jeux de mise à l’échelle de machine virtuelle.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Diagnostics Azure directement dans Log Analytics
De nombreuses ressources Azure sont en mesure d’écrire des journaux de diagnostic et métriques directement à un espace de travail Analytique de journal dans Azure Monitor, et c’est le meilleur moyen de collecter les données pour l’analyse. Lorsque vous utilisez les diagnostics Azure, les données sont écrites immédiatement dans l’espace de travail, et il est inutile d’écrire tout d’abord les données vers le stockage.

Les ressources Azure qui prennent en charge [Azure monitor](../../azure-monitor/overview.md) peuvent envoyer leurs journaux et métriques directement à un espace de travail Analytique de journal.

> [!NOTE]
> Envoi de plusieurs métriques multidimensionnelles à un espace de travail Analytique de journal via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : Le métrique « Messages entrants » dans un hub d’événements peut être examiné et représenté sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

* Pour plus d’informations sur les métriques disponibles, voir [Mesures prises en charge avec Azure Monitor](../../azure-monitor/platform/metrics-supported.md).
* Pour plus d’informations sur les journaux d’activité disponibles, voir [Schéma et services pris en charge pour les journaux de diagnostic](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Activer les diagnostics avec PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L’exemple PowerShell suivant montre comment utiliser [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) pour activer les diagnostics sur un groupe de sécurité réseau. La même approche fonctionne pour toutes les ressources prises en charge : définissez `$resourceId` sur l’ID de la ressource pour laquelle vous souhaitez activer les diagnostics.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Activer les diagnostics avec des modèles Resource Manager

Pour activer les diagnostics sur une ressource lors de sa création, et envoyer les diagnostics vers votre espace de travail Log Analytics, vous pouvez utiliser un modèle semblable à celui ci-dessous. Cet exemple est destiné à un compte Automation, mais fonctionne pour tous les types de ressources pris en charge.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnostics Azure vers stockage, puis vers Log Analytics

Pour la collecte des journaux sur certaines ressources, il est possible envoyer les journaux vers le stockage Azure, puis configurer l’espace de travail Analytique de journal pour lire les journaux à partir du stockage.

Azure Monitor peut utiliser cette approche pour collecter des diagnostics à partir du stockage Azure pour les journaux et les ressources suivantes :

| Ressource | Journaux d’activité |
| --- | --- |
| Service Fabric |ETWEvent <br> Événement opérationnel <br> Événement Reliable Actor <br> Événement de service fiable |
| Virtual Machines |Syslog Linux <br> Événement Windows <br> Journal IIS <br> ETWEvent Windows |
| Rôles web <br> Rôles de travail |Syslog Linux <br> Événement Windows <br> Journal IIS <br> ETWEvent Windows |

> [!NOTE]
> Vous sont facturés des données Azure normaux pour stockage et les transactions lorsque vous envoyez des diagnostics pour un compte de stockage et lorsque l’espace de travail Analytique de journal lit les données à partir de votre compte de stockage.
>
>

Consultez [utilisez stockage blob pour IIS et la table de stockage pour les événements](azure-storage-iis-table.md) pour en savoir plus sur la façon dont Azure Monitor peut collecter ces journaux.

## <a name="connectors-for-azure-services"></a>Connecteurs pour les services Azure

Il existe un connecteur pour Application Insights, ce qui permet aux données collectées par Application Insights à envoyer à un espace de travail Analytique de journal.

En savoir plus sur le [connecteur Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>Scripts pour collecter et publier des données sur l’espace de travail Analytique de journal

Pour les services Azure qui ne fournissent pas un moyen direct d’envoyer des journaux et les mesures à un espace de travail Analytique de journal, vous pouvez utiliser un script d’automatisation d’Azure pour collecter les journaux et les métriques. Le script peut alors envoyer les données à l’espace de travail à l’aide de la [API du collecteur de données](../../azure-monitor/platform/data-collector-api.md)

La galerie de modèles Azure a [exemples d’utilisation d’Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) pour collecter des données à partir des services et l’envoyer à Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser un Stockage Blob pour IIS et un Stockage Table pour les événements](azure-storage-iis-table.md) afin de lire les journaux pour les services Azure qui écrivent des diagnostics dans Stockage Table ou les journaux IIS écrits dans le Stockage Blob.
* [Activer les solutions](../../azure-monitor/insights/solutions.md) pour fournir des informations sur les données.
* [Utiliser les requêtes de recherche](../../azure-monitor/log-query/log-query-overview.md) pour analyser les données.
