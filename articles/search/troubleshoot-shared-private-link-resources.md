---
title: Dépannage des ressources de liaison privée partagée
titleSuffix: Azure Cognitive Search
description: Guide de résolution des problèmes courants liés à la gestion des ressources de liaison privée partagée.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/30/2021
ms.openlocfilehash: cfd348b5d30d3bf90b7e7cfd0d9c80dee25910cf
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776360"
---
# <a name="troubleshooting-common-issues-with-shared-private-link-resources"></a>Résolution des problèmes courants liés aux ressources de liaison privée partagée

Les ressources de liaison privée partagée permettent à Recherche cognitive Azure d’établir des connexions sortantes sécurisées pour accéder aux ressources client. Toutefois, au cours du processus de gestion (création, suppression ou mise à jour) de ces ressources, plusieurs types d’erreurs peuvent se produire.

## <a name="creating-a-shared-private-link-resource"></a>Création d’une ressource de liaison privée partagée

Quatre étapes distinctes sont impliquées dans la création d’une ressource de liaison privée partagée :

1. Le client appelle l’[API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) du plan de gestion sur le fournisseur de ressources (RP) de recherche avec les détails de la ressource de liaison privée partagée à créer.

2. La recherche de RP valide la requête et si la validation est concluante, une opération Azure Resource Manager asynchrone (dont la progression peut être interrogée par le client) est lancée

3. Recherchez les requêtes pour la fin de l’opération (qui prend généralement quelques minutes). À ce stade, la ressource de liaison privée partagée a un état d’approvisionnement « Mise à jour ».

4. Une fois l’opération terminée avec succès, un point de terminaison privé (avec éventuellement des zones et mappages DNS) est créé. À ce stade, si le client interroge l’état de la ressource de liaison privée partagée, son état d’approvisionnement est « Réussi ».

![Étapes impliquées dans la création de ressources de liaison privée partagée ](media\troubleshoot-shared-private-link-resources\shared-private-link-states.png)

Certaines erreurs courantes qui se produisent pendant la phase de création sont répertoriées ci-dessous.

### <a name="request-validation-failures"></a>Échecs de validation de requête

+ Référence SKU non prise en charge : les ressources de liaison privée partagée peuvent uniquement être créées pour les références SKU payantes, les services de niveau gratuit ne sont pas pris en charge.

+ Validation de nom : les noms de ressource de liaison privée partagée sont limités à un certain ensemble de caractères. Si le nom de la ressource contient des caractères non valides, la demande de création de la ressource n’est pas acceptée.
Les règles de nommage d’une ressource de liaison privée partagée sont les suivantes :
  
  + La longueur doit être comprise entre 1 et 60 caractères
  + Doit contenir uniquement des caractères alphanumériques ou les caractères de soulignement (_), de point (.) ou de trait d’union (-)

+ Validation de `groupId` : le `groupId` spécifié dans le cadre de la demande de création d’une ressource de liaison privée partagée doit correspondre (à la fois dans l’orthographe et la casse) au tableau ci-dessous :

| Ressource Azure | ID de groupe | Première version de l’API disponible |
| --- | --- | --- |
| Stockage Azure - Blob (ou) ADLS Gen 2 | `blob`| `2020-08-01` |
| Stockage Azure - Tables | `table`| `2020-08-01` |
| Azure Cosmos DB - API SQL | `Sql`| `2020-08-01` |
| Azure SQL Database | `sqlServer`| `2020-08-01` |
| Azure Database pour MySQL (préversion) | `mysqlServer`| `2020-08-01-Preview` |
| Azure Key Vault | `vault` | `2020-08-01` |
| Azure Functions (préversion) | `sites` | `2020-08-01-Preview` |

Les ressources marquées avec « (préversion) » sont uniquement disponibles dans les versions d’API du plan de gestion en préversion et ne sont pas encore mises à la disposition générale. Tout autre `groupId` (ou un `groupId` utilisé dans une version d’API qui ne le prend pas en charge) échouera à la validation.

+ Validation de type de `privateLinkResourceId` : comme `groupId`, Recherche cognitive Azure valide que le « bon » type de ressource est spécifié dans le `privateLinkResourceId`. Les types de ressource valides sont les suivants :

| Ressource Azure | Type de ressource | Première version de l’API disponible |
| --- | --- | --- |
| Stockage Azure | `Microsoft.Storage/storageAccounts`| `2020-08-01` |
| Azure Cosmos DB | `Microsoft.DocumentDb/databaseAccounts`| `2020-08-01` |
| Azure SQL Database | `Microsoft.Sql/servers`| `2020-08-01` |
| Azure Database pour MySQL (préversion) | `Microsoft.DBforMySQL/servers`| `2020-08-01-Preview` |
| Azure Key Vault | `Microsoft.KeyVault/vaults` | `2020-08-01` |
| Azure Functions (préversion) | `Microsoft.Web/sites` | `2020-08-01-Preview` |

