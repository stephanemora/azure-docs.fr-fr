---
title: Configuration de clés gérées par le client dans Azure Monitor
description: Informations et étapes relatives à la configuration de CMK (Customer-Managed Key) pour chiffrer des données dans vos espaces de travail Log Analytics à l’aide d’une clé Azure Key Vault.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/05/2020
ms.openlocfilehash: eff751465c7b64429968b0305e6ad483943c374b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048181"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Configuration de clés gérées par le client dans Azure Monitor 

Cet article fournit des informations générales et des étapes relatives à la configuration de clés gérées par le client (CMK, Customer-Managed Keys) pour vos espaces de travail Log Analytics et les composants Application Insights. Une fois la configuration effectuée, toutes les données envoyées à vos espaces de travail ou aux composants sont chiffrées à l’aide de votre clé Azure Key Vault.

Nous vous recommandons de passer en revue les [Limitations et contraintes](#limitations-and-constraints) ci-dessous avant de procéder à la configuration.

## <a name="disclaimers"></a>Clauses d’exclusion de responsabilité

- Azure Monitor CMK est une fonctionnalité à accès anticipé et est activée pour les abonnements inscrits.

- Le déploiement de CMK décrit dans cet article est fourni en qualité de production et est pris en charge en tant que tel même s’il s’agit d’une fonctionnalité à accès anticipé.

- La fonctionnalité CMK est fournie sur un cluster de magasin de données dédié, cluster Azure Data Explorer (ADX) qui convient aux clients envoyant 1 To de données par jour ou plus. 

- Le modèle tarifaire de CMK n’est pas disponible et n’est pas abordé dans cet article. Un modèle tarifaire pour un cluster ADX dédié est attendu pour le deuxième trimestre de l’année civile 2020 et s’appliquera à tous les déploiements CMK existants.

- Cet article décrit la configuration de CMK pour les espaces de travail Log Analytics. Les composants de CMK pour Application Insights sont également pris en charge à l’aide de cet article, dont l’annexe liste les différences.

> [!NOTE]
> Log Analytics et Application Insights utilisent la même plateforme de magasin de données et le même moteur de requête.
> Nous sommes en train de rassembler ces deux magasins en intégrant Application Insights à Log Analytics pour créer un seul magasin de journaux unifié sous Azure Monitor. Cette modification est planifiée pour le deuxième trimestre de l’année civile 2020. Si vous n’avez pas besoin de déployer de CMK pour vos données Application Insights d’ici là, nous vous recommandons d’attendre la fin du regroupement ; en effet, ces déploiements seront interrompus par le regroupement et vous devrez reconfigurer CMK après la migration vers l’espace de travail Log Analytics. Le minimum de 1 To par jour s’applique au niveau du cluster, et tant que le regroupement, prévu pour le deuxième trimestre, n’aura pas été effectué, Application Insights et Log Analytics auront besoin de clusters distincts.

## <a name="customer-managed-key-cmk-overview"></a>Vue d’ensemble des clés gérées par le client (CMK)

Le [chiffrement au repos](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) est une exigence de sécurité et de confidentialité courante dans les organisations. Vous pouvez laisser Azure gérer complètement le chiffrement au repos, mais vous disposez aussi de plusieurs options pour gérer étroitement le chiffrement ou les clés de chiffrement.

Le magasin de données Azure Monitor garantit que toutes les données sont chiffrées au repos à l’aide de clés managées par Azure, tout en étant stockées dans le Stockage Azure. Azure Monitor fournit également une option pour le chiffrement des données à l’aide de votre propre clé qui est stockée dans [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), accessible à l’aide de l’authentification par [identité managée](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) attribuée par le système. Cette clé peut être [protégée par un logiciel ou par un HSM matériel](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
L’utilisation du chiffrement par Azure Monitor est identique au fonctionnement du [chiffrement de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption).

La fréquence à laquelle le Stockage Azure Monitor accède à Key Vault pour les opérations d’encapsulation (wrap) et de désencapsulation (unwrap) est comprise entre 6 et 60 secondes. Le Stockage Azure Monitor respecte toujours les modifications des autorisations de clé en l’espace d’une heure.

Les données ingérées au cours des 14 derniers jours sont également conservées dans le cache à chaud (SSD) pour optimiser l’utilisation du moteur de requête. Ces données restent chiffrées avec les clés Microsoft, quelle que soit la configuration de CMK, mais nous travaillons au chiffrement du disque SSD avec CMK pour le début de 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Fonctionnement de CMK dans Azure Monitor

Azure Monitor tire parti de l’identité managée attribuée par le système pour accorder l’accès à votre coffre de clés Azure. Une identité managée attribuée par le système ne peut être associée qu’à une seule ressource Azure. L’identité du magasin de données Azure Monitor (cluster ADX) est prise en charge au niveau du cluster ; ainsi, la fonctionnalité CMK est fournie sur un cluster ADX dédié. Pour que la fonctionnalité CMK soit prise en charge sur plusieurs espaces de travail, une nouvelle ressource Log Analytics (*cluster*) s’exécute en tant que connexion d’identité intermédiaire entre votre coffre de clés et vos espaces de travail Log Analytics. Ce concept est conforme à la contrainte d’identité affectée par le système et l’identité est conservée entre le cluster ADX et la ressource de *cluster* Log Analytics, tandis que les données de tous les espaces de travail associés sont protégées avec votre clé Key Vault. Le stockage en cluster ADX sous-jacent utilise l’identité managée qui est associée à la ressource de *cluster* pour s’authentifier et accéder à votre coffre de clés Azure via Azure Active Directory.

![Vue d’ensemble de CMK](media/customer-managed-keys/cmk-overview.png)
1.  Coffre de clés du client.
2.  Ressource de cluster Log Analytics du client disposant d’une identité managée avec des autorisations pour Key Vault : l’identité est prise en charge au niveau du magasin de données (cluster ADX).
3.  Cluster ADX Azure Monitor dédié.
4.  Espaces de travail du client associés à la ressource de cluster pour le chiffrement CMK.

## <a name="encryption-keys-management"></a>Gestion des clés de chiffrement

Il existe trois types de clés impliquées dans le chiffrement des données de Stockage :

- **KEK** : clé de chiffrement principale dans Key Vault (CMK)
- **AEK** : clé de chiffrement de compte
- **DEK** : clé de chiffrement de données

Les règles suivantes s’appliquent :

- Le compte de stockage ADX génère une clé de chiffrement unique pour chaque compte de stockage, connue sous le nom d’AEK.

- La clé AEK est utilisé pour dériver les clés DEK, clés utilisées pour chiffrer chaque bloc de données écrites sur le disque.

- Quand vous configurez votre clé dans Key Vault et que vous la référencez dans la ressource de *cluster*, Stockage Azure encapsule la clé AEK avec votre clé KEK dans Azure Key Vault.

- Votre clé KEK ne quitte jamais votre coffre de clés et, dans le cas d’une clé HSM, elle ne quitte jamais le matériel.

- Stockage Azure utilise l’identité managée associée à la ressource de *cluster* pour s’authentifier et accéder à Azure Key Vault par le biais d’Azure Active Directory.

- Pour les opérations de lecture/écriture, Stockage Azure envoie des requêtes à Azure Key Vault pour encapsuler et désencapsuler la clé AEK afin d’effectuer les opérations de chiffrement et de déchiffrement.

## <a name="cmk-provisioning-procedure"></a>Procédure de provisionnement de CMK

Pour la configuration de CMK pour Application Insights, suivez les étapes 3 et 6 du contenu de l’annexe.

1. Mise en liste verte des abonnements : cette opération est nécessaire pour cette fonctionnalité à accès anticipé
2. Création du coffre de clés Azure et stockage de la clé
3. Création d’une ressource de *cluster*
4. Octroi d’autorisations d’accès à votre coffre de clés
5. Provisionnement du magasin de données (cluster ADX) Azure Monitor
6. Association des espaces de travail Log Analytics

La procédure n’étant pas prise en charge dans l’interface utilisateur, le processus de provisionnement est effectué par le biais de l’API REST.

> [!IMPORTANT]
> Toute demande d’API doit inclure un jeton d’autorisation du porteur dans l’en-tête de la demande.

Par exemple :

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Où *eyJ0eXAiO....* représente le jeton d’autorisation complet. 

Vous pouvez obtenir le jeton à l’aide de l’une des méthodes suivantes :

1. Utilisez la méthode [Inscriptions des applications](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens).

2. Dans le portail Azure
    1. Accédez aux outils de développement (F12) du portail Azure.
    1. Recherchez la chaîne d’autorisation sous « En-têtes de demande » dans l’une des instances de « batch?api-version ». Elle ressemble à ceci : « authorization: Bearer \<jeton\> ». 
    1. Copiez-la et ajoutez-la à votre appel d’API selon les exemples ci-dessous.

3. Accédez au site de documentation REST Azure. Appuyez sur « Essayer » sur n’importe quelle API et copiez le jeton du porteur.

### <a name="subscription-whitelisting"></a>Mise en liste verte des abonnements

CMK est une fonctionnalité à accès anticipé. Les abonnements dans lesquels vous envisagez de créer des ressources de *cluster* doivent être préalablement inclus dans une liste verte par le groupe de produits Azure. Utilisez vos contacts au sein de Microsoft pour fournir vos ID d’abonnement.

> [!IMPORTANT]
> La fonctionnalité CMK est régionale. Vos coffre de clés Azure, compte de stockage, ressource de *cluster* et espaces de travail Log Analytics associés doivent se trouver dans la même région, mais ils peuvent être dans des abonnements différents.

### <a name="storing-encryption-key-kek"></a>Stockage de la clé de chiffrement (KEK)

Créez une ressource Azure Key Vault, puis générez ou importez une clé à utiliser pour le chiffrement des données.

Le coffre de clés Azure doit être configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor.

Les paramètres suivants sont disponibles par le biais de l’interface CLI et de PowerShell :
- La [suppression réversible](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) doit être activée
- La [protection contre le vidage](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) doit être activée pour bénéficier d’une protection contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

### <a name="create-cluster-resource"></a>Création d’une ressource de *cluster*

Cette ressource est utilisée comme connexion d’identité intermédiaire entre votre coffre de clés et vos espaces de travail. Une fois que vous avez reçu une confirmation que vos abonnements ont été ajoutés à la liste verte, créez une ressource de *cluster* Log Analytics dans la région où se trouvent vos espaces de travail. Application Insights et Log Analytics nécessitent des ressources de cluster distinctes. Vous définissez le type de la ressource de cluster au moment de la création en affectant à la propriété « clusterType » la valeur « LogAnalytics » ou « ApplicationInsights ». Le type de ressource de cluster ne peut pas être modifié.

Pour la configuration de CMK pour Application Insights, suivez cette étape du contenu de l’annexe.

**Créer**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
La valeur « clusterType » est « ApplicationInsights » pour CMK pour Application Insights.

**Réponse**

L’identité est attribuée à la ressource de *cluster* au moment de la création.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```
« principalId » est un identificateur unique généré par le service d'identité managée pour la ressource de *cluster*.

> [!IMPORTANT]
> Copiez et conservez la valeur « cluster-id », car vous en aurez besoin dans les étapes suivantes.

Si vous souhaitez supprimer la ressource de *cluster* pour une raison quelconque, par exemple, pour la créer avec un nom ou un type de cluster (clusterType) différent, utilisez cet appel d’API :

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Octroi d’autorisations d’accès au coffre de clés

Mettez à jour votre coffre de clés et ajoutez une stratégie d’accès pour la ressource de cluster. Les autorisations d’accès à votre coffre de clés sont ensuite propagées vers le Stockage Azure Monitor sous-jacent à utiliser pour le chiffrement des données.
Ouvrez votre coffre de clés dans le portail Azure, puis cliquez sur « Stratégies d’accès », puis sur « + Ajouter une stratégie d’accès » pour créer une stratégie ces paramètres ci-après :

- Autorisations de clé : sélectionnez « Obtenir », « Inclure la clé » et « Ne pas inclure la clé ».

- Sélectionner le principal : entrez l’ID de cluster (cluster-id), qui est la valeur « clusterId » dans la réponse de l’étape précédente.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/grant-key-vault-permissions.png)

L’autorisation *Obtenir* est nécessaire pour vérifier que votre coffre de clés est configuré comme récupérable pour protéger votre clé et l’accès à vos données Azure Monitor.

La propagation de la ressource de *cluster* dans Azure Resource Manager prend quelques minutes. Lors de la configuration de cette stratégie d’accès immédiatement après la création de la ressource de *cluster*, une erreur temporaire peut se produire. Dans ce cas, réessayez après quelques minutes.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Mettre à jour la ressource de cluster avec les détails de l’identificateur de clé

Cette étape applique les futures mises à jour de version de clé dans votre coffre de clés. Mettez à jour la ressource de *cluster* à l’aide des détails de l’*identificateur de clé* Key Vault, afin de permettre au Stockage Azure Monitor d’utiliser la nouvelle version de la clé. Sélectionnez la version actuelle de votre clé dans Azure Key Vault pour afficher les détails de l’identificateur de clé.

![Octroi d’autorisations d’accès au coffre de clés](media/customer-managed-keys/key-identifier-8bit.png)

Mettez à jour la propriété KeyVaultProperties da la ressource *cluster* avec les détails de l’identificateur de clé.

**Mettre à jour**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
            KeyName: "<key-name>",
            KeyVersion: "<current-version>"
            },
   },
   "location":"<region-name>",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```
« KeyVaultProperties » contient les détails de l'identificateur de clé Key Vault.

**Réponse**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Provisionnement du magasin de données (cluster ADX) Azure Monitor

Au cours de la période d’accès anticipé de la fonctionnalité, le cluster ADX est provisionné manuellement par l’équipe du produit une fois les étapes précédentes accomplies. Utilisez le canal que vous avez avec Microsoft pour fournir les informations suivantes :

- Vérification que les étapes ci-dessus ont été correctement effectuées.

- Réponse JSON de l’étape précédente. Elle peut être récupérée à tout moment à l’aide d’un appel d’API Get :

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
   Authorization: Bearer <token>
   ```

   **Réponse**
   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-Id"
     },
     "properties": {
          "KeyVaultProperties": {
               KeyVaultUri: "https://key-vault-name.vault.azure.net",
               KeyName: "key-name",
               KeyVersion: "current-version"
               },
       "provisioningState": "Succeeded",
       "clusterType": "LogAnalytics", 
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

### <a name="workspace-association-to-cluster-resource"></a>Association d’espaces de travail à la ressource de *cluster*

> [!NOTE]
> Vous devez effectuer cette étape **uniquement** après avoir reçu de la part du groupe de produits par le biais de votre canal Microsoft une confirmation que le **provisionnement du magasin de données Azure Monitor (ADX)** a été exécuté. Si vous associez des espaces de travail et ingérez des données avant ce **provisionnement**, les données sont définitivement supprimées.

Pour la configuration de CMK pour Application Insights, suivez cette étape du contenu de l’annexe.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
*clusterDefinitionId* est la valeur de *clusterId* fournie dans la réponse de l’étape précédente.

**Réponse**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

Une fois l’association effectuée, les données envoyées à vos espaces de travail sont stockées sous forme chiffrée à l’aide de votre clé managée.

### <a name="workspace-association-verification"></a>Vérification de l'association de l'espace de travail
Vous pouvez vérifier qu'un espace de travail est associé à une ressource de *cluster* en consultant la réponse [Workspaces – Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get). L'espace de travail associé présentera une propriété « clusterResourceId » avec l'ID de la ressource de *cluster*.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
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
    "retentionInDays": days,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
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

Le Stockage Azure Monitor respecte toujours les modifications des autorisations de clé en l’espace d’une heure, normalement plus tôt, et devient alors indisponible. Toutes les données ingérées dans les espaces de travail associés à votre ressource de *cluster* sont supprimées et les requêtes échouent. Les données précédemment ingérées restent inaccessibles dans le Stockage Azure Monitor tant que votre clé est révoquée et que vos espaces de travail ne sont pas supprimés. Les données inaccessibles sont régies par la stratégie de conservation des données et sont vidées à la fin de la durée de conservation.

Le Stockage interroge régulièrement votre coffre de clés pour tenter de désencapsuler la clé de chiffrement et une fois qu’il est accessible, l’ingestion et l’interrogation des données reprennent dans un délai de 30 minutes.

## <a name="cmk-kek-rotation"></a>Rotation de clé CMK (KEK)

La rotation de clé CMK nécessite une mise à jour explicite de la ressource de *cluster* avec la nouvelle version de la clé Azure Key Vault. Pour mettre à jour Azure Monitor avec votre nouvelle version de clé, suivez les instructions de l’étape « Mettre à jour la ressource de *cluster* avec les détails de l’*identificateur de clé* ».

Si vous mettez à jour votre clé dans Key Vault et que vous ne mettez pas à jour les détails du nouvel *identificateur de clé* dans la ressource de *cluster**, le Stockage Azure Monitor continue à utiliser votre clé précédente.

## <a name="limitations-and-constraints"></a>Limitations et contraintes

- La fonctionnalité CMK est prise en charge au niveau du cluster ADX et nécessite un cluster ADX Azure Monitor dédié.

- Le nombre maximal de ressources de *cluster* par abonnement est limité à 5.

- L’*association de la ressource de cluster* à un espace de travail ne doit être effectuée qu’une fois que vous avez reçu du groupe de produits une confirmation que le provisionnement du cluster ADX a été exécuté. Les données envoyées avant ce provisionnement sont définitivement.

- Le chiffrement CMK s’applique aux données nouvellement ingérées après la configuration de CMK. Les données qui ont été ingérées avant la configuration de CMK demeurent chiffrées avec la clé Microsoft. Vous pouvez interroger les données avant et après la configuration de manière fluide.

- Une fois que l’espace de travail est associé à une ressource de *cluster*, il ne peut pas en être dissocié, car les données sont chiffrées avec votre clé et ne sont pas accessibles sans votre clé KEK dans Azure Key Vault.

- Le coffre de clés Azure doit être configuré comme récupérable. Les propriétés ci-après, qui ne sont pas activées par défaut, doivent être configurées à l’aide de l’interface CLI et de PowerShell :

  - La [suppression réversible](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) doit être activée
  - La [protection contre le vidage](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) doit être activée pour bénéficier d’une protection contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.

- Application Insights et Log Analytics nécessitent des ressources de *cluster* distinctes. Vous définissez le type de la ressource de *cluster* au moment de la création en affectant à la propriété « clusterType » la valeur « LogAnalytics » ou « ApplicationInsights ». Le type de ressource de *cluster* ne peut pas être modifié.

- Le déplacement de la ressource de *cluster* vers un autre groupe de ressources ou abonnement n’est pas pris en charge.

- Votre Azure Key Vault, la ressource de *cluster* et les espaces de travail associés doivent se trouver dans la même région et dans le même locataire Azure Active Directory (Azure AD) mais peuvent être liés à des abonnements différents.

- L’association d’un espace de travail à la ressource de *cluster* échoue s’il est associé à une autre ressource de *cluster*

## <a name="troubleshooting-and-management"></a>Résolution des problèmes et gestion

- Disponibilité de Key Vault
    - En temps normal, le Stockage met en cache la clé AEK pendant de courtes périodes et revient régulièrement dans Key Vault pour la désencapsulation.
    
    - Erreurs de connexion temporaires. Le Stockage gère les erreurs temporaires (délais d’attente, échecs de connexion, problèmes DNS) en autorisant les clés à rester en cache pendant un peu plus de temps, compensant toute courte période d’indisponibilité. Les fonctionnalités de requête et d’ingestion se poursuivent sans interruption.
    
    - Une indisponibilité du site actif d’environ 30 minutes entraîne l’indisponibilité du compte de stockage. La fonctionnalité de requête est indisponible et les données ingérées sont mises en cache pendant plusieurs heures à l’aide de la clé Microsoft pour éviter la perte de données. Quand l’accès à Key Vault est restauré, la requête est disponible et les données mises en cache temporaires sont ingérées dans le magasin de données et chiffrées avec CMK.

- Si vous créez une ressource de *cluster* et spécifiez la propriété KeyVaultProperties immédiatement, l’opération peut échouer, car la stratégie d’accès ne peut pas être définie tant que l’identité système n’a pas été attribuée à la *ressource* cluster.

- Si vous mettez à jour la ressource de *cluster* existante avec KeyVaultProperties et que la stratégie d’accès de clé « Obtenir » est manquante dans Key Vault, l’opération échoue.

- Si vous essayez de supprimer une ressource de *cluster* qui est associée à un espace de travail, l’opération de suppression échoue.

- Utilisez l'API ci-après afin d'obtenir toutes les ressources de *cluster* d'un groupe de ressources :

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
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
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Utilisez l’appel d’API ci-après afin d’obtenir toutes les ressources de *cluster* pour un abonnement :

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **Réponse**
    
  La même réponse que pour les ressources de *cluster* pour un groupe de ressources, mais dans l’étendue d’un abonnement.
    
- Utilisez cet appel d’API pour supprimer une ressource de *cluster* : vous devez supprimer tous les espaces de travail associés pour pouvoir supprimer votre ressource de *cluster* :

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **Réponse**

  200 OK


## <a name="appendix"></a>Annexe

La fonctionnalité CMK (Customer Managed Key) pour Application Insights est également prise en charge, même si vous devez prendre en compte le changement indiqué ci-après en vue de la planification du déploiement de CMK pour vos composants Application Insight.

Log Analytics et Application Insights utilisent la même plateforme de magasin de données et le même moteur de requête. Nous sommes en train de rassembler ces deux magasins en intégrant Application Insights à Log Analytics pour fournir un seul magasin de journaux unifié sous Azure Monitor avant le deuxième trimestre de
2020. Ce changement permet d’intégrer vos données Application Insight aux espaces de travail Log Analytics et de rendre possibles les requêtes, les insights et d’autres améliorations, tandis que la configuration de CMK sur votre espace de travail s’applique également à vos données Application Insights.

> [!NOTE]
> Si vous n’avez pas besoin de déployer CMK pour vos données Application Insights avant l’intégration, nous vous recommandons d’attendre avec CMK pour Application Insights ; en effet, ces déploiements seront interrompus par l’intégration et vous devrez reconfigurer CMK après la migration vers l’espace de travail Log Analytics. Le minimum de 1 To par jour s’applique au niveau du cluster, et tant que le regroupement, prévu pour le deuxième trimestre, n’aura pas été effectué, Application Insights et Log Analytics auront besoin de clusters distincts.

## <a name="application-insights-cmk-configuration"></a>Configuration de CMK pour Application Insights

La configuration de CMK pour Application Insights est identique à celle illustrée dans cet article, y compris les contraintes et la résolution des problèmes, à l’exception des étapes ci-après :

- Création d’une ressource de *cluster*

- Association d’un composant à une ressource de *cluster*

Lors de la configuration de CMK pour Application Insights, suivez les étapes ci-après au lieu de celles listées ci-dessus.

### <a name="create-a-cluster-resource"></a>Création d’une ressource de *cluster*

Cette ressource est utilisée comme connexion d’identité intermédiaire entre votre coffre de clés et vos composants. Une fois que vous avez reçu une confirmation que vos abonnements ont été ajoutés à la liste verte, créez une ressource de *cluster* Log Analytics dans la région où se trouvent vos composants. Vous définissez le type de la ressource de *cluster* au moment de la création en affectant à la propriété *clusterType* la valeur *LogAnalytics* ou *ApplicationInsights*. Ce doit être *ApplicationInsights* pour CMK pour Application Insights. Le paramètre *clusterType* ne peut pas être modifié après la configuration.

**Créer**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**Réponse**

L’identité est attribuée à la ressource de *cluster* au moment de la création.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
« principalId » est un identificateur unique généré par le service d'identité managée.

> [!IMPORTANT]
> Copiez et conservez la valeur « cluster-id », car vous en aurez besoin dans les étapes suivantes.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Associer un composant à une ressource de *cluster* à l’aide de l’API [Composants : créer ou mettre à jour](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
« clusterDefinitionId » est la valeur « clusterId » fournie dans la réponse de l'étape précédente.
« web » est un exemple de « kind » (genre).

**Réponse**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
« clusterDefinitionId » est l'ID de la ressource de *cluster* associée à ce composant.

Une fois l’association effectuée, les données envoyées à vos composants sont stockées sous forme chiffrée à l’aide de votre clé managée.
