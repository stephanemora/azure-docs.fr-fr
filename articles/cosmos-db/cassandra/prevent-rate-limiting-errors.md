---
title: Empêcher les erreurs de limitation de débit pour l’API Azure Cosmos DB pour Cassandra
description: Empêchez les opérations de votre API Azure Cosmos DB pour Cassandra de déclencher des erreurs de limitation de débit avec la fonctionnalité de nouvelles tentatives côté serveur (SSR).
author: dileepraotv-github
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/11/2021
ms.author: turao
ms.openlocfilehash: 469dc3f87bb7c783f2c3138e2bcf592c85312006
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129757619"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-cassandra-operations"></a>Empêcher les erreurs de limitation de débit pour les opérations de l’API Azure Cosmos DB pour Cassandra
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Le coût de toutes les opérations de base de données, normalisé par Azure Cosmos DB, est exprimé en unités de requête (RU). Les unités de requête correspondent en quelque sorte à une devise de performances, faisant abstraction des ressources système (UC, IOPS, mémoire, etc.) requises pour effectuer les opérations de base de données prises en charge par Azure Cosmos DB.

Les opérations de l’API Cassandra Azure Cosmos DB peuvent échouer avec des erreurs de limitation de débit (OverloadedException/429) si elles dépassent la limite de débit d’une table (RU). Cela peut être géré par le client comme décrit dans la stratégie de nouvelles tentatives du [document de recommandations de l’API Cassandra](https://devblogs.microsoft.com/cosmosdb/cassandra-api-java/#retry-policy). Si la stratégie de nouvelles tentatives du client ne peut pas être implémentée pour gérer l’échec dû à une erreur de limitation de débit, nous pouvons utiliser la fonctionnalité de nouvelles tentatives côté serveur (SSR) où les opérations qui dépassent la limite de débit d’une table seront automatiquement relancées après un court délai. Il s’agit d’un paramètre au niveau du compte qui s’applique à tous les espaces clés et à toutes les tables du compte.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à votre compte d’API Azure Cosmos DB pour Cassandra.

3. Accédez au volet **Fonctionnalités** dans la section **Paramètres**.

4. Sélectionnez **Nouvelle tentative côté serveur**.

5. Cliquez sur **Activer** pour activer cette fonctionnalité pour tous les collections dans votre compte.

:::image type="content" source="./media/prevent-rate-limiting-errors/prevent-rate-limiting-errors.png" alt-text="Capture d’écran de la fonctionnalité de nouvelles tentatives côté serveur pour l’API Azure Cosmos DB pour Cassandra":::

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

1. Vérifiez si la fonctionnalité SSR est déjà activée pour votre compte :

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

2. **Activez** la fonctionnalité SSR pour toutes les tables dans votre compte de base de données. Jusqu’à quinze minutes peuvent être nécessaires pour que cette modification soit prise en compte.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra DisableRateLimitingResponses
   ```

3. La commande suivante **désactive** la nouvelle tentative côté serveur pour toutes les tables dans votre compte de base de données en supprimant `DisableRateLimitingResponses` de la liste des capacités. Jusqu’à quinze minutes peuvent être nécessaires pour que cette modification soit prise en compte.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra
   ```

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-are-requests-retried"></a>Comment les demandes font-elles l’objet d’une nouvelle tentative ?

Les demandes font l’objet d’une nouvelle tentative en continu (de façon répétée) jusqu’à ce qu’un délai de 60 secondes soit atteint. Si le délai d’expiration est atteint, le client reçoit une erreur d’expiration du délai de lecture ou d’écriture en conséquence.

### <a name="when-is-ssr-most-beneficial"></a>Quand la fonctionnalité SSR est-elle le plus avantageuse ?

La fonctionnalité de nouvelles tentatives côté serveur (SSR) est la plus avantageuse en cas de pic soudain pendant une courte durée de moins d’une minute, ce qui permet d’éviter les erreurs de limitation. Si la charge de travail a augmenté et reste constamment au-dessus de la RU spécifiée, la fonctionnalité SSR ne sera pas d’un grand secours. Il est suggéré d’augmenter la RU de manière appropriée.

### <a name="suggested-client-side-settings"></a>Quels sont les paramètres suggérés côté client ?

Une fois la fonctionnalité SSR activée, l’application cliente doit augmenter le délai d’expiration de lecture au-delà des 60 secondes du paramètre des nouvelles tentatives du serveur. Nous vous recommandons 90 secondes pour plus de sécurité.

SocketOptions setReadTimeoutMillis DefaultDriverOption.REQUEST_TIMEOUT


### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>Comment puis-je superviser les effets d’une nouvelle tentative côté serveur ?

Vous pouvez afficher les erreurs de limitation de débit (429) qui font l’objet d’une nouvelle tentative côté serveur dans le volet Métriques de Cosmos DB. Ces erreurs ne sont pas dirigées vers le client quand la fonctionnalité SSR est activée, car elles sont gérées et font l’objet d’une nouvelle tentative côté serveur.

Vous pouvez rechercher les entrées de journal contenant *estimatedDelayFromRateLimitingInMilliseconds* dans vos [journaux de ressources Cosmos DB](../cosmosdb-monitor-resource-logs.md).

### <a name="will-server-side-retry-affect-my-consistency-level"></a>La nouvelle tentative côté serveur a-t-elle une incidence sur mon niveau de cohérence ?

La nouvelle tentative côté serveur n’a pas d’impact sur les niveaux de cohérence. Les demandes font l’objet d’une nouvelle tentative côté serveur si elles subissent une limitation de débit (Erreur 429).

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>La nouvelle tentative côté serveur affecte-t-elle tout type d’erreur que mon client peut recevoir ?

Non, la nouvelle tentative côté serveur affecte uniquement les erreurs de limitation de débit (429) en effectuant une nouvelle tentative côté serveur. Cette fonctionnalité vous évide d’avoir à gérer les erreurs de limitation de débit dans l’application cliente. Toutes les [autres erreurs](troubleshoot-common-issues.md) sont dirigées vers le client.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la résolution des erreurs courantes, consultez cet article :

* [Résoudre des problèmes courants dans l’API Azure Cosmos DB pour Cassandra](troubleshoot-common-issues.md)


Consultez les articles suivants pour en savoir plus sur le provisionnement du débit dans Azure Cosmos DB :

* [Unités de requête et débit dans Azure Cosmos DB](../request-units.md)
* [Provisionner le débit sur les conteneurs et les bases de données](../how-to-provision-throughput-cassandra.md) 
* [Meilleures pratiques en matière de clés de partition](../cassandra-partitioning.md)

