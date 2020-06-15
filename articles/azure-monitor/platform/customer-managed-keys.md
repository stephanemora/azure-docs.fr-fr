---
title: Clé gérée par le client dans Azure Monitor
description: Informations et étapes relatives à la configuration de CMK (Customer-Managed Key) pour chiffrer des données dans vos espaces de travail Log Analytics à l’aide d’une clé Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 05/20/2020
ms.openlocfilehash: 8bff8cf1111675446c1c9fb2e5dde8b19e2ef5c1
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310884"
---
# <a name="azure-monitor-customer-managed-key"></a>Clé gérée par le client dans Azure Monitor 

Cet article fournit des informations générales et des étapes pour configurer des clés gérées par le client (CMK, Customer-Managed Keys) qui sont destinées à vos espaces de travail Log Analytics. Une fois la configuration effectuée, toutes les données envoyées à vos espaces de travail sont chiffrées au moyen de votre clé Azure Key Vault.

Nous vous recommandons de passer en revue les [Limitations et contraintes](#limitationsandconstraints) ci-dessous avant de procéder à la configuration.

## <a name="customer-managed-key-cmk-overview"></a>Vue d’ensemble des clés gérées par le client (CMK)

Le [chiffrement au repos](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) est une exigence de sécurité et de confidentialité courante des organisations. Si vous pouvez laisser Azure gérer complètement le chiffrement au repos, plusieurs options vous permettent de le gérer vous-même, ainsi que les clés de chiffrement.

Azure Monitor garantit que toutes les données sont chiffrées au repos à l’aide de clés gérées par Azure. Azure Monitor vous permet également de chiffrer les données à l’aide de votre propre clé stockée dans votre  [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), à laquelle vous pouvez accéder via le stockage en vous authentifiant à l’aide d’une  [identité gérée](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) attribuée par le système. Cette clé peut être  [protégée par un module de sécurité HSM matériel ou logiciel](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Le chiffrement d’Azure Monitor opère de façon similaire au  [chiffrement du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption).

Une clé CMK vous permet de contrôler l’accès à vos données et de le révoquer à tout moment. Le stockage Azure Monitor respecte toujours les modifications des autorisations de clé en l’espace d’une heure. Les données ingérées au cours des 14 derniers jours sont également conservées dans le cache à chaud (SSD) afin d’optimiser l’utilisation du moteur de requête. Ces données restent chiffrées avec des clés Microsoft, quelle que soit la configuration de clé CMK, mais votre contrôle sur les données SSD est sujet à une  [révocation de clé](#cmk-kek-revocation). Nous travaillerons à l’implémentation de données SSD chiffrées avec clé CMK au cours de la seconde moitié de 2020.

La capacité de clé CMK est fournie sur des clusters Log Analytics dédiés. Pour vérifier que nous disposons de la capacité requise dans votre région, votre abonnement doit nécessairement être mis en liste verte au préalable. Avant de commencer à configurer une clé CMK, demandez à votre contact Microsoft de mettre votre abonnement en liste verte.

Le  [modèle de tarification des clusters Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)utilise des réservations de capacité à partir de 1000 Go/jour.

## <a name="how-cmk-works-in-azure-monitor"></a>Fonctionnement de CMK dans Azure Monitor

Azure Monitor tire parti de l’identité managée attribuée par le système pour accorder l’accès à votre coffre de clés Azure. L’identité gérée attribuée par le système ne peut être associée qu’à une seule ressource Azure, tandis que l’identité du cluster Log Analytics est prise en charge au niveau du cluster. Cela exige que la capacité de clé CMK soit fournie sur un cluster Log Analytics dédié. Pour prendre en charge la capacité de clé CMK sur plusieurs espaces de travail, une nouvelle ressource *cluster* Log Analytics s’exécute en tant que connexion d’identité intermédiaire entre votre Key Vault et vos espaces de travail Log Analytics. Le stockage en cluster Log Analytics utilise l’identité gérée associée à la ressource *cluster* pour s’authentifier auprès de votre Azure Key Vault via Azure Active Directory. 

Après la configuration d’une clé CMK, toutes les données ingérées dans les espaces de travail associés à votre ressource *cluster* sont chiffrées à l’aide de la clé stockée dans votre Key Vault. Vous pouvez dissocier des espaces de travail de la ressource *cluster* à tout moment. Les nouvelles données sont ingérées dans un stockage Log Analytics et chiffrées avec une clé Microsoft. Et vous pouvez interroger vos données, nouvelles et anciennes, sans la moindre difficulté.


![Vue d’ensemble de CMK](media/customer-managed-keys/cmk-overview-8bit.png)

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

1. Mise en liste verte d’abonnement : la capacité de clé CMK est fournie sur des clusters Log Analytics dédiés. Pour vérifier que nous disposons de la capacité requise dans votre région, votre abonnement doit nécessairement être mis en liste verte au préalable. Demandez à votre contact Microsoft de mettre votre abonnement en liste verte.
2. Création du coffre de clés Azure et stockage de la clé
3. Création d’une ressource de *cluster*
4. Octroi d’autorisations d’accès à votre coffre de clés
5. Association des espaces de travail Log Analytics

La procédure n’étant pas prise en charge dans l’interface utilisateur, le processus de provisionnement est effectué par le biais de l’API REST.

> [!IMPORTANT]
> Toute demande d’API doit inclure un jeton d’autorisation du porteur dans l’en-tête de la demande.

Par exemple :

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Où *eyJ0eXAiO....* représente le jeton d’autorisation complet. 

Vous pouvez obtenir le jeton à l’aide de l’une des méthodes suivantes :

1. Utilisez la méthode [Inscriptions des applications](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens).
2. Dans le portail Azure
    1. Accédez au portail Azure pendant que vous êtes dans l’« outil de développement » (F12)
    1. Recherchez la chaîne d’autorisation sous « En-têtes de demande » dans l’une des instances de « batch?api-version ». Elle ressemble à ceci : « authorization: Bearer eyJ0eXAiO....". 
    1. Copiez-la et ajoutez-la à votre appel d’API selon les exemples ci-dessous.
3. Accédez au site de documentation REST Azure. Appuyez sur « Essayer » sur n’importe quelle API et copiez le jeton du porteur.

### <a name="asynchronous-operations-and-status-check"></a>Opérations asynchrones et vérification de l’état

Certaines opérations de cette procédure de configuration s’exécutent de façon asynchrone, car elles ne peuvent pas être effectuées rapidement. La réponse pour l’opération asynchrone retourne initialement un code d’état HTTP 200 (OK) et un en-tête avec la propriété *Azure-AsyncOperation* lorsqu’elle est acceptée :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Pour vérifier l’état de l’opération asynchrone, envoyez une requête GET dans la valeur d’en-tête *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
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

### <a name="subscription-whitelisting"></a>Mise en liste verte des abonnements

La capacité de clé CMK est fournie sur des clusters Log Analytics dédiés. Pour vérifier que nous disposons de la capacité requise dans votre région, votre abonnement doit nécessairement être mis en liste verte au préalable. Utilisez vos contacts au sein de Microsoft pour fournir vos ID d’abonnement.

> [!IMPORTANT]
> La fonctionnalité CMK est régionale. Vos coffre de clés Azure, ressource de *cluster* et espaces de travail Log Analytics associés doivent se trouver dans la même région, mais ils peuvent être dans des abonnements différents.

### <a name="storing-encryption-key-kek"></a>Stockage de la clé de chiffrement (KEK)

Créez un coffre de clés Azure, ou utilisez-en un existant, pour générer ou importer une clé à utiliser pour le chiffrement des données. Le coffre de clés Azure doit être configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor. Vous pouvez vérifier cette configuration dans les propriétés de votre coffre de clés : les fonctionnalités de *suppression réversible* et de *protection contre la suppression définitive* doivent être activées.

![Paramètres de suppression réversible et de protection contre la suppression définitive](media/customer-managed-keys/soft-purge-protection.png)

Les paramètres suivants sont disponibles par le biais de l’interface CLI et de PowerShell :
- [Suppression réversible](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- La [protection contre la suppression définitive](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) protège contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

### <a name="create-cluster-resource"></a>Création d’une ressource de *cluster*

Cette ressource est utilisée comme connexion d’identité intermédiaire entre votre coffre de clés et vos espaces de travail Log Analytics. Une fois que vous avez reçu une confirmation que vos abonnements ont été ajoutés à la liste verte, créez une ressource de *cluster* Log Analytics dans la région où se trouvent vos espaces de travail.

Vous devez spécifier le niveau (sku) de *réservation de capacité* lors de la création d’une ressource *cluster*. Le niveau de *réservation de capacité* peut varier entre 1 000 et 2 000 Go par jour, et vous pouvez le modifier ultérieurement par incréments de 100. Si vous avez besoin d’un niveau de réservation de capacité supérieur à 2 000 Go par jour, contactez-nous à l’adresse LAIngestionRate@microsoft.com. [En savoir plus](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

La propriété *billingType* détermine l’attribution de facturation pour la ressource *cluster* et ses données :
- *Cluster* (par défaut) -- Les coûts de la réservation de capacité pour votre cluster sont attribués à la ressource *Cluster*.
- *Espaces de travail* -- Les coûts de la réservation de capacité pour votre cluster sont attribués proportionnellement aux espaces de travail du cluster. Une partie de l’utilisation est facturée à la ressource *Cluster* si le total des données ingérées pour la journée est inférieur à la réservation de capacité. Pour en savoir plus sur le modèle de tarification du cluster, consultez [Clusters dédiés Log Analytics](manage-cost-storage.md#log-analytics-dedicated-clusters). 

> [!NOTE]
> Après avoir créé votre ressource *cluster*, vous pouvez la mettre à jour en utilisant la propriété *sku*, *keyVaultProperties* ou *billingType* avec la requête REST PATCH.

**Créer**

Cette requête Resource Manager est asynchrone.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Copiez et enregistrez la réponse, car vous aurez besoin des détails aux étapes suivantes.

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
    "clusterType": "LogAnalytics",
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

Mettez à jour votre coffre de clés avec une nouvelle stratégie d’accès qui accorde des autorisations à votre ressource de *cluster*. Ces autorisations sont utilisées par le Stockage Azure Monitor sous-jacent pour le chiffrement des données. Ouvrez votre coffre de clés dans le portail Azure, puis cliquez sur « Stratégies d’accès », puis sur « + Ajouter une stratégie d’accès » pour créer une stratégie avec les paramètres ci-après :

- Autorisations de clé : sélectionnez « Obtenir », « Inclure la clé » et « Ne pas inclure la clé ».
- Sélectionner le principal : entrez la valeur principal-id retournée dans la réponse à l’étape précédente.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L’autorisation *Obtenir* est nécessaire pour vérifier que votre coffre de clés est configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Mettre à jour la ressource de cluster avec les détails de l’identificateur de clé

Cette étape est effectuée lors des mises à jour de version de clé initiales et futures dans votre coffre de clés. Elle informe le stockage Azure Monitor sur la version de clé à utiliser pour le chiffrement des données. Une fois mise à jour, votre nouvelle clé est utilisée pour encapsuler et désencapsuler sur la clé de stockage (AEK).

Pour obtenir les détails de l’*identificateur de clé*de votre coffre de clé et mettre à jour la ressource de*cluster*, sélectionnez la version actuelle de votre clé dans Azure Key Vault.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/key-identifier-8bit.png)

Mettez à jour la propriété KeyVaultProperties da la ressource *cluster* avec les détails de l’identificateur de clé.

**Mettre à jour**

Cette requête du gestionnaire des ressources est une opération asynchrone lors de la mise à jour des détails de l’identificateur de clé, alors qu’elle est synchrone lors de la mise à jour de la valeur de capacité.

> [!NOTE]
> Vous pouvez fournir un corps partiel dans une ressource *cluster* pour mettre à jour une propriété *sku*, *keyVaultProperties* ou *billingType*.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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
    "clusterType": "LogAnalytics", 
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

**Associer un espace de travail**

Cette requête Resource Manager est asynchrone.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
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
2. Envoyez une requête [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) et observez la réponse. L’espace de travail associé présente un clusterResourceId sous « features ».

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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

## <a name="cmk-manage"></a>Gestion de clé CMK

- **Obtenir toutes les ressources *cluster* d’un groupe de ressources**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
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
          "clusterType": "LogAnalytics", 
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

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Réponse**
    
  La même réponse que pour les ressources de *cluster* pour un groupe de ressources, mais dans l’étendue d’un abonnement.

- **Mettre à jour la *réservation de capacité* dans une ressource *cluster***

  À mesure que le volume de données de vos espaces de travail associés change au fil du temps, vous souhaitez mettre à jour le niveau de réservation de capacité de manière appropriée. Suivez la [mise à jour de la ressource *cluster*](#update-cluster-resource-with-key-identifier-details) et fournissez votre nouvelle valeur de capacité. Celle-ci peut varier entre 1 000 et 2 000 Go par jour par incréments de 100. Pour un niveau de réservation de capacité supérieur à 2 000 Go par jour, adressez-vous à votre contact Microsoft pour l’activer. Notez que vous n’avez pas besoin de fournir tout le corps de la requête REST et que vous devez inclure la propriété sku :

  **body**
  ```json
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

  **body**
  ```json
  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Dissocier l’espace de travail**

  Pour effectuer cette opération, vous devez disposer des autorisations d’écriture sur l’espace de travail et la ressource *cluster*. Vous pouvez à tout moment dissocier un espace de travail de votre ressource *cluster*. Les nouvelles données ingérées après l’opération de dissociation sont stockées dans le stockage Log Analytics et chiffrées avec une clé Microsoft. Vous pouvez interroger les données ingérées dans votre espace de travail avant et après la dissociation en toute facilité tant que la ressource *cluster* est approvisionnée et configurée avec une clé de Key Vault valide.

  Cette requête Resource Manager est asynchrone.

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Réponse**

  200 OK et en-tête.

  Les données ingérées après l’opération de dissociation sont stockées dans le stockage Log Analytics. Cela peut prendre jusqu’à 90 minutes. Vous pouvez vérifier l’état de dissociation de l’espace de travail de deux manières :

  1. Copiez la valeur de l’URL Azure-AsyncOperation à partir de la réponse et suivez les instructions de[contrôle de l’état des opérations asynchrones](#asynchronous-operations-and-status-check).
  2. En envoyant une requête [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) et en observant la réponse. L’espace de travail dissocié n’a pas d’identificateur *clusterResourceId* sous *features*.

- **Supprimer votre ressource *cluster***

  Pour effectuer cette opération, vous devez disposer des autorisations d’écriture sur la ressource *cluster*. Une opération de suppression réversible est effectuée pour permettre la récupération de votre ressource de *cluster*, y compris ses données, dans un délai de 14 jours, que la suppression ait été accidentelle ou intentionnelle. Le nom de la ressource de *cluster* reste réservé pendant la période de suppression réversible et vous ne pouvez pas l’utiliser pour un autre cluster. À l’issue la période de suppression réversible, le nom de la ressource *cluster* redevient disponible, et votre ressource *cluster* ainsi que ses données sont définitivement supprimés. Tout espace de travail associé est dissocié de la ressource *cluster* lors de l’opération de suppression. Les nouvelles données ingérées sont stockées dans le stockage Log Analytics et chiffrées avec une clé Microsoft. L’opération de dissociation des espaces de travail est asynchrone et peut prendre jusqu’à 90 minutes.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Réponse**

  200 OK

- **Récupérer votre ressource *cluster* et vos données** 
  
  Une ressource *cluster* supprimée au cours des 14 derniers jours est dans un état de suppression réversible. Il est possible de la récupérer avec ses données. Étant donné que tous les espaces de travail ont été dissociés de la ressource *cluster* suite à la suppression de celle-ci, vous devez réassocier vos espaces de travail après la récupération du chiffrement par clé CMK. Actuellement, cette opération de récupération est effectuée manuellement par le groupe de produits. Utilisez votre canal Microsoft pour les demandes de récupération.

## <a name="limitationsandconstraints"></a>Limitations et contraintes

\- La clé CMK est prise en charge sur un cluster Log Analytics dédié, et convient pour des clients qui envoient au moins 1 To par jour.

\- Le nombre maximal de ressources  *cluster* par région et abonnement est de 2.

\- Vous pouvez associer un espace de travail à votre ressource  *cluster*, puis le dissocier si clé CMK n’est pas requise pour l’espace de travail. Le nombre d’associations d’espace de travail sur un espace de travail particulier sur une période de 30 jours est limité à 2.

\- Vous ne devez associer un espace de travail à une ressource  *cluster* qu’APRÈS avoir vérifié que l’approvisionnement du cluster Log Analytics est terminé. Les données envoyées à votre espace de travail avant la fin de l’approvisionnement seront définitivement supprimées.

\- Le chiffrement par clé CMK s’applique aux données ingérées après la configuration de la clé CMK. Les données ingérées avant la configuration de la clé CMK restent chiffrées avec une clé Microsoft. Vous pouvez interroger les données ingérées avant et après la configuration de clé CMK en toute facilité.

\- Azure Key Vault doit être configuré comme récupérable. Les propriétés ci-après ne sont pas activées par défaut, et doivent être configurées à l’aide de l’interface CLI ou de PowerShell : - La  [Suppression réversible](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) doit être activée.
- La  [Protection contre le vidage](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) doit être activée pour éviter toute suppression forcée du secret ou du coffre, même après une suppression réversible.

\- Le déplacement d’une ressource  *cluster* vers un autre groupe de ressources ou abonnement n’est pas pris en charge.

\- Votre Azure Key Vault, la ressource  *cluster* et les espaces de travail associés doivent se trouver dans la même région et dans le même locataire Azure Active Directory (Azure AD) mais peuvent être liés à des abonnements différents.

\- L’association d’un espace de travail à une ressource  *cluster* échoue si l’espace de travail est associé à une autre ressource  *Cluster*.

## <a name="troubleshooting"></a>Dépannage

- Comportement avec disponibilité du Key Vault
  - En temps normal, le Stockage met en cache la clé AEK pendant de courtes périodes et revient régulièrement dans Key Vault pour la désencapsulation.
    
  - Erreurs de connexion temporaires : le Stockage gère les erreurs temporaires (délais d’attente, échecs de connexion, problèmes DNS) en autorisant les clés à rester en cache pendant un peu plus de temps, compensant toute courte période d’indisponibilité. Les fonctionnalités de requête et d’ingestion se poursuivent sans interruption.
    
  - Une indisponibilité du site actif d’environ 30 minutes entraîne l’indisponibilité du compte de stockage. La fonctionnalité de requête est indisponible et les données ingérées sont mises en cache pendant plusieurs heures à l’aide de la clé Microsoft pour éviter la perte de données. Quand l’accès à Key Vault est restauré, la requête est disponible et les données mises en cache temporaires sont ingérées dans le magasin de données et chiffrées avec CMK.

  - Taux d’accès au Key Vault : la fréquence à laquelle le stockage Azure Monitor accède au Key Vault pour les opérations d’encapsulation (wrap) et de désencapsulation (unwrap) est comprise entre 6 et 60 secondes.

- Si vous créez une ressource de *cluster* et spécifiez la propriété KeyVaultProperties immédiatement, l’opération peut échouer, car la stratégie d’accès ne peut pas être définie tant que l’identité système n’a pas été attribuée à la *ressource* cluster.

- Si vous mettez à jour la ressource de *cluster* existante avec KeyVaultProperties et que la stratégie d’accès de clé « Obtenir » est manquante dans Key Vault, l’opération échoue.

- Si vous essayez de supprimer une ressource de *cluster* qui est associée à un espace de travail, l’opération de suppression échoue.

- Si vous recevez une erreur de conflit lors de la création d’une ressource de *cluster*, il est possible que vous ayez supprimé votre ressource de *cluster* au cours des derniers 14 jours et qu’elle se trouve dans une période de suppression réversible. Le nom de la ressource de *cluster* reste réservé pendant la période de suppression réversible et vous ne pouvez pas l’utiliser pour un autre cluster. Le nom est libéré après la période de suppression réversible, lorsque la ressource de *cluster* est supprimée définitivement.

- Si vous mettez à jour votre ressource de *cluster* pendant qu’une opération est en cours, l’opération échoue.

- Si vous ne parvenez pas à déployer votre ressource *cluster*, vérifiez que votre Azure Key Vault, votre ressource  *cluster* et les espaces de travail Log Analytics associés se trouvent dans la même région. Ils peuvent être liés à des abonnements différents.

- Si vous mettez à jour votre version de clé dans Key Vault et ne mettez pas à jour les détails de l’identificateur de clé dans la ressource *cluster*, le cluster Log Analytics continue d’utiliser votre clé précédente et vos données deviennent inaccessibles. Mettez à jour les nouveaux détails de l’identificateur de clé dans la ressource *cluster* pour reprendre l’ingestion des données et avoir la possibilité d’interroger les données.

- Pour obtenir du support et de l’aide concernant la clé gérée par le client, utilisez vos contacts dans Microsoft.