En outre, le `groupId` spécifié doit être valide pour le type de ressource spécifié. Par exemple, le « blob » `groupId` est valide pour le type « Microsoft.Storage/storageAccounts ». Il ne peut pas être utilisé avec un autre type de ressource. Pour une version d’API de gestion de recherche donnée, les clients peuvent découvrir les détails des types de ressources et `groupId` pris en charge en utilisant l’ [API Répertorier les ressources prises en charge](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported).

+ Application de la limite de quota : les services de recherche ont des quotas imposés sur le nombre de ressources de liaison privée partagée qui peuvent être créées et le nombre de différents types de ressources cibles qui sont utilisés (selon `groupId`). Ceux-ci sont documentés dans la [section Limites de ressources de liaison privée partagée](search-limits-quotas-capacity.md#shared-private-link-resource-limits) de la page Limites du service Recherche cognitive Azure.

### <a name="azure-resource-manager-deployment-failures"></a>Échecs de déploiement Azure Resource Manager

Une fois que la recherche a accepté la demande de création d’une ressource de liaison privée partagée, le déploiement Azure Resource Manager qu’il lance peut également échouer pour plusieurs raisons. Dans tous les cas, lorsque les clients interrogent l’état de l’opération asynchrone (décrite [ici](search-indexer-howto-access-private.md#step-1-create-a-shared-private-link-resource-to-the-storage-account)), un message d’erreur approprié et tous les détails disponibles s’affichent.

Les ressources de liaison privée partagée qui ont échoué lors du déploiement d’Azure Resource Manager s’afficheront dans les appels d’API [List](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/listbyservice) et [Get](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get), mais auront un « État d’approvisionnement » de `Failed`. Une fois la raison de l’échec du déploiement d’Azure Resource Manager constatée, supprimez la ressource `Failed`, puis recréez-la après avoir appliqué la résolution appropriée à partir du tableau ci-dessous.

| Raison de l’échec de déploiement | Description | Résolution |
| --- | --- | --- |
| Fournisseur de ressources réseau non inscrit pour l’abonnement à la ressource cible | Un point de terminaison privé (et ses mappages DNS associés) est créé pour la ressource cible (compte de stockage, CosmosDB, SQL Server, etc.) par le biais du fournisseur de ressources (RP) `Microsoft.Network`. Si l’abonnement qui héberge la ressource cible (« abonnement cible ») n’est pas inscrit auprès du RP `Microsoft.Network`, le déploiement d’Azure Resource Manager peut échouer. | Les clients doivent inscrire ce RP dans leur abonnement cible. En règle générale, cette opération peut être effectuée par le biais du portail Azure, de PowerShell ou de l’interface CLI, comme indiqué dans [ce guide](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) |
| `groupId` non valide pour la ressource cible | Lors de la création de comptes CosmosDB, les clients peuvent spécifier le type d’API pour le compte de base de données. Si CosmosDB offre plusieurs types d’API différents, Recherche cognitive Azure prend en charge uniquement « SQL » comme `groupId` pour les ressources de liaison privée partagée. Lorsqu’une ressource de liaison privée partagée « SQL » est créée pour un `privateLinkResourceId` pointant vers un compte de base de données non SQL, le déploiement Azure Resource Manager échoue en raison de la non-correspondance de `groupId`. L’ID de ressource Azure d’un compte CosmosDB n’est pas suffisant pour déterminer le type d’API utilisé. Recherche cognitive Azure tente de créer le point de terminaison privé, qui est ensuite refusé par CosmosDB. | Les clients doivent s’assurer que le `privateLinkResourceId` de la ressource CosmosDB spécifiée est destiné à un compte de base de données de type API « SQL ». |
| Ressource cible introuvable | L’existence de la ressource cible spécifiée dans `privateLinkResourceId` est vérifiée uniquement au début du déploiement d’Azure Resource Manager. Si la ressource cible n’est plus disponible, le déploiement échoue. | Le client doit s’assurer que la ressource cible est présente dans l’abonnement et le groupe de ressources spécifiés et qu’elle n’est pas déplacée/supprimée |
| Erreurs temporaires/autres | Le déploiement d’Azure Resource Manager peut échouer en cas de panne de l’infrastructure ou pour d’autres raisons inattendues. Cela doit être rare et indique généralement un état transitoire. | Réessayez de créer cette ressource ultérieurement. Si le problème persiste, contactez le support Azure. |

### <a name="resource-stuck-in-updating-or-incomplete-state"></a>La ressource est bloquée dans l’état « Mise à jour » ou « Incomplet »

En règle générale, une ressource de liaison privée partagée doit passer à un état terminal (`Succeeded` ou `Failed`) sous quelques minutes après que la demande a été acceptée par le RP de recherche.

Dans de rares cas, Recherche cognitive Azure peut ne pas être en mesure de marquer correctement l’état de la ressource de liaison privée partagée sur un état terminal (`Succeeded` ou `Failed`). Cela se produit généralement en raison d’une défaillance inattendue ou catastrophique dans le RP de recherche. Les ressources de liaison privée partagée sont automatiquement transférées vers un état `Failed` si elles ont été « bloquées » dans un état non terminal pendant plus de 8 heures.

Si vous constatez que la ressource de liaison privée partagée n’a pas été transformée en état terminal, attendez 8 heures avant qu’elle passe à `Failed` pour la supprimer et la recréer. Ou bien, au lieu d’attendre, vous pouvez essayer de créer une autre ressource de liaison privée partagée avec un nom différent (en conservant tous les autres paramètres).

## <a name="updating-a-shared-private-link-resource"></a>Mise à jour d’une ressource de liaison privée partagée

Une ressource de liaison privée partagée existante peut être mise à jour à l’aide de l’[API de création ou de mise à jour](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate). La recherche de RP n’autorise que les mises à jour restrictives de la ressource de liaison privée partagée. Seul le message de demande peut être modifié par le biais de cette API.

+ Il n’est pas possible de mettre à jour les propriétés « principales » d’une ressource de liaison privée partagée existante (comme `privateLinkResourceId` ou `groupId`), et cette opération ne sera jamais prise en charge. Si une autre propriété en plus du message de demande doit être modifiée, nous conseillons aux clients de supprimer et recréer la ressource de liaison privée partagée.

+ La tentative de mise à jour du message de demande d’une ressource de liaison privée partagée n’est possible que si elle a atteint l’état d’approvisionnement `Succeeded`.

## <a name="deleting-a-shared-private-link-resource"></a>Suppression d’une ressource de liaison privée partagée

Les clients peuvent supprimer une ressource de liaison privée partagée existante via l’[API de suppression](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/delete). Comme avec le processus de création (ou de mise à jour), il s’agit d’une opération asynchrone en quatre étapes :

1. Le client demande au RP de recherche de supprimer la ressource de liaison privée partagée.

2. Le RP de recherche confirme que la ressource existe et qu’elle est dans un état valide pour la suppression. Si c’est le cas, il initie une opération de suppression d’Azure Resource Manager pour supprimer la ressource.

3. Recherchez les requêtes pour la fin de l’opération (qui prend généralement quelques minutes). À ce stade, la ressource de liaison privée partagée a un état d’approvisionnement « Suppression ».

4. Une fois l’opération terminée avec succès, le point de terminaison privé de sauvegarde et les mappages DNS associés sont supprimés. La ressource n’apparaît pas dans le cadre de l’opération [List](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/listbyservice) et la tentative d’une opération [Get](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get) sur cette ressource entraîne l’affichage d’une erreur 404 introuvable.

![Étapes impliquées dans la suppression de ressources de liaison privée partagée ](media\troubleshoot-shared-private-link-resources\shared-private-link-delete-states.png)

Certaines erreurs courantes qui se produisent pendant la phase de suppression sont répertoriées ci-dessous.

| Type d'échec | Description | Résolution |
| --- | --- | --- |
| La ressource est dans un état non terminal | Impossible de supprimer une ressource de liaison privée partagée qui ne se trouve pas dans un état terminal (`Succeeded` ou `Failed`). Il est possible (rare) qu’une ressource de liaison privée partagée soit bloquée dans un état non terminal pendant jusqu’à 8 heures. | Attendez que la ressource ait atteint un état terminal et recommencez la demande de suppression. |
| Échec de l’opération de suppression avec l’erreur « Conflit » | L’opération Azure Resource Manager pour supprimer une ressource de liaison privée partagée arrive au fournisseur de ressources de la ressource cible spécifiée dans `privateLinkResourceId` (« RP cible ») avant de pouvoir supprimer le point de terminaison privé et les mappages DNS. Les clients peuvent utiliser des [verrous de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) pour empêcher toute modification de leurs ressources. Lorsqu’Azure Resource Manager atteint le RP cible, le RP cible doit modifier l’état de la ressource cible (pour supprimer les détails sur le point de terminaison privé de ses métadonnées). Quand un verrou est configuré sur la ressource cible (ou son groupe de ressources/abonnement), l’opération Azure Resource Manager échoue avec un « Conflit » (et les détails appropriés). La ressource de liaison privée partagée n’est pas supprimée. | Les clients doivent supprimer le verrou sur la ressource cible avant de retenter l’opération de suppression. **Remarque** : Ce problème peut également se produire lorsque des clients essaient de supprimer un service de recherche avec des ressources de liaison privée partagée qui pointent vers des ressources cibles « verrouillées ». |
| L’opération de suppression a échoué | L’opération de suppression Azure Resource Manager asynchrone peut échouer dans de rares cas. Lorsque cette opération échoue, l’interrogation de l’état de l’opération asynchrone présente aux clients un message d’erreur et les détails appropriés. | Réessayez l’opération ultérieurement ou contactez le support Azure si le problème persiste.
| La ressource est bloquée dans l’état « Suppression » | Dans de rares cas, une ressource de liaison privée partagée peut être bloquée dans l’état « Suppression » pendant jusqu’à 8 heures, probablement en raison d’une défaillance catastrophique sur le RP de recherche. | Attendez 8 heures, après quoi la ressource passera à l’état `Failed`, puis réexécutez la demande.|

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les ressources de liaison privée partagée et sur leur utilisation pour sécuriser l’accès au contenu protégé.

+ [Accès au contenu protégé par le biais d’indexeurs](search-indexer-howto-access-private.md)
+ [Référence d’API REST](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources)