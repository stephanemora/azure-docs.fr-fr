---
title: Meilleures pratiques pour la surveillance du Stockage File d’attente Azure
description: Découvrez les meilleures pratiques d’utilisation des métriques et journaux pour surveiller votre Stockage File d’attente Azure.
author: normesta
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.author: normesta
ms.date: 08/24/2021
ms.custom: monitoring
ms.openlocfilehash: e7344330740bebc41a49547c7602a852b5f7665f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832326"
---
# <a name="best-practices-for-monitoring-azure-queue-storage"></a>Meilleures pratiques pour la surveillance du Stockage File d’attente Azure

Cet article présente des scénarios courants de surveillance du Stockage File d’attente, ainsi que des recommandations de meilleures pratiques pour les mettre en œuvre.  

## <a name="monitor-message-counts-in-each-queue"></a>Surveiller le nombre de messages dans chaque file d’attente 

Vous pouvez surveiller le nombre de messages dans toutes les files d’attente d’un compte de stockage à l’aide de la métrique `QueueMessageCount`. Cette métrique est actualisée quotidiennement.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Si vous utilisez PowerShell, vous pouvez vous servir d’une commande similaire à la suivante :  

```powershell
(Get-AzMetric -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/queueServices/default -MetricName "QueueMessageCount").data.Average
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Si vous utilisez Azure CLI, vous pouvez vous servir une commande similaire à la suivante : 

```azurecli
az monitor metrics list --resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/contosogroup/providers/Microsoft.Storage/storageAccounts/contoso/ --metric "QueueMessageCount" --interval PT1H --query value[0].timeseries[0].data[0].average
```

---

Vous pouvez déterminer de manière dynamique si des charges de travail doivent être ajustées pour gérer le volume des messages en interrogeant le nombre approximatif de messages dans chaque file d’attente, avant de prendre une mesure appropriée. Pour obtenir le nombre approximatif de messages, utilisez l’opération REST [Get Queue Metadata](/rest/api/storageservices/get-queue-metadata) (obtenir les métadonnées de file d’attente) ou l’un des kits de développement logiciel (SDK) de Stockage Blob pris en charge. 

L’exemple suivant utilise la bibliothèque Stockage Azure .NET v12 pour obtenir le nombre approximatif de messages.

```csharp
        static async Task<string> RetrieveNextMessageAsync(QueueClient theQueue)
        {
            if (await theQueue.ExistsAsync())
            {
                QueueProperties properties = await theQueue.GetPropertiesAsync();

                if (properties.ApproximateMessagesCount > 0)
                {
                    QueueMessage[] retrievedMessage = await theQueue.ReceiveMessagesAsync(1);
                    string theMessage = retrievedMessage[0].MessageText;
                    await theQueue.DeleteMessageAsync(retrievedMessage[0].MessageId, retrievedMessage[0].PopReceipt);
                    return theMessage;
                }

                return null;
            }

            return null;
        }
