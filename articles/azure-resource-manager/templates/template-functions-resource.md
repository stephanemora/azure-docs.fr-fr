---
title: Fonctions de modèle - Ressources
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs sur les ressources.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b8d0a3e60654c9d3f951c6f288ea904bb4c0d50b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900636"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Fonctions de ressources pour les modèles Azure Resource Manager

Resource Manager offre les fonctions ci-après pour obtenir des valeurs de ressource :

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [fournisseurs](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Pour obtenir des valeurs de paramètres, de variables ou du déploiement actuel, consultez [Fonctions de valeur de déploiement](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Retourne l’ID d’une [ressource d’extension](../management/extension-resource-types.md), un type de ressource qui s’applique à une autre ressource pour y ajouter des fonctionnalités.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceId |Oui |string |ID de la ressource à laquelle s’applique la ressource d’extension. |
| resourceType |Oui |string |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |string |Nom de la ressource. |
| nom_ressource2 |Non |string |Segment de nom de ressource suivant si nécessaire. |

Continuez à ajouter des noms de ressource en paramètres lorsque le type de ressource contient plus de segments.

### <a name="return-value"></a>Valeur retournée

Le format de base de l’ID de ressource retourné par cette fonction est le suivant :

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Le segment d’étendue varie en fonction de la ressource étendue.

Lorsque la ressource d’extension est appliquée à une **ressource**, l’ID de la ressource est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Lorsque la ressource d’extension est appliquée à un **groupe de ressources**, le format est le suivant :

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Lorsque la ressource d’extension est appliquée à un **abonnement**, le format est le suivant :

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Lorsque la ressource d’extension est appliquée à un **groupe d’administration**, le format est le suivant :

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Exemple extensionResourceId

L’exemple suivant retourne l’ID de la ressource pour un verrou de groupe de ressources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

La syntaxe de cette fonction varie en fonction du nom des opérations de liste. Chaque implémentation retourne des valeurs pour le type de ressource qui prend en charge une opération de liste. Le nom de l’opération doit commencer par `list`. Des utilisations courantes sont `listKeys` et `listSecrets`. 

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |string |Identificateur unique pour la ressource. |
| apiVersion |Oui |string |Version d'API de l'état d'exécution des ressources. En règle générale, au format, **aaaa-mm-jj**. |
| functionValues |Non |object | Objet qui contient les valeurs de la fonction. Fournissez uniquement cet objet pour les fonctions qui prennent en charge la réception d’un objet avec des valeurs de paramètre, comme **listAccountSas** sur un compte de stockage. Un exemple de transmission de valeurs de fonction est illustré dans cet article. | 

### <a name="valid-uses"></a>Utilisations valides

Les fonctions list peuvent être utilisées uniquement dans les propriétés d’une définition de ressource et dans la section outputs d’un modèle ou d’un déploiement. Quand elles sont utilisées avec une [itération de propriété](create-multiple-instances.md#property-iteration), vous pouvez utiliser les fonctions list pour `input`, car l’expression est affectée à la propriété de ressource. Vous ne pouvez pas les utiliser avec `count`, car le nombre doit être déterminé avant que la fonction list ne soit résolue.

### <a name="implementations"></a>Implémentations

Les utilisations possibles de list* sont indiquées dans le tableau suivant.

| Type de ressource | Nom de la fonction |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Listkeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) | 
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Pour déterminer les types de ressources qui ont une opération de liste, utilisez les options suivantes :

* Affichez les [opérations d’API REST](/rest/api/) pour un fournisseur de ressources et recherchez les opérations de liste. Par exemple, les comptes de stockage présentent l’[opération listKeys](/rest/api/storagerp/storageaccounts).
* Utilisez la cmdlet PowerShell [Get-AzureRmProviderOperation](/powershell/module/az.resources/get-azprovideroperation). L’exemple ci-dessous obtient toutes les opérations de liste pour les comptes de stockage :

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Utilisez la commande Azure CLI suivante pour filtrer uniquement les opérations de liste :

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valeur retournée

L’objet retourné varie selon la fonction de liste que vous utilisez. Par exemple, la fonction listKeys pour un compte de stockage retourne le format suivant :

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

D’autres fonctions de liste ont différents formats de retour. Pour afficher le format d’une fonction, incluez-le dans la section des sorties comme indiqué dans l’exemple de modèle.

### <a name="remarks"></a>Notes

Spécifiez la ressource en utilisant le nom de la ressource ou la [fonction resourceId](#resourceid). Lorsque vous utilisez une fonction de liste dans le modèle qui déploie la ressource référencée, utilisez le nom de la ressource.

Si vous utilisez une fonction **list** dans une ressource qui est déployée conditionnellement, la fonction est évaluée même si la ressource n’est pas déployée. Vous obtenez une erreur si la fonction **list** fait référence à une ressource qui n’existe pas. Utilisez la fonction **if** pour vous assurer que la fonction est évaluée lors du déploiement de la ressource. Consultez la [fonction if](template-functions-logical.md#if) pour un exemple de modèle qui utilise « if » et « list » avec une ressource déployée de manière conditionnelle.

### <a name="list-example"></a>Exemple de liste

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) suivant montre comment retourner les clés primaires et secondaires à partir d’un compte de stockage dans la section outputs. Il retourne également un jeton SAS pour le compte de stockage. 

Pour obtenir le jeton SAS, passez un objet pour l’heure d’expiration. L’heure d’expiration doit être dans le futur. Cet exemple vise à montrer comment vous utilisez les fonctions de liste. En général, vous devez utiliser le jeton SAS dans une valeur de ressource au lieu de la retourner sous la forme d’une valeur de sortie. Les valeurs de sortie sont stockées dans l’historique de déploiement et ne sont pas sécurisées.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>fournisseurs

```json
providers(providerNamespace, [resourceType])
```

Renvoie des informations sur un fournisseur de ressources et les types de ressources qu’il prend en charge. Si vous ne fournissez pas un type de ressource, la fonction retourne tous les types pris en charge pour le fournisseur de ressources.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| espacedenoms_fournisseur |Oui |string |Espace de noms du fournisseur. |
| resourceType |Non |string |Type de ressource dans l'espace de noms spécifié. |

### <a name="return-value"></a>Valeur retournée

Chaque type pris en charge est renvoyé au format suivant : 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Le classement du tableau des valeurs retournées n’est pas garanti.

### <a name="providers-example"></a>Exemple de fournisseurs

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) suivant montre comment utiliser la fonction provider :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Pour le fournisseur de ressources **Microsoft.Web** et le type de ressource **sites**, l’exemple précédent retourne un objet au format suivant :

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Renvoie un objet représentant l’état d’exécution d’une ressource.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |string |Nom ou identificateur unique d’une ressource. Lorsque vous référencez une ressource dans le modèle actuel, indiquez uniquement le nom de la ressource en tant que paramètre. Lorsque vous référencez une ressource précédemment déployée, ou lorsque le nom de la ressource est ambigu, fournissez l’ID de la ressource. |
| apiVersion |Non |string |Version d’API de la ressource spécifiée. Incluez ce paramètre quand la ressource n’est pas provisionnée dans le même modèle. En règle générale, au format, **aaaa-mm-jj**. Pour obtenir les versions d’API valides pour votre ressource, consultez [Référence de modèle](/azure/templates/). |
| 'Full' |Non |string |Valeur qui spécifie si l’objet de ressource complet doit être retourné. Si vous ne spécifiez pas `'Full'`, seul l’objet properties de la ressource est retourné. L’objet complet comprend des valeurs telles que l’ID de ressource et l’emplacement. |

### <a name="return-value"></a>Valeur retournée

Chaque type de ressource retourne des propriétés différentes pour la fonction de référence. La fonction ne retourne pas un format prédéfini unique. La valeur retournée varie également selon que vous avez spécifié ou non l’objet complet. Pour afficher les propriétés d’un type de ressource, renvoyez l’objet dans la section des sorties comme indiqué dans l’exemple.

### <a name="remarks"></a>Notes

La fonction de référence récupère l’état d’exécution d’une ressource déployée précédemment ou déployée dans le modèle actuel. Cet article montre des exemples pour les deux scénarios.

En règle générale, vous utilisez la fonction de **référence** pour renvoyer une valeur particulière d’un objet, telle que l’URI du point de terminaison d’objet blob ou le nom de domaine complet.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Utilisez `'Full'` quand vous avez besoin de valeurs de ressource qui ne font pas partie du schéma de propriétés. Par exemple, pour définir des stratégies d’accès à des coffres de clés, obtenez les propriétés d’identité d’une machine virtuelle.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Utilisations valides

La fonction de référence ne peut être utilisée que dans les propriétés d’une définition de ressource et dans la section de sortie d’un modèle ou d’un déploiement. Lorsqu’elle est utilisée avec une [itération de propriété](create-multiple-instances.md#property-iteration), vous pouvez utiliser la fonction de référence pour `input`, car l’expression est affectée à la propriété de ressource. Vous ne pouvez pas l’utiliser avec `count`, car le nombre doit être déterminé avant que la fonction de référence ne soit résolue.

Vous ne pouvez pas utiliser la fonction Référence dans les sorties d’un [modèle imbriqué](linked-templates.md#nested-template) pour retourner une ressource que vous avez déployée dans le modèle imbriqué. Utilisez plutôt un [modèle lié](linked-templates.md#linked-template).

Si vous utilisez une fonction **reference** dans une ressource qui est déployée conditionnellement, la fonction est évaluée même si la ressource n’est pas déployée.  Vous obtenez une erreur si la fonction **reference** fait référence à une ressource qui n’existe pas. Utilisez la fonction **if** pour vous assurer que la fonction est évaluée lors du déploiement de la ressource. Consultez la [fonction if](template-functions-logical.md#if) pour un exemple de modèle qui utilise « if » et « reference » avec une ressource déployée de manière conditionnelle.

### <a name="implicit-dependency"></a>Dépendance implicite

En utilisant la fonction reference, vous déclarez de manière implicite qu’une ressource dépend d’une autre ressource si la ressource référencée est configurée dans le même modèle et vous désignez cette ressource par son nom (pas par son ID). Vous n’avez pas besoin d’utiliser également la propriété dependsOn. La fonction n’est pas évaluée tant que le déploiement de la ressource référencée n’est pas terminé.

### <a name="resource-name-or-identifier"></a>Nom ou identificateur de la ressource

Lorsque vous faites référence à une ressource déployée dans le même modèle, indiquez le nom de la ressource.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Lorsque vous faites référence à une ressource qui n’est pas déployée dans le même modèle, fournissez l’ID de ressource.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Pour éviter toute ambiguïté quant à la ressource à laquelle vous faites référence, vous pouvez fournir un identificateur de ressource complet.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))]"
```

Quand vous créez une référence complète à une ressource, l’ordre utilisé pour combiner les segments de type et de nom n’est pas une simple concaténation des deux. Au lieu de cela, utilisez après l’espace de noms une séquence de paires *type/nom* du moins spécifique au plus spécifique :

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Par exemple :

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` est correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` n’est pas correct

Pour simplifier la création d’un ID de ressource, utilisez les fonctions `resourceId()` décrites dans ce document au lieu de la fonction `concat()`.

### <a name="get-managed-identity"></a>Obtenir une identité managée

[Les identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md) sont des [types de ressources d’extension](../management/extension-resource-types.md) créés implicitement pour certaines ressources. L’identité managée n’étant pas définie explicitement dans le modèle, vous devez référencer la ressource à laquelle elle est appliquée. Utilisez `Full` pour obtenir toutes les propriétés, y compris l’identité créée implicitement.

Par exemple, pour obtenir l’ID de locataire d’une identité managée appliquée à un groupe de machines virtuelles identiques, utilisez :

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Exemple de référence

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) suivant déploie, puis référence une ressource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

L’exemple précédent retourne les deux objets. L’objet de propriétés retourné présente le format suivant :

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

L’objet complet retourné présente le format suivant :

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) suivant référence un compte de stockage qui n’est pas déployé dans ce modèle. Le compte de stockage existe déjà dans le même abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Renvoie un objet qui représente le groupe de ressources actuel. 

### <a name="return-value"></a>Valeur retournée

L’objet renvoyé présente le format suivant :

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

La propriété **ManagedBy** est retournée uniquement pour les groupes de ressources qui contiennent des ressources gérées par un autre service. Pour Managed Applications, Databricks et AKS, la valeur de la propriété est l’ID de ressource de la ressource de gestion.

### <a name="remarks"></a>Notes

La fonction `resourceGroup()` ne peut pas être utilisée dans un modèle qui est [déployé au niveau abonnement](deploy-to-subscription.md). Elle n’est utilisable que dans les modèles déployés sur un groupe de ressources. Vous pouvez utiliser la fonction `resourceGroup()` dans un [modèle lié ou imbriqué (avec portée interne)](linked-templates.md) qui cible un groupe de ressources, même lorsque le modèle parent est déployé dans l’abonnement. Dans ce scénario, le modèle lié ou imbriqué est déployé au niveau du groupe de ressources. Pour plus d’informations sur le ciblage d’un groupe de ressources dans un déploiement au niveau de l’abonnement, consultez [Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements](cross-resource-group-deployment.md).

Une utilisation courante de la fonction resourceGroup consiste à créer des ressources dans le même emplacement que le groupe de ressources. L’exemple suivant utilise l’emplacement du groupe de ressources pour une valeur de paramètre par défaut.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Vous pouvez également utiliser la fonction resourceGroup pour appliquer des balises du groupe de ressources à une ressource. Pour plus d’informations, voir [Appliquer les balises d’un groupe de ressources](../management/tag-resources.md#apply-tags-from-resource-group).

Quand vous utilisez des modèles imbriqués pour effectuer un déploiement sur plusieurs groupes de ressources, vous pouvez spécifier l’étendue de l’évaluation de la fonction resourceGroup. Pour plus d’informations, voir [Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Exemple de groupe de ressources

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) suivant retourne les propriétés du groupe de ressources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent renvoie un objet dans le format suivant :

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Retourne l'identificateur unique d'une ressource. Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. 

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Non |string (au format GUID) |La valeur par défaut est l’abonnement actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre abonnement. |
| resourceGroupName |Non |string |La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre groupe de ressources. |
| resourceType |Oui |string |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |string |Nom de la ressource. |
| nom_ressource2 |Non |string |Segment de nom de ressource suivant si nécessaire. |

Continuez à ajouter des noms de ressource en paramètres lorsque le type de ressource contient plus de segments.

### <a name="return-value"></a>Valeur retournée

L’ID de la ressource est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Lorsqu’il est utilisé dans un [déploiement au niveau de l’abonnement](deploy-to-subscription.md), l’ID de la ressource est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Pour obtenir l’ID dans d’autres formats, voir :

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Notes

Le nombre de paramètres que vous fournissez varie selon qu’il s'agit d’une ressource parent ou d’une ressource enfant et selon que la ressource fait partie du même abonnement ou du même groupe de ressources.

Pour obtenir l’ID de ressource d’une ressource parent se trouvant dans le même abonnement et le même groupe de ressources, indiquez le type et le nom de la ressource.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Pour obtenir l’ID de ressource d’une ressource enfant, faites attention au nombre de segments dans le type de ressource. Indiquez un nom de ressource pour chaque segment du type de ressource. Le nom du segment correspond à la ressource qui existe pour cette partie de la hiérarchie.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Pour obtenir l’ID de ressource d’une ressource se trouvant dans le même abonnement mais dans un groupe de ressources différent, indiquez le nom du groupe de ressources.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Pour obtenir l’ID de ressource d’une ressource se trouvant dans un abonnement et un groupe de ressources différents, indiquez l’ID d’abonnement et le nom du groupe de ressources.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Souvent, vous devez utiliser cette fonction lorsque vous utilisez un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Exemple d’ID de ressource

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) suivant retourne l’ID de ressource pour un compte de stockage appartenant au groupe de ressources :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Name | Type | Valeur |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

```json
subscription()
```

Retourne des détails concernant l’abonnement pour le déploiement actuel. 

### <a name="return-value"></a>Valeur retournée

La fonction retourne les informations au format suivant :

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Notes

Quand vous utilisez des modèles imbriqués pour effectuer un déploiement sur plusieurs abonnements, vous pouvez spécifier l’étendue de l’évaluation de la fonction subscription. Pour plus d’informations, voir [Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Exemple d’abonnement

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) suivant montre la fonction subscription qui est appelée dans la section outputs. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Retourne l’identificateur unique d’une ressource déployée au niveau de l’abonnement.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Non |string (au format GUID) |La valeur par défaut est l’abonnement actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre abonnement. |
| resourceType |Oui |string |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |string |Nom de la ressource. |
| nom_ressource2 |Non |string |Segment de nom de ressource suivant si nécessaire. |

Continuez à ajouter des noms de ressource en paramètres lorsque le type de ressource contient plus de segments.

### <a name="return-value"></a>Valeur retournée

L'identificateur est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Notes

Utilisez cette fonction pour récupérer l’ID des ressources [déployées dans l’abonnement](deploy-to-subscription.md) plutôt qu’un groupe de ressources. L’ID retourné diffère de la valeur retournée par la fonction [resourceId](#resourceid) en ce qu’il n’inclut pas de valeur de groupe de ressources.

### <a name="subscriptionresourceid-example"></a>Exemple subscriptionResourceID

Le modèle suivant attribue un rôle intégré. Vous pouvez le déployer soit sur un groupe de ressources, soit sur un abonnement. Il utilise la fonction subscriptionResourceId pour récupérer l’ID de ressource pour les rôles intégrés.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Retourne l’identificateur unique d’une ressource déployée au niveau du tenant.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceType |Oui |string |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |string |Nom de la ressource. |
| nom_ressource2 |Non |string |Segment de nom de ressource suivant si nécessaire. |

Continuez à ajouter des noms de ressource en paramètres lorsque le type de ressource contient plus de segments.

### <a name="return-value"></a>Valeur retournée

L'identificateur est retourné au format suivant :

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Notes

Cette fonction permet de récupérer l’ID d’une ressource déployée sur le tenant. L’ID retourné diffère des valeurs retournées par d’autres fonctions d’ID de ressource en ce qu’il n’inclut pas de valeurs de groupe de ressources ou d’abonnement.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](template-syntax.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](create-multiple-instances.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déployer une application avec un modèle Azure Resource Manager](deploy-powershell.md).

