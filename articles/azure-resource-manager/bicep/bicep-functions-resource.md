---
title: Fonctions Bicep - Ressources
description: Décrit les fonctions à utiliser dans un fichier Bicep pour récupérer les valeurs relatives aux ressources.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: e52f46cf6b29af491f3542d13e360ef936251af6
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026361"
---
# <a name="resource-functions-for-bicep"></a>Fonctions de ressources pour Bicep

Resource Manager propose les fonctions suivantes pour obtenir les valeurs des ressources dans votre fichier Bicep :

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [pickZones](#pickzones)
* [reference](#reference)
* [resourceId](#resourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Pour obtenir les valeurs du déploiement actuel, consultez [Fonctions de valeur de déploiement](./bicep-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Retourne l’ID d’une [ressource d’extension](../management/extension-resource-types.md), un type de ressource qui s’applique à une autre ressource pour y ajouter des fonctionnalités.

La fonction extensionResourceId est disponible dans les fichiers Bicep, mais vous n’en avez généralement pas besoin. Utilisez plutôt le nom symbolique de la ressource et accédez à la propriété `id`.

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

Une définition de stratégie personnalisée déployée sur un groupe d’administration est implémentée en tant que ressource d’extension. Pour créer et attribuer une stratégie, déployez le fichier Bicep suivant dans un groupe d’administration.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

---

Les définitions de stratégie intégrées sont des ressources de niveau locataire. Pour obtenir un exemple de déploiement d’une définition de stratégie intégrée, consultez [tenantResourceId](#tenantresourceid).

## <a name="getsecret"></a>getSecret

`getSecret([secretName])`

Renvoie la valeur du secret stockée dans Azure Key Vault. Vous pouvez utiliser la fonction getSecret pour obtenir un secret de coffre de clés et transmettre la valeur renvoyée à un paramètre de chaîne de module bicep. La fonction getSecret peut uniquement être appelée dans une ressource `Microsoft.KeyVault/vaults` et utilisée avec un paramètre associé à un élément décoratif `@secure()`.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| secretName | Oui | string | Nom du secret stocké dans un coffre de clés. |

### <a name="return-value"></a>Valeur retournée

Valeur du secret pour le nom du secret.

### <a name="example"></a>Exemple

Le fichier Bicep suivant est utilisé en tant que module.  Il est doté d’un paramètre *adminPassword* défini avec l’élément décoratif `@secure()`.

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  ...
}
```

Le fichier Bicep suivant utilise le fichier Bicep précédent en tant que module. Le fichier Bicep référence un coffre de clés existant, appelle la fonction `getSecret` pour récupérer le secret du coffre de clés, puis passe la valeur en tant que paramètre au module.

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

La syntaxe de cette fonction varie en fonction du nom des opérations de liste. Chaque implémentation retourne des valeurs pour le type de ressource qui prend en charge une opération de liste. Le nom de l’opération doit commencer par `list` et peut avoir un suffixe. Voici quelques utilisations courantes : `list`, `listKeys`, `listKeyValue` et `listSecrets`.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |string |Identificateur unique pour la ressource. |
| apiVersion |Oui |string |Version d'API de l'état d'exécution des ressources. En règle générale, au format, **aaaa-mm-jj**. |
| functionValues |Non |object | Objet qui contient les valeurs de la fonction. Fournissez uniquement cet objet pour les fonctions qui prennent en charge la réception d’un objet avec des valeurs de paramètre, comme **listAccountSas** sur un compte de stockage. Un exemple de transmission de valeurs de fonction est illustré dans cet article. |

### <a name="valid-uses"></a>Utilisations valides

Les fonctions de liste peuvent être utilisés dans les propriétés d’une définition de ressource. N’utilisez pas de fonction de liste qui expose des informations sensibles dans la section de sortie d’un fichier Bicep. Les valeurs de sortie sont stockées dans l’historique de déploiement et peuvent être récupérées par un utilisateur malveillant.

Lorsqu’elles sont utilisées avec une [boucle de propriété](./loop-properties.md), vous pouvez utiliser les fonctions list pour `input`, car l’expression est affectée à la propriété de ressource. Vous ne pouvez pas les utiliser avec `count`, car le nombre doit être déterminé avant que la fonction list ne soit résolue.

### <a name="implementations"></a>Implémentations

Les utilisations possibles de list* sont indiquées dans le tableau suivant.

| Type de ressource | Nom de la fonction |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft.ApiManagement/service/subscriptions | [listSecrets](/rest/api/apimanagement/2019-12-01/subscription/listsecrets) |
| Microsoft.AppConfiguration/configurationStores | [Listkeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/2020-09-01/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/2020-09-01/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-03-15/notebookworkspaces/listconnectioninfo) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
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
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listauthkeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
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

D’autres fonctions de liste ont différents formats de retour. Pour afficher le format d’une fonction, incluez-le dans la section des sorties comme indiqué dans l’exemple de fichier Bicep.

### <a name="remarks"></a>Notes

Spécifiez la ressource en utilisant le nom de la ressource ou la [fonction resourceId](#resourceid). Lorsque vous utilisez une fonction de liste dans le fichier Bicep qui déploie la ressource référencée, utilisez le nom de la ressource.

Si vous utilisez une fonction **list** dans une ressource qui est déployée conditionnellement, la fonction est évaluée même si la ressource n’est pas déployée. Vous obtenez une erreur si la fonction **list** fait référence à une ressource qui n’existe pas. Utilisez l’opérateur [d’expression conditionnelle **?:** ](./operators-logical.md#conditional-expression--) pour vous assurer que la fonction est uniquement évaluée lorsque la ressource est déployée.

### <a name="list-example"></a>Exemple de liste

L’exemple suivant utilise listKeys lors de la définition d’une valeur pour [les scripts de déploiement](../templates/deployment-script-template.md).

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}
```

L’exemple suivant montre une fonction de liste qui accepte un paramètre. Dans ce cas, la fonction est toujours **listAccountSas**. Passez un objet pour l’heure d’expiration. L’heure d’expiration doit être dans le futur.

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Détermine si un type de ressource prend en charge les zones pour une région.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| espacedenoms_fournisseur | Oui | string | Espace de noms du fournisseur du type de ressource pour lequel la prise en charge des zones doit être vérifiée. |
| resourceType | Oui | string | Type de ressource pour lequel la prise en charge des zones doit être vérifiée. |
| location | Oui | string | Région pour laquelle la prise en charge des zones doit être vérifiée. |
| numberOfZones | Non | entier | Nombre de zones logiques à retourner. La valeur par défaut est 1. Le nombre doit être un entier positif compris entre 1 et 3.  Utilisez 1 pour les ressources à une seule zone. Pour les ressources multizones, la valeur doit être inférieure ou égale au nombre de zones prises en charge. |
| offset | Non | entier | Décalage par rapport à la zone logique de départ. La fonction retourne une erreur si le décalage plus le nombre de zones (numberOfZones) dépasse le nombre de zones prises en charge. |

### <a name="return-value"></a>Valeur retournée

Tableau avec les zones prises en charge. Quand les valeurs par défaut pour offset et numberOfZones sont utilisées, un type de ressource et une région qui prend en charge les zones retournent le tableau suivant :

```json
[
    "1"
]
```

Quand le paramètre `numberOfZones` est défini sur 3, il retourne :

```json
[
    "1",
    "2",
    "3"
]
```

Quand le type de ressource ou la région ne prend pas en charge les zones, un tableau vide est retourné.

```json
[
]
```

### <a name="pickzones-example"></a>Exemple de pickZones

Le fichier Bicep suivant présente trois résultats pour l’utilisation de la fonction pickZones.

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

La sortie des exemples précédents retourne trois tableaux.

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| supported | tableau | [ "1" ] |
| notSupportedRegion | tableau | [] |
| notSupportedType | tableau | [] |

Vous pouvez vous servir de la réponse de pickZones pour déterminer s’il convient de fournir une valeur null pour des zones ou affecter des machines virtuelles à différentes zones.

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Renvoie un objet représentant l’état d’exécution d’une ressource.

La fonction de référence est disponible dans les fichiers Bicep, mais vous n’en avez généralement pas besoin. Utilisez plutôt le nom symbolique de la ressource.

L’exemple suivant déploie un compte de stockage. Il utilise le nom symbolique `stg` du compte de stockage pour renvoyer une propriété.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Pour obtenir une propriété de ressource existante non déployée dans le modèle, utilisez le mot clé `existing` :

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
output blobAddress string = stg.properties.primaryEndpoints.blob
```

Pour plus d’informations, consultez [Ressources de référence](./compare-template-syntax.md#reference-resources) et la [fonction de référence du modèle JSON](../templates/template-functions-resource.md#reference).


## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Retourne l'identificateur unique d'une ressource.

La fonction resourceId est disponible dans les fichiers Bicep, mais vous n’en avez généralement pas besoin. Utilisez plutôt le nom symbolique de la ressource et accédez à la propriété `id`.

Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou n’est pas approvisionné dans le même fichier Bicep. Le format de l’identificateur retourné varie selon que le déploiement se produit à l’échelle d’un groupe de ressources, d’un abonnement, d’un groupe d’administration ou d’un locataire.

Par exemple :

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageID string = stg.id
```

Pour obtenir l’ID d’une ressource non déployée dans le fichier Bicep, utilisez le mot clé existant.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
    name: storageAccountName
}

output storageID string = stg.id
```

Pour plus d'informations, consultez la [fonction resourceId du modèle JSON](../templates/template-functions-resource.md#resourceid).

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Retourne l’identificateur unique d’une ressource déployée au niveau de l’abonnement.

La fonction subscriptionResourceId est disponible dans les fichiers Bicep, mais vous n’en avez généralement pas besoin. Utilisez plutôt le nom symbolique de la ressource et accédez à la propriété `id`.

L'identificateur est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Notes

Utilisez cette fonction pour récupérer l’ID des ressources [déployées dans l’abonnement](deploy-to-subscription.md) plutôt qu’un groupe de ressources. L’ID retourné diffère de la valeur retournée par la fonction [resourceId](#resourceid) en ce qu’il n’inclut pas de valeur de groupe de ressources.

### <a name="subscriptionresourceid-example"></a>Exemple subscriptionResourceID

Le fichier Bicep suivant attribue un rôle intégré. Vous pouvez le déployer soit sur un groupe de ressources, soit sur un abonnement. Il utilise la fonction subscriptionResourceId pour récupérer l’ID de ressource pour les rôles intégrés.

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Retourne l’identificateur unique d’une ressource déployée au niveau du tenant.

La fonction tenantResourceId est disponible dans les fichiers Bicep, mais vous n’en avez généralement pas besoin. Utilisez plutôt le nom symbolique de la ressource et accédez à la propriété `id`.

L'identificateur est retourné au format suivant :

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Les définitions de stratégie intégrées sont des ressources de niveau locataire. Pour déployer une attribution de stratégie qui fait référence à une définition de stratégie intégrée, utilisez la fonction tenantResourceId.

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).
* Pour itérer un nombre de fois spécifié lors de la création d’un type de ressource, consultez [Déployer plusieurs instances de ressources dans Bicep](./loop-resources.md).
* Pour découvrir comment déployer le fichier Bicep que vous avez créé, consultez [Déployer des ressources avec Bicep et Azure PowerShell](./deploy-powershell.md).
