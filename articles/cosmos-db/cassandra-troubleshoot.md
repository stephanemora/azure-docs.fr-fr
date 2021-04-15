---
title: Résoudre des erreurs courantes dans l’API Cassandra dans Azure Cosmos DB
description: Cet article décrit les problèmes courants rencontrés dans l’API Cassandra dans Azure Cosmos DB et la façon de les résoudre.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967351"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Résoudre des problèmes courants dans l’API Cassandra dans Azure Cosmos DB

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

L’API Cassandra dans [Azure Cosmos DB](./introduction.md) est une couche de compatibilité qui assure la [prise en charge du protocole filaire](cassandra-support.md) pour la base de données Apache Cassandra open source.

Cet article décrit les erreurs et solutions courantes pour les applications qui utilisent l’API Cassandra dans Azure Cosmos DB. Si votre erreur n’est pas répertoriée et si vous rencontrez une erreur lorsque vous exécutez une [opération prise en charge dans Cassandra](cassandra-support.md), mais que l’erreur n’est pas présente lorsque vous utilisez Apache Cassandra en mode natif, [créez une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>En tant que service natif Cloud complètement managé, Azure Cosmos DB fournit des [garanties de disponibilité, de débit et de cohérence](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) pour l’API Cassandra. L’API Cassandra facilite également les opérations de plateforme sans maintenance et les mises à jour correctives sans temps d’arrêt.
>
>Ces garanties ne sont pas possibles dans les implémentations précédentes d’Apache Cassandra, de sorte que de nombreuses opérations back end de l’API Cassandra diffèrent d’Apache Cassandra. Nous vous recommandons d’utiliser des paramètres et des approches spécifiques pour éviter les erreurs courantes.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Cette erreur est une exception de wrapper de niveau supérieur avec un grand nombre de causes possibles et d’exceptions internes, dont la plupart peuvent être liées au client.

Causes et solutions courantes :

- **Délai d’inactivité d’Azure LoadBalancers** : Ce problème peut également se manifester sous la forme `ClosedConnectionException`. Pour résoudre le problème, définissez le paramètre Keep Alive dans le pilote (consultez [Activer Keep Alive pour le pilote Java](#enable-keep-alive-for-the-java-driver)) et augmentez les paramètres Keep Alive dans votre système d’exploitation, ou [ajustez le délai d’inactivité dans Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Épuisement des ressources de l’application cliente** : Vérifiez que les ordinateurs clients disposent de suffisamment de ressources pour traiter la demande.

## <a name="cant-connect-to-a-host"></a>Impossible de se connecter à un hôte

Vous pouvez voir l’erreur suivante : « Impossible de se connecter à un hôte, planification d’une nouvelle tentative dans 600 000 millisecondes ».

Cette erreur peut être causée par l’épuisement de la traduction d’adresses réseau sources (SNAT) côté client. Pour écarter ce problème, suivez les étapes indiquées dans [SNAT pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md).

Il peut également s’agir d’un problème de délai d’inactivité où l’équilibreur de charge Azure dispose d’un délai d’inactivité de quatre minutes par défaut. Consultez [Délai d’inactivité de l’équilibreur de charge](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Activez Keep Alive pour le pilote Java](#enable-keep-alive-for-the-java-driver) et définissez l’intervalle `keepAlive` sur le système d’exploitation sur une valeur inférieure à quatre minutes.

## <a name="overloadedexception-java"></a>OverloadedException (Java)

Les requêtes sont limitées, car le nombre total d’unités de requête consommées est supérieur au nombre d’unités de requête que vous avez approvisionné sur l’espace de clés ou la table.

Envisagez de mettre à l’échelle le débit attribué à un espace de clés ou à une table à partir du portail Azure (voir [Mettre à l’échelle de manière élastique un compte d’API Cassandra Azure Cosmos DB](manage-scale-cassandra.md)) ou d’implémenter une stratégie de nouvelle tentative.

Pour Java, consultez les exemples de nouvelles tentatives pour le [pilote v3.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) et le [pilote v4.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Consultez également [Extensions Azure Cosmos Cassandra pour Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>OverloadedException malgré un débit suffisant

Le système semble limiter les requêtes bien que le débit approvisionné soit suffisant pour le volume de requêtes ou le coût d’unités de requête consommé. Il existe deux causes possibles :

- **Opérations au niveau du schéma** : L’API Cassandra implémente un budget de débit du système pour les opérations au niveau du schéma (CREATE TABLE, ALTER TABLE, DROP TABLE). Ce budget doit être suffisant pour les opérations de schéma dans un système de production. Toutefois, si vous avez un grand nombre d’opérations au niveau du schéma, vous risquez de dépasser cette limite.

  Comme le budget n’est pas contrôlé par l’utilisateur, envisagez de réduire le nombre d’opérations de schéma que vous exécutez. Si cette action ne résout pas le problème, ou si elle n’est pas possible pour votre charge de travail, [créez une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Asymétrie des données** : Lorsque le débit est approvisionné dans l’API Cassandra, il est divisé de manière égale entre les partitions physiques, et chaque partition physique a une limite supérieure. Si une quantité importante de données est insérée ou interrogée à partir d’une partition particulière, le débit peut être limité même si vous approvisionnez une grande quantité de débit global (unités de requête) pour cette table.

  Examinez votre modèle de données et assurez-vous qu’il n’y a pas d’asymétrie excessive susceptible de provoquer des partitions chaudes.

## <a name="intermittent-connectivity-errors-java"></a>Erreurs de connectivité intermittente (Java)

La connexion s’interrompt ou expire de manière inattendue.

Les pilotes Apache Cassandra pour Java fournissent deux stratégies de reconnexion natives : `ExponentialReconnectionPolicy` et `ConstantReconnectionPolicy`. Par défaut, il s’agit de `ExponentialReconnectionPolicy`. Toutefois, pour l’API Cassandra dans Azure Cosmos DB, nous vous recommandons `ConstantReconnectionPolicy` avec un délai de deux secondes.

Consultez la [documentation du pilote Java 4.x](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), la [documentation du pilote Java 3.x](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) ou les exemples dans [Configurer ReconnectionPolicy pour le pilote Java](#configure-reconnectionpolicy-for-the-java-driver).

## <a name="error-with-load-balancing-policy"></a>Erreur avec la stratégie d’équilibrage de charge

Vous avez peut-être implémenté une stratégie d’équilibrage de charge dans la version 3.x du pilote Java DataStax, avec un code similaire à ce qui suit :

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Si la valeur de `withLocalDc()` ne correspond pas au centre de données du point de contact, il se peut que vous rencontriez une erreur intermittente : `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`.

Implémentez [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Pour que cela fonctionne, vous devrez peut-être mettre à niveau DataStax à l’aide du code suivant :

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Le décompte échoue sur une grande table

Lorsque vous exécutez `select count(*) from table` ou une commande similaire pour un grand nombre de lignes, le délai d’attente du serveur expire.

Si vous utilisez un client CQLSH local, modifiez les paramètres `--connect-timeout` ou `--request-timeout`. Consultez la page [cqlsh: the CQL shell](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Si le décompte est toujours interrompu, vous pouvez obtenir le décompte des enregistrements à partir de la télémétrie principale d’Azure Cosmos DB en accédant à l’onglet Métriques dans le portail Azure, en sélectionnant la métrique `document count`, puis en ajoutant un filtre pour la base de données ou la collection (l’équivalent de la table dans Azure Cosmos DB). Vous pouvez alors pointer la souris sur le graphique résultant sur le moment pour lequel vous souhaitez connaître le décompte du nombre d’enregistrements.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="affichage des mesures":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Configurer ReconnectionPolicy pour le pilote Java

### <a name="version-3x"></a>Version 3.x

Pour la version 3.x du pilote Java, configurez la stratégie de reconnexion lorsque vous créez un objet cluster :

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Version 4.x

Pour la version 4.x du pilote Java, configurez la stratégie de reconnexion en remplaçant les paramètres dans le fichier `reference.conf` :

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Activer Keep Alive pour le pilote Java

### <a name="version-3x"></a>Version 3.x

Pour la version 3.x du pilote Java, définissez Keep Alive lorsque vous créez un objet cluster, puis assurez-vous que l’option Keep Alive est [activée dans le système d’exploitation](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) :

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Version 4.x

Pour la version 4.x du pilote Java, définissez Keep Alive en remplaçant les paramètres dans `reference.conf`, puis assurez-vous que l’option Keep Alive est [activée dans le système d’exploitation](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) :

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [fonctionnalités prises en charge](cassandra-support.md) dans l’API Cassandra dans Azure Cosmos DB.
- Découvrez comment [migrer d’Apache Cassandra natif vers l’API Cassandra dans Azure Cosmos DB](cassandra-migrate-cosmos-db-databricks.md).