```

Songez également à utiliser Service Bus qui prend en charge les messages par entité. Pour en savoir plus, consultez [Informations de référence sur l’analyse des données Azure Service Bus](../../service-bus-messaging/monitor-service-bus-reference.md). 

## <a name="audit-account-activity"></a>Auditer l’activité du compte

Dans de nombreux cas, vous devez auditer les activités de vos comptes de stockage à des fins de sécurité et de conformité. Les opérations sur les comptes de stockage se répartissent en deux catégories : *Plan de contrôle* et *Plan de données*. 

Une opération de plan de contrôle est une demande Azure Resource Manager de création de compte de stockage ou de mise à jour d’une propriété d’un compte de stockage existant. Pour plus d’informations, consultez [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

Une opération de plan de données est une opération sur les données d’un compte de stockage, qui résulte d’une demande adressée au point de terminaison du service de stockage. Par exemple, une opération de plan de données est exécutée lorsque vous ajoutez un message à la file d’attente. Pour plus d’informations, consultez [API de Stockage Azure](/rest/api/storageservices/). 

La section montre comment trouver les informations sur le « quand », le « qui », le « quoi » et le « comment » des opérations de plan de contrôle et de plan de données.

### <a name="auditing-control-plane-operations"></a>Audit des opérations de plan de contrôle

Les opérations de Resource Manager sont capturées dans le [journal d’activité Azure](../../azure-monitor/essentials/activity-log.md). Pour afficher le journal d’activité, ouvrez votre compte de stockage dans le portail Azure, puis sélectionnez **Journal d’activité**.

> [!div class="mx-imgBorder"]
> ![Journal d’activité](./media/queues-storage-monitoring-scenarios/activity-log.png)


Ouvrez une entrée de journal pour afficher un JSON décrivant l’activité. Le JSON suivant présente des informations sur le « quand », le « quoi » et le « comment » d’une opération de plan de contrôle :

> [!div class="mx-imgBorder"]
> ![JSON du journal d’activité](./media/queues-storage-monitoring-scenarios/activity-log-json.png)

La disponibilité des informations sur le « qui » dépend de la méthode d’authentification utilisée pour effectuer l’opération de plan de contrôle. Si l’autorisation est le fait d’un principal de sécurité Azure AD, l’identificateur d’objet de celui-ci apparaît également dans cette sortie JSON (par exemple, `"http://schemas.microsoft.com/identity/claims/objectidentifier": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"`). Étant donné que vous ne verrez peut-être pas toujours d’autres informations relatives à l’identité, telles qu’une adresse e-mail ou un nom, l’identificateur d’objet est toujours la meilleure façon d’identifier de manière unique le principal de sécurité. 

Vous pouvez trouver le nom convivial de celui-ci en prenant la valeur de l’identificateur d’objet et en recherchant le principal de sécurité dans la page Azure AD du portail Azure. La capture d’écran suivante montre un résultat de recherche dans Azure AD.

