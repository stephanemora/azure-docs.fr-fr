---
title: Clé gérée par le client dans Azure Monitor
description: Informations et étapes relatives à la configuration de CMK (Customer-Managed Key) pour chiffrer des données dans vos espaces de travail Log Analytics à l’aide d’une clé Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 09/09/2020
ms.openlocfilehash: 5d44758ebf94c7487935ef47a17ad810dc5cf9f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657301"
---
# <a name="azure-monitor-customer-managed-key"></a>Clé gérée par le client dans Azure Monitor 

Cet article fournit des informations générales et des étapes pour configurer des clés gérées par le client (CMK, Customer-Managed Keys) qui sont destinées à vos espaces de travail Log Analytics. Une fois la configuration effectuée, toutes les données envoyées à vos espaces de travail sont chiffrées au moyen de votre clé Azure Key Vault.

Nous vous recommandons de passer en revue les [Limitations et contraintes](#limitationsandconstraints) ci-dessous avant de procéder à la configuration.

## <a name="customer-managed-key-cmk-overview"></a>Vue d’ensemble des clés gérées par le client (CMK)

Le [chiffrement au repos](../../security/fundamentals/encryption-atrest.md) est une exigence de sécurité et de confidentialité courante dans les organisations. Vous pouvez laisser Azure gérer complètement le chiffrement au repos, mais vous disposez aussi de plusieurs options pour gérer étroitement le chiffrement ou les clés de chiffrement.

Azure Monitor veille à ce que toutes les données et requêtes enregistrées soient chiffrées au repos à l’aide de clés gérées par Microsoft (MMK). Azure Monitor fournit également une option de chiffrement à l’aide de votre propre clé qui est stockée dans votre [Azure Key Vault](../../key-vault/general/overview.md) et accessible à l’aide de l’authentification par [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) attribuée par le système. Cette clé (CMK) peut être [protégée par un logiciel ou par un HSM matériel](../../key-vault/general/overview.md). L’utilisation du chiffrement par Azure Monitor est identique à celle du [chiffrement par Stockage Azure](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption).

La capacité de clé gérée par le client est fournie sur des clusters Log Analytics dédiés et vous permet de révoquer l’accès à vos données à tout moment et de les protéger avec le contrôle [Lockbox](#customer-lockbox-preview). Pour vérifier que nous disposons de la capacité requise pour un cluster dédié dans votre région, votre abonnement doit nécessairement être autorisé au préalable. Avant de commencer à configurer une clé CMK, demandez à votre contact Microsoft d’autoriser votre abonnement.

Le [modèle de tarification des clusters Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) utilise des réservations de capacité à partir de 1 000 Go/jour.

Les données ingérées au cours des 14 derniers jours sont également conservées dans le cache à chaud (SSD) afin d’optimiser l’utilisation du moteur de requête. Ces données restent chiffrées avec des clés Microsoft, quelle que soit la configuration de clé gérée par le client, mais votre contrôle sur les données SSD est sujet à une [révocation de clé](#cmk-kek-revocation). Nous travaillerons à l’implémentation de données SSD chiffrées avec clé CMK au cours de la seconde moitié de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Fonctionnement de CMK dans Azure Monitor

Azure Monitor tire parti de l’identité managée attribuée par le système pour accorder l’accès à votre coffre de clés Azure. L’identité gérée attribuée par le système ne peut être associée qu’à une seule ressource Azure, tandis que l’identité du cluster Log Analytics est prise en charge au niveau du cluster. Cela exige que la capacité de clé CMK soit fournie sur un cluster Log Analytics dédié. Pour prendre en charge la capacité de clé CMK sur plusieurs espaces de travail, une nouvelle ressource *cluster* Log Analytics s’exécute en tant que connexion d’identité intermédiaire entre votre Key Vault et vos espaces de travail Log Analytics. Le stockage en cluster Log Analytics utilise l’identité gérée associée à la ressource *cluster* pour s’authentifier auprès de votre Azure Key Vault via Azure Active Directory. 

Après la configuration d’une clé CMK, toutes les données ingérées dans les espaces de travail associés à votre ressource *cluster* sont chiffrées à l’aide de la clé stockée dans votre Key Vault. Vous pouvez dissocier des espaces de travail de la ressource *cluster* à tout moment. Les nouvelles données sont ingérées dans un stockage Log Analytics et chiffrées avec une clé Microsoft. Et vous pouvez interroger vos données, nouvelles et anciennes, sans la moindre difficulté.


![Vue d’ensemble de CMK](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Ressource *cluster* Log Analytics ayant une identité gérée avec des autorisations pour Key Vault. L’identité est propagée vers le stockage de cluster Log Analytics dédié sous-jacent.
3. Cluster Log Analytics dédié.
4. Espaces de travail associés à la ressource *cluster* pour le chiffrement par clé CMK.

## <a name="encryption-keys-operation"></a>Opération de clés de chiffrement

Il existe trois types de clés impliquées dans le chiffrement des données de Stockage :

- **KEK** - Clé de chiffrement principale (CMK)
- **AEK** : clé de chiffrement de compte
- **DEK** : clé de chiffrement de données

Les règles suivantes s’appliquent :

- Les comptes de stockage de cluster Log Analytics génèrent une clé de chiffrement unique pour chaque compte de stockage, appelée clé de chiffrement de compte (AEK, Account Encryption Key).

- La clé AEK est utilisé pour dériver les clés DEK, clés utilisées pour chiffrer chaque bloc de données écrites sur le disque.

- Lorsque vous configurez votre clé dans le coffre de clés et que vous la référencez dans la ressource de *cluster*, Stockage Azure envoie des requêtes à votre coffre de clés Azure pour envelopper et désenvelopper l’AEK afin d’effectuer des opérations de chiffrement et de déchiffrement de données.

- Votre clé KEK ne quitte jamais votre coffre de clés et, dans le cas d’une clé HSM, elle ne quitte jamais le matériel.

- Stockage Azure utilise l’identité managée associée à la ressource de *cluster* pour s’authentifier et accéder à Azure Key Vault par le biais d’Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procédure de provisionnement de CMK

1. Autorisation de l’abonnement : la capacité de clé CMK est fournie sur des clusters Log Analytics dédiés. Pour vérifier que nous disposons de la capacité requise dans votre région, votre abonnement doit nécessairement être autorisé au préalable. Demandez à votre contact Microsoft d’autoriser votre abonnement.
2. Création du coffre de clés Azure et stockage de la clé
3. Création d’une ressource de *cluster*
4. Octroi d’autorisations d’accès à votre coffre de clés
5. Association des espaces de travail Log Analytics

La procédure n’est pas prise en charge dans le portail Azure et l’approvisionnement est effectué par le biais de requêtes PowerShell ou REST.

> [!IMPORTANT]
> Toute requête REST doit inclure un jeton d’autorisation du porteur dans son en-tête.

Par exemple :

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer eyJ0eXAiO....
```

Où *eyJ0eXAiO....* représente le jeton d’autorisation complet. 

Vous pouvez obtenir le jeton à l’aide de l’une des méthodes suivantes :

1. Utilisez la méthode [Inscriptions des applications](/graph/auth/auth-concepts#access-tokens).
2. Dans le portail Azure
    1. Accédez au portail Azure pendant que vous êtes dans l’« outil de développement » (F12)
    1. Recherchez la chaîne d’autorisation sous « En-têtes de demande » dans l’une des instances de « batch?api-version ». Elle ressemble à ceci : « authorization: Bearer eyJ0eXAiO....". 
    1. Copiez-la et ajoutez-la à votre appel d’API selon les exemples ci-dessous.
3. Accédez au site de documentation REST Azure. Appuyez sur « Essayer » sur n’importe quelle API et copiez le jeton du porteur.

### <a name="asynchronous-operations-and-status-check"></a>Opérations asynchrones et vérification de l’état

Certaines opérations de cette procédure de configuration s’exécutent de façon asynchrone, car elles ne peuvent pas être effectuées rapidement. Lors de l’utilisation de requêtes REST dans la configuration, la réponse retourne initialement un code d’état HTTP 200 (OK) et un en-tête avec la propriété *Azure-AsyncOperation* lorsqu’elle est acceptée :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Pour vérifier l’état de l’opération asynchrone, envoyez une requête GET dans la valeur d’en-tête *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

La réponse contient des informations sur l’opération et son *état*. Les valeurs possibles sont les suivantes :

L’opération est en cours
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

L’opération de mise à jour de l’identificateur de clé est en cours.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

La suppression de la ressource *cluster* est en cours : lorsque vous supprimez une ressource *cluster* qui contient des espaces de travail associés à des espaces de travail, une opération de dissociation est effectuée pour chacun des espaces de travail dans les opérations asynchrones, qui peut prendre du temps.
Cela ne s’applique pas lorsque vous supprimez un *cluster* sans espace de travail associé. Dans ce cas, la ressource *cluster* est immédiatement supprimée.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

L’opération est terminée
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

L’opération a échoué
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Autorisation de l’abonnement pour le déploiement de la clé CMK

La capacité de clé CMK est fournie sur des clusters Log Analytics dédiés. Pour vérifier que nous disposons de la capacité requise dans votre région, votre abonnement doit nécessairement être autorisé au préalable. Utilisez vos contacts au sein de Microsoft pour fournir vos ID d’abonnement.

> [!IMPORTANT]
> La fonctionnalité CMK est régionale. Vos coffre de clés Azure, ressource de *cluster* et espaces de travail Log Analytics associés doivent se trouver dans la même région, mais ils peuvent être dans des abonnements différents.

### <a name="storing-encryption-key-kek"></a>Stockage de la clé de chiffrement (KEK)

Créez un coffre de clés Azure, ou utilisez-en un existant, pour générer ou importer une clé à utiliser pour le chiffrement des données. Le coffre de clés Azure doit être configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor. Vous pouvez vérifier cette configuration dans les propriétés de votre coffre de clés : les fonctionnalités de *suppression réversible* et de *protection contre la suppression définitive* doivent être activées.

![Paramètres de suppression réversible et de protection contre la suppression définitive](media/customer-managed-keys/soft-purge-protection.png)

Ces paramètres peuvent être mis à jour par le biais de l’interface CLI et de PowerShell :

- [Suppression réversible](../../key-vault/general/soft-delete-overview.md)
- La [protection contre la suppression définitive](../../key-vault/general/soft-delete-overview.md#purge-protection) protège contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

### <a name="create-cluster-resource"></a>Création d’une ressource de *cluster*

Cette ressource est utilisée comme connexion d’identité intermédiaire entre votre coffre de clés et vos espaces de travail Log Analytics. Une fois que vous avez reçu une confirmation que vos abonnements ont été autorisés, créez une ressource *Cluster* Log Analytics dans la région où se trouvent vos espaces de travail.

Vous devez spécifier le niveau (sku) de *réservation de capacité* lors de la création d’une ressource *cluster*. Le niveau de *réservation de capacité* peut varier entre 1000 et 3000 Go par jour, et vous pouvez le modifier ultérieurement par incréments de 100. Si vous avez besoin d’un niveau de réservation de capacité supérieur à 3000 Go par jour, contactez-nous à l’adresse LAIngestionRate@microsoft.com. [En savoir plus](./manage-cost-storage.md#log-analytics-dedicated-clusters)

La propriété *billingType* détermine l’attribution de facturation pour la ressource *cluster* et ses données :
- *Cluster* (par défaut) -- Les coûts de la réservation de capacité pour votre cluster sont attribués à la ressource *Cluster*.
- *Espaces de travail* -- Les coûts de la réservation de capacité pour votre cluster sont attribués proportionnellement aux espaces de travail du cluster. Une partie de l’utilisation est facturée à la ressource *Cluster* si le total des données ingérées pour la journée est inférieur à la réservation de capacité. Pour en savoir plus sur le modèle de tarification du cluster, consultez [Clusters dédiés Log Analytics](manage-cost-storage.md#log-analytics-dedicated-clusters). 

> [!NOTE]
> * Après avoir créé votre ressource *cluster*, vous pouvez la mettre à jour en utilisant la propriété *sku*, *keyVaultProperties* ou *billingType* avec la requête REST PATCH.
> * Vous pouvez actuellement mettre à jour *billingType* à l’aide de la requête REST, ce qui n’est pas pris en charge dans PowerShell.

Cette opération est asynchrone et peut durer un certain temps.

> [!IMPORTANT]
> Copiez et enregistrez la réponse, car vous aurez besoin des détails aux étapes suivantes.
> 

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity daily-ingestion-gigabyte 
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

L’identité est attribuée à la ressource de *cluster* au moment de la création.

**Réponse**

200 OK et en-tête.

L’approvisionnement du cluster Log Analytics prenant du temps, vous pouvez vérifier l’état de l’approvisionnement de deux manières :

1. Copiez la valeur de l’URL Azure-AsyncOperation à partir de la réponse et suivez les instructions de[contrôle de l’état des opérations asynchrones](#asynchronous-operations-and-status-check).
2. Envoyez une requête GET sur la ressource *cluster*, puis examinez la valeur *provisioningState*. L’état indique *ProvisioningAccount* pendant l’approvisionnement et *Succeeded* une fois celui-ci terminé.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

**Réponse**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

Le GUID « principalId » est généré par le service d’identité managée pour la ressource de *cluster*.

### <a name="grant-key-vault-permissions"></a>Octroi d’autorisations d’accès au coffre de clés

Mettez à jour votre coffre de clés avec une nouvelle stratégie d’accès pour accorder des autorisations à votre ressource *Cluster*. Ces autorisations sont utilisées par le Stockage Azure Monitor sous-jacent pour le chiffrement des données. Ouvrez votre coffre de clés dans le portail Azure, puis cliquez sur « Stratégies d’accès », puis sur « + Ajouter une stratégie d’accès » pour créer une stratégie avec les paramètres ci-après :

- Autorisations de clé : sélectionnez « Obtenir », « Inclure la clé » et « Ne pas inclure la clé ».
- Sélectionner le principal : entrez le nom de la ressource *Cluster* ou la valeur principal-id retournée dans la réponse à l’étape précédente.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L’autorisation *Obtenir* est nécessaire pour vérifier que votre coffre de clés est configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Mettre à jour la ressource de cluster avec les détails de l’identificateur de clé

Cette étape est effectuée lors des mises à jour de version de clé initiales et futures dans votre coffre de clés. Elle informe le stockage Azure Monitor sur la version de clé à utiliser pour le chiffrement des données. Une fois mise à jour, votre nouvelle clé est utilisée pour encapsuler et désencapsuler sur la clé de stockage (AEK).

Pour obtenir les détails de l’*identificateur de clé*de votre coffre de clé et mettre à jour la ressource de*cluster*, sélectionnez la version actuelle de votre clé dans Azure Key Vault.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/key-identifier-8bit.png)

Mettez à jour la propriété KeyVaultProperties da la ressource *cluster* avec les détails de l’identificateur de clé.

Cette opération est asynchrone lors de la mise à jour des détails de l’identificateur de clé et peut prendre un certain temps. Elle est synchrone lors de la mise à jour de la valeur Capacité.

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

> [!NOTE]
> Vous pouvez mettre à jour les valeurs *sku*, *keyVaultProperties* ou *billingType* de la ressource *Cluster* à l’aide de l’instruction PATCH.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

« KeyVaultProperties » contient les détails de l'identificateur de clé Key Vault.

**Réponse**

200 OK et en-tête.
La propagation de l’identificateur de clé prend quelques minutes. Vous pouvez vérifier l’état de la mise à jour de deux manières :
1. Copiez la valeur de l’URL Azure-AsyncOperation à partir de la réponse et suivez les instructions de[contrôle de l’état des opérations asynchrones](#asynchronous-operations-and-status-check).
2. Envoyez une requête GET sur la ressource *cluster*, puis examinez les propriétés *KeyVaultProperties*. Les détails de l’identificateur de clé récemment mis à jour doivent être retournés dans la réponse.

Une réponse à la demande de récupération sur la ressource de *cluster* doit ressembler à ceci lorsque la mise à jour de l’identificateur de clé est terminée :

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Association d’espaces de travail à la ressource de *cluster*

Pour effectuer cette opération, vous devez disposer des autorisations « écrire » sur votre espace de travail et la ressource de *cluster*, ce qui implique notamment les actions suivantes :

- Dans l'espace de travail : Microsoft.OperationalInsights/workspaces/write
- Dans la ressource *Cluster* : Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Cette étape ne doit être accomplie qu’une fois l’approvisionnement du cluster Log Analytics terminé. Si vous associez des espaces de travail et ingérez des données avant ce provisionnement, les données ingérées sont définitivement supprimées.

Cette opération est asynchrone et peut durer un certain temps.

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Réponse**

200 OK et en-tête.

Les données ingérées sont stockées chiffrées avec votre clé managée après une opération d’association qui peut prendre jusqu’à 90 minutes. Vous pouvez vérifier l’état de l’association d’espace de travail de deux manières :

1. Copiez la valeur de l’URL Azure-AsyncOperation à partir de la réponse et suivez les instructions de[contrôle de l’état des opérations asynchrones](#asynchronous-operations-and-status-check).
2. Envoyez une requête [Workspaces – Get](/rest/api/loganalytics/workspaces/get) et observez la réponse. L’espace de travail associé présente un clusterResourceId sous « features ».

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

**Réponse**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>Révocation de clé CMK (KEK)

Vous pouvez révoquer l’accès aux données en désactivant votre clé ou en supprimant la stratégie d’accès de la ressource *cluster* dans votre Key Vault. Le stockage de cluster Log Analytics respecte toujours les modifications des autorisations de clé en maximum une heure. Il devient alors indisponible. Toutes les nouvelles données ingérées dans les espaces de travail associés à votre ressource  *cluster* sont définitivement supprimées. Les données sont donc inaccessibles et les requêtes adressées à ces espaces de travail échouent. Les données précédemment ingérées restent dans le stockage tant que votre ressource *cluster* et vos espaces de travail ne sont pas supprimés. Les données inaccessibles sont régies par la stratégie de conservation des données et sont vidées à la fin de la durée de conservation. 

Les données ingérées au cours des 14 derniers jours sont également conservées dans le cache à chaud (SSD) pour optimiser l’utilisation du moteur de requête. Elles sont supprimées lors d’une opération de révocation de clé et devient également inaccessibles.

Le stockage sonde régulièrement votre Key Vault pour tenter de désencapsuler la clé de chiffrement et, une fois qu’il y a accès, l’ingestion et l’interrogation des données reprennent dans un délai de 30 minutes.

## <a name="cmk-kek-rotation"></a>Rotation de clé CMK (KEK)

La rotation de clé CMK nécessite une mise à jour explicite de la ressource *cluster* avec la nouvelle version de clé dans Azure Key Vault. Suivez les instructions de l’étape « Mettre à jour la ressource *cluster* avec les détails de l’identificateur de clé ». Si vous ne mettez pas à jour les nouveaux détails de l’identificateur de clé dans la ressource *cluster*, le stockage de cluster Log Analytics continue d’utiliser votre clé précédente pour le chiffrement. Si vous désactivez ou supprimez votre ancienne clé avant de mettre à jour la nouvelle dans la ressource *cluster*, vous passez à l’état [révocation de clé](#cmk-kek-revocation).

Toutes vos données restent accessibles après l’opération de rotation de clé, car les données sont toujours chiffrées avec la clé de chiffrement de compte (AEK, Account Encryption Key), tandis que celle-ci est désormais chiffrée avec votre nouvelle version de clé de chiffrement de clé (KEK, Key Encryption Key) dans Key Vault.

## <a name="cmk-for-queries"></a>Clé CMK pour les requêtes

Le langage de requête utilisé dans Log Analytics est expressif et peut contenir des informations sensibles dans les commentaires que vous ajoutez aux requêtes ou dans la syntaxe de requête. Certaines organisations requièrent que ces informations soient protégées dans le cadre de la stratégie CMK et vous devez sauvegarder vos requêtes en les chiffrant avec votre clé. Azure Monitor vous permet de stocker des requêtes de *recherches enregistrées* et d’*alertes de journal* chiffrées avec votre clé dans votre propre compte de stockage lorsque vous êtes connecté à votre espace de travail. 

> [!NOTE]
> Les requêtes Log Analytics peuvent être enregistrées dans différents magasins en fonction du scénario utilisé. Les requêtes restent chiffrées avec Microsoft Key (MMK) dans les scénarios suivants, quelle que soit la configuration de CMK : Classeurs dans Azure Monitor, tableaux de bord Azure, application logique Azure, Azure Notebooks et Automation Runbooks.

Lorsque vous apportez votre propre stockage (BYOS) et que vous l’associez à votre espace de travail, le service charge les requêtes de *recherches enregistrées* et d’*alertes de journal*  dans votre compte de stockage. Cela signifie que vous contrôlez le compte de stockage et la [stratégie de chiffrement au repos](../../storage/common/encryption-customer-managed-keys.md) à l’aide de la même clé que celle utilisée pour chiffrer les données dans le cluster Log Analytics ou d’une clé différente. Toutefois, vous êtes responsable des coûts associés à ce compte de stockage. 

**Considérations à prendre en compte avant de définir la clé CMK pour les requêtes**
* Vous devez disposer d’autorisations d’écriture pour votre espace de travail et votre compte de stockage.
* Veillez à créer votre compte de stockage dans la même région que celle où se trouve votre espace de travail Log Analytics.
* Les *recherches enregistrées* en stockage sont considérées comme des artefacts de service et leur format peut changer.
* Les *recherches enregistrées* existantes sont supprimées de votre espace de travail. Copiez toutes les *recherches enregistrées* dont vous avez besoin avant la configuration. Vous pouvez afficher vos *recherches enregistrées* à l’aide de [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch).
* L’historique des requêtes n’est pas pris en charge et vous ne pouvez pas voir les requêtes que vous avez exécutées.
* Vous pouvez associer un seul compte de stockage à l’espace de travail pour l’enregistrement des requêtes, mais il peut être utilisé pour les requêtes de *recherches enregistrées* et d’*alertes de journal*.
* L’action d’épingler au tableau de bord n’est pas prise en charge.

**Configurer BYOS pour les requêtes de recherches enregistrées**

Associez un compte de stockage à *interroger* à votre espace de travail -- les requêtes de *recherches enregistrées* sont enregistrées dans votre compte de stockage. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

Après la configuration, toute nouvelle requête *de recherche enregistrée* sera sauvegardée dans votre stockage.

**Configurer BYOS pour les requêtes d’alertes de journal**

Associez un compte de stockage pour les *alertes* à votre espace de travail. Les requêtes d’*alertes de journal* sont enregistrées dans votre compte de stockage. 

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

Après la configuration, toute nouvelle requête d’alerte sera sauvegardée dans votre stockage.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (préversion)
Lockbox vous permet d’approuver ou de rejeter la demande d’un ingénieur Microsoft d’accéder à vos données lors d’une demande de support.

Dans Azure Monitor, vous disposez de ce contrôle sur les données des espaces de travail associés à votre cluster Log Analytics dédié. Le contrôle Lockbox s’applique aux données stockées dans un cluster Log Analytics dédié dans lequel elles sont maintenues isolées dans les comptes de stockage du cluster configurés dans votre abonnement protégé par Lockbox.  

En savoir plus sur [Customer Lockbox pour Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

## <a name="cmk-management"></a>Gestion de clé CMK

- **Obtenir toutes les ressources *cluster* d’un groupe de ressources**
  
  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Réponse**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **Obtenir toutes les ressources *cluster* d’un abonnement**
  
  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Réponse**
    
  La même réponse que pour les ressources de *cluster* pour un groupe de ressources, mais dans l’étendue d’un abonnement.

- **Mettre à jour la *réservation de capacité* dans une ressource *cluster***

  À mesure que le volume de données de vos espaces de travail associés change au fil du temps, vous souhaitez mettre à jour le niveau de réservation de capacité de manière appropriée. Suivez la [mise à jour de la ressource *cluster*](#update-cluster-resource-with-key-identifier-details) et fournissez votre nouvelle valeur de capacité. Celle-ci peut varier entre 1000 et 3000 Go par jour par incréments de 100. Pour un niveau de réservation de capacité supérieur à 3000 Go par jour, adressez-vous à votre contact Microsoft pour l’activer. Notez que vous n’avez pas besoin de fournir le corps entier de la requête REST et que vous devez inclure la propriété sku :

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity "daily-ingestion-gigabyte"
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Mettre à jour la propriété *billingType* dans la ressource *cluster***

  La propriété *billingType* détermine l’attribution de facturation pour la ressource *cluster* et ses données :
  - *cluster* (par défaut) : la facturation est attribuée à l’abonnement hébergeant votre ressource cluster.
  - *workspaces* : la facturation est attribuée de façon proportionnée aux abonnements hébergeant vos espaces de travail.
  
  Suivez la [mise à jour de la ressource *cluster*](#update-cluster-resource-with-key-identifier-details) et fournissez votre nouvelle valeur de billingType. Notez que vous n’avez pas besoin de fournir tout le corps de la requête REST et que vous devez inclure la propriété *billingType* :

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Dissocier l’espace de travail**

  Pour effectuer cette opération, vous devez disposer des autorisations d’écriture sur l’espace de travail et la ressource *cluster*. Vous pouvez à tout moment dissocier un espace de travail de votre ressource *cluster*. Les nouvelles données ingérées après l’opération de dissociation sont stockées dans le stockage Log Analytics et chiffrées avec une clé Microsoft. Vous pouvez interroger les données ingérées dans votre espace de travail avant et après la dissociation en toute facilité tant que la ressource *cluster* est approvisionnée et configurée avec une clé de Key Vault valide.

  Cette opération est asynchrone et peut durer un certain temps.

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Réponse**

  200 OK et en-tête.

  Les données ingérées après l’opération de dissociation sont stockées dans le stockage Log Analytics. Cela peut prendre jusqu’à 90 minutes. Vous pouvez vérifier l’état de dissociation de l’espace de travail de deux manières :

  1. Copiez la valeur de l’URL Azure-AsyncOperation à partir de la réponse et suivez les instructions de[contrôle de l’état des opérations asynchrones](#asynchronous-operations-and-status-check).
  2. En envoyant une requête [Workspaces – Get](/rest/api/loganalytics/workspaces/get) et en observant la réponse. L’espace de travail dissocié n’a pas d’identificateur *clusterResourceId* sous *features*.

- **Vérifier l’état de l’association de l’espace de travail**
  
  Effectuez une opération Get sur l’espace de travail et observez si la propriété *clusterResourceId* est présente dans la réponse sous *Fonctionnalités*. L’espace de travail associé présentera la propriété *clusterResourceId*.

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Supprimer votre ressource *cluster***

  Pour effectuer cette opération, vous devez disposer des autorisations d’écriture sur la ressource *cluster*. Une opération de suppression réversible est effectuée pour permettre la récupération de votre ressource de *cluster*, y compris ses données, dans un délai de 14 jours, que la suppression ait été accidentelle ou intentionnelle. Le nom de la ressource de *cluster* reste réservé pendant la période de suppression réversible et vous ne pouvez pas l’utiliser pour un autre cluster. À l’issue la période de suppression réversible, le nom de la ressource *cluster* redevient disponible, et votre ressource *cluster* ainsi que ses données sont définitivement supprimés. Tout espace de travail associé est dissocié de la ressource *cluster* lors de l’opération de suppression. Les nouvelles données ingérées sont stockées dans le stockage Log Analytics et chiffrées avec une clé Microsoft. 
  
  L’opération de dissociation des espaces de travail est asynchrone et peut prendre jusqu’à 90 minutes.

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Réponse**

  200 OK

- **Récupérer votre ressource *cluster* et vos données** 
  
  Une ressource *cluster* supprimée au cours des 14 derniers jours est dans un état de suppression réversible. Il est possible de la récupérer avec ses données. Étant donné que tous les espaces de travail ont été dissociés de la ressource *cluster* suite à la suppression de celle-ci, vous devez réassocier vos espaces de travail après la récupération du chiffrement par clé CMK. Actuellement, cette opération de récupération est effectuée manuellement par le groupe de produits. Utilisez votre canal Microsoft pour les demandes de récupération.

## <a name="limitationsandconstraints"></a>Limitations et contraintes

- La clé CMK est prise en charge sur un cluster Log Analytics dédié, et convient pour des clients qui envoient au moins 1 To par jour.

- Le nombre maximal de ressources *cluster* par région et abonnement est de 2.

- Vous pouvez associer un espace de travail à votre ressource *cluster*, puis le dissocier si une clé CMK n’est pas requise pour l’espace de travail. Le nombre d’associations d’espace de travail sur un espace de travail particulier sur une période de 30 jours est limité à 2.

- Vous ne devez associer un espace de travail à une ressource *cluster* qu’APRÈS avoir vérifié que l’approvisionnement du cluster Log Analytics est terminé. Les données envoyées à votre espace de travail avant la fin de l’approvisionnement seront définitivement supprimées.

- Le chiffrement CMK s’applique aux données nouvellement ingérées après la configuration de CMK. Les données qui ont été ingérées avant la configuration de CMK demeurent chiffrées avec la clé Microsoft. Vous pouvez interroger les données ingérées avant et après la configuration de CMK de manière fluide.

- Le coffre de clés Azure doit être configuré comme récupérable. Les propriétés ci-après, qui ne sont pas activées par défaut, doivent être configurées à l’aide de l’interface CLI ou de PowerShell :<br>
  - [Suppression réversible](../../key-vault/general/soft-delete-overview.md)
  - La [protection contre le vidage](../../key-vault/general/soft-delete-overview.md#purge-protection) doit être activée pour bénéficier d’une protection contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

- Le déplacement de la ressource de *cluster* vers un autre groupe de ressources ou abonnement n’est pas pris en charge.

- Votre Azure Key Vault, la ressource de *cluster* et les espaces de travail associés doivent se trouver dans la même région et dans le même locataire Azure Active Directory (Azure AD) mais peuvent être liés à des abonnements différents.

- L’association d’un espace de travail à la ressource de *cluster* échoue s’il est associé à une autre ressource de *cluster*

## <a name="troubleshooting"></a>Dépannage

- Comportement avec disponibilité du Key Vault
  - En temps normal, le Stockage met en cache la clé AEK pendant de courtes périodes et revient régulièrement dans Key Vault pour la désencapsulation.
    
  - Erreurs de connexion temporaires : le Stockage gère les erreurs temporaires (délais d’attente, échecs de connexion, problèmes DNS) en autorisant les clés à rester en cache pendant un peu plus de temps, compensant toute courte période d’indisponibilité. Les fonctionnalités de requête et d’ingestion se poursuivent sans interruption.
    
  - Une indisponibilité du site actif d’environ 30 minutes entraîne l’indisponibilité du compte de stockage. La fonctionnalité de requête est indisponible et les données ingérées sont mises en cache pendant plusieurs heures à l’aide de la clé Microsoft pour éviter la perte de données. Quand l’accès à Key Vault est restauré, la requête est disponible et les données mises en cache temporaires sont ingérées dans le magasin de données et chiffrées avec CMK.

  - Taux d’accès au Key Vault : la fréquence à laquelle le stockage Azure Monitor accède au Key Vault pour les opérations d’encapsulation (wrap) et de désencapsulation (unwrap) est comprise entre 6 et 60 secondes.

- Si vous créez une ressource de *cluster* et spécifiez la propriété KeyVaultProperties immédiatement, l’opération peut échouer, car la stratégie d’accès ne peut pas être définie tant que l’identité système n’a pas été attribuée à la *ressource* cluster.

- Si vous mettez à jour la ressource de *cluster* existante avec KeyVaultProperties et que la stratégie d’accès de clé « Obtenir » est manquante dans Key Vault, l’opération échoue.

- Si vous recevez une erreur de conflit lors de la création d’une ressource de *cluster*, il est possible que vous ayez supprimé votre ressource de *cluster* au cours des derniers 14 jours et qu’elle se trouve dans une période de suppression réversible. Le nom de la ressource de *cluster* reste réservé pendant la période de suppression réversible et vous ne pouvez pas l’utiliser pour un autre cluster. Le nom est libéré après la période de suppression réversible, lorsque la ressource de *cluster* est supprimée définitivement.

- Si vous mettez à jour votre ressource de *cluster* pendant qu’une opération est en cours, l’opération échoue.

- Si vous ne parvenez pas à déployer votre ressource *cluster*, vérifiez que votre Azure Key Vault, votre ressource  *cluster* et les espaces de travail Log Analytics associés se trouvent dans la même région. Ils peuvent être liés à des abonnements différents.

- Si vous mettez à jour votre version de clé dans Key Vault et ne mettez pas à jour les détails de l’identificateur de clé dans la ressource *cluster*, le cluster Log Analytics continue d’utiliser votre clé précédente et vos données deviennent inaccessibles. Mettez à jour les nouveaux détails de l’identificateur de clé dans la ressource *cluster* pour reprendre l’ingestion des données et avoir la possibilité d’interroger les données.

- Certaines opérations sont longues et peuvent prendre un certain temps. Il s’agit des opérations *Cluster* create, *Cluster* key update et *Cluster* delete. Vous pouvez vérifier l’état de l’opération de deux manières :
  1. Lorsque vous utilisez REST, copiez la valeur de l’URL Azure-AsyncOperation à partir de la réponse et suivez les instructions de[vérification de l’état des opérations asynchrones](#asynchronous-operations-and-status-check).
  2. Envoyez une requête GET au *cluster* ou à l’espace de travail du cluster et observez la réponse. Par exemple, l’espace de travail dissocié n’a pas de *clusterResourceId* sous *Fonctionnalités*.

- Pour obtenir du support et de l’aide concernant la clé gérée par le client, utilisez vos contacts dans Microsoft.

- Messages d’erreur
  
  Création d’une ressource *cluster* :
  -  400 -- Le nom du cluster n’est pas valide. Le nom du cluster peut contenir les caractères a-z, A-Z, 0-9 et doit compter entre 3 et 63 caractères.
  -  400 -- Le corps de la demande a la valeur null ou est dans un format incorrect.
  -  400 -- Nom de SKU non valide. Définissez le nom de la SKU sur capacityReservation.
  -  400 -- La capacité a été fournie, mais la référence SKU n’est pas capacityReservation. Définissez le nom de la SKU sur capacityReservation.
  -  400 -- Xapacité manquante dans la SKU. Définissez la valeur de capacité sur 1 000 ou plus par degrés de 100 (Go).
  -  400 -- La capacité dans la SKU n’est pas comprise dans la plage. Doit être au minimum de 1 000 et jusqu’à la capacité maximale autorisée, disponible sous « Utilisation et coût estimé » dans votre espace de travail.
  -  400 -- La capacité est verrouillée pendant 30 jours. La réduction de la capacité est autorisée 30 jours après la mise à jour.
  -  400 -- Aucune SKU n’a été définie. Définissez le nom de la SKU sur capacityReservation et la valeur de capacité sur 1 000 ou plus par degrés de 100 (Go).
  -  400 -- L’identité a la valeur nul ou est vide. Définissez l’identité avec le type systemAssigned.
  -  400 -- Les KeyVaultProperties sont définies à la création. Mettez à jour les KeyVaultProperties après la création du cluster.
  -  400 -- Impossible d’exécuter une opération pour le moment. L’opération asynchrone est dans un état autre que réussi. Le cluster doit effectuer cette opération avant l’exécution d’une opération de mise à jour.

  Mise à jour d’une ressource de *Cluster*
  -  400 -- Le cluster est en cours de suppression. L’opération asynchrone est en cours. Le cluster doit effectuer cette opération avant l’exécution d’une opération de mise à jour.
  -  400 -- Les KeyVaultProperties ne sont pas vides, mais leur format est incorrect. Consultez [mise à jour de l’identificateur de la clé](#update-cluster-resource-with-key-identifier-details).
  -  400 -- Échec de validation de la clé dans Key Vault. Peut être dû à un manque d’autorisations ou à l’inexistence de la clé. Vérifiez que vous [avez défini la clé et la stratégie d’accès](#grant-key-vault-permissions) dans Key Vault.
  -  400 -- La clé n’est pas récupérable. La suppression réversible et la protection contre le vidage doivent être définis pour Key Vault. Consulter la [documentation sur Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 -- Impossible d’exécuter une opération pour le moment. Attendez que l’opération asynchrone se termine et réessayez.
  -  400 -- Le cluster est en cours de suppression. Attendez que l’opération asynchrone se termine et réessayez.

    Ressource de *cluster* Obtention :
    -  404 -- Cluster introuvable, le cluster a peut-être été supprimé. Si vous essayez de créer un cluster portant ce nom et que cela génère un conflit, le cluster est supprimé de manière réversible pendant 14 jours. Vous pouvez contacter le support technique pour le récupérer ou utiliser un autre nom pour créer un nouveau cluster. 

  Ressource de *cluster* Suppression
    -  409 -- Impossible de supprimer un cluster en état d’approvisionnement. Attendez que l’opération asynchrone se termine et réessayez.

  Association de l’espace de travail :
  -  404 -- Espace de travail introuvable. L’espace de travail que vous avez spécifié n’existe pas ou a été supprimé.
  -  409 -- Opération d’association ou de dissociation de l’espace de travail en cours.
  -  400 -- Cluster introuvable, le cluster que vous avez spécifié n’existe pas ou a été supprimé. Si vous essayez de créer un cluster portant ce nom et que cela génère un conflit, le cluster est supprimé de manière réversible pendant 14 jours. Contactez le support technique pour le récupérer.

  Dissociation de l’espace de travail :
  -  404 -- Espace de travail introuvable. L’espace de travail que vous avez spécifié n’existe pas ou a été supprimé.
  -  409 -- Opération d’association ou de dissociation de l’espace de travail en cours.