> [!div class="mx-imgBorder"]
> ![Recherche dans Azure Active Directory](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

### <a name="auditing-data-plane-operations"></a>Audit des opérations de plan de données

Les opérations de plan de données sont capturées dans des [journaux de ressources Azure pour le Stockage](monitor-queue-storage.md#analyzing-logs). Vous pouvez [configurer un paramètre de diagnostic](monitor-queue-storage.md#send-logs-to-azure-log-analytics) afin d’exporter les journaux vers l’espace de travail Log Analytics pour une expérience de requête native. 

Voici une requête Log Analytics qui extrait les informations sur le « quand », le « qui », le « quoi » et le « comment » dans une liste d’entrées de journal. 

```kusto
StorageQueueLogs 
| where TimeGenerated > ago(3d) 
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

Pour la partie « quand » de votre audit, le champ `TimeGenerated` indique quand l’entrée de journal a été enregistrée. 

Pour la partie « quoi » de votre audit, le champ `Uri` indique l’élément qui a été modifié ou lu. 

Pour la partie « comment » de votre audit, le champ `OperationName` indique l’opération qui a été exécutée. 

Pour la partie « qui » de votre audit, le champ `AuthenticationType` indique le type d’authentification utilisé pour effectuer une demande. Ce champ peut afficher tout type d’authentification pris en charge par Stockage Azure, y compris l’utilisation d’une clé de compte, d’un jeton de signature d’accès partagé (SAP) ou d’une authentification Azure Active Directory (Azure AD). 

Si une demande a été authentifiée à l’aide d’Azure AD, le champ `RequesterObjectId` constitue la méthode la plus fiable pour identifier le principal de sécurité. Vous pouvez trouver le nom convivial de celui-ci en prenant la valeur du champ `RequesterObjectId` et en recherchant le principal de sécurité dans la page Azure AD du portail Azure. La capture d’écran suivante montre un résultat de recherche dans Azure AD.

> [!div class="mx-imgBorder"]
> ![Recherche dans Azure Active Directory-2](./media/queues-storage-monitoring-scenarios/search-azure-active-directory.png)

Dans certains cas, un nom d’utilisateur principal, ou *UPN*, peut apparaître dans les journaux. Par exemple, si le principal de sécurité est un utilisateur Azure AD, l’UPN s’affichera probablement. Pour d’autres types de principaux de sécurité tels que les identités gérées attribuées par l’utilisateur, ou dans certains scénarios tels que l’authentification croisée de locataire Azure AD, l’UPN n’apparaît pas dans les journaux. 

Cette requête affiche toutes les opérations d’écriture effectuées par les principaux de sécurité OAuth.

```kusto
StorageQueueLogs
| where TimeGenerated > ago(3d)
  and OperationName == "PutMessage"
  and AuthenticationType == "OAuth"
| project TimeGenerated, AuthenticationType, RequesterObjectId, OperationName, Uri
```

L’authentification avec clé partagée et SAP n’offre aucun moyen d’auditer des identités individuelles. Par conséquent, si vous souhaitez améliorer votre capacité à auditer sur la base de l’identité, nous vous recommandons de passer à Azure AD et d’empêcher l’authentification avec clé partagée et SAP. Pour savoir comment empêcher l’authentification avec clé partagée et SAP, consultez [Empêcher l’autorisation avec clé partagée pour un compte de stockage Azure](../common/shared-key-authorization-prevent.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json&tabs=portal). Pour commencer à utiliser Azure AD, consultez [Autoriser l’accès aux blobs avec Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="optimize-cost-for-infrequent-queries"></a>Optimiser les coûts des requêtes peu fréquentes

Vous pouvez exporter les journaux vers Log Analytics afin de bénéficier de fonctionnalités de requête natives riches. Lorsque vous avez des transactions massives sur votre compte de stockage, le coût d’utilisation des journaux avec Log Analytics peut être élevé. Consultez [Tarification d’Azure Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). Si vous n’envisagez d’interroger des journaux qu’occasionnellement (par exemple, à des fins d’audit de conformité), vous pouvez envisager de réduire le coût total de ces requêtes en exportant les journaux vers un compte de stockage, puis en utilisant une solution de requête serverless sur données de journal, telle qu’Azure Synapse.

Azure Synapse vous permet de créer un pool SQL serverless pour interroger des données de journal quand vous en avez besoin. Cela peut réduire sensiblement les coûts. 

1. Exportez les journaux vers un compte de stockage. Consultez [Création d’un paramètre de diagnostic](monitor-queue-storage.md#creating-a-diagnostic-setting).

2. Créez et configurez un espace de travail Synapse. Consultez [Démarrage rapide : créer un espace de travail Synapse](../../synapse-analytics/quickstart-create-workspace.md).

2. Interrogez les journaux. Consultez [Interroger des fichiers JSON à l’aide d’un pool SQL serverless dans Azure Synapse Analytics](../../synapse-analytics/sql/query-json-files.md).

   Voici un exemple :

   ```sql
    select
        JSON_VALUE(doc, '$.time') AS time,
        JSON_VALUE(doc, '$.properties.accountName') AS accountName,
        JSON_VALUE(doc, '$.identity.type') AS identityType,    
        JSON_VALUE(doc, '$.identity.requester.objectId') AS requesterObjectId,
        JSON_VALUE(doc, '$.operationName') AS operationName,
        JSON_VALUE(doc, '$.callerIpAddress') AS callerIpAddress,
        JSON_VALUE(doc, '$.uri') AS uri
        doc
    from openrowset(
            bulk 'https://demo2uswest4log.blob.core.windows.net/insights-logs-storageread/resourceId=/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/demo2uswest/blobServices/default/y=2021/m=03/d=19/h=*/m=*/PT1H.json',
            format = 'csv', fieldterminator ='0x0b', fieldquote = '0x0b'
        ) with (doc nvarchar(max)) as rows
    order by JSON_VALUE(doc, '$.time') desc

   ```

## <a name="see-also"></a>Voir aussi

- [Surveiller le service Stockage File d’attente](monitor-queue-storage.md)
- [Informations de référence sur les données de supervision du service Stockage File d’attente Azure](monitor-queue-storage-reference.md)
- [Tutoriel : Utiliser des requêtes Kusto dans Azure Data Explorer et Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)
- [Bien démarrer avec les requêtes de journal dans Azure Monitor](../../azure-monitor/logs/get-started-queries.md).

  

