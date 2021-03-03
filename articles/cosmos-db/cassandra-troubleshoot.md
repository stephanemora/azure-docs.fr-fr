---
title: Résoudre des erreurs courantes dans l’API Cassandra dans Azure Cosmos DB
description: Ce document décrit les manières de résoudre des problèmes couramment rencontrés dans l’API Cassandra dans Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658569"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Résoudre des problèmes courants dans l’API Cassandra dans Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

L’API Cassandra dans Azure Cosmos DB est une couche de compatibilité qui assure la [prise en charge du protocole filaire](cassandra-support.md) pour la base de données Apache Cassandra open source populaire, et qui est optimisée par [Azure Cosmos DB](./introduction.md). En tant que service natif Cloud entièrement managé, Azure Cosmos DB fournit des [garanties de disponibilité, de débit et de cohérence](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) pour l’API Cassandra. Ces garanties ne sont pas possibles dans des implémentations héritées d’Apache Cassandra. L’API Cassandra facilite également les opérations de plateforme sans maintenance et les mises à jour correctives sans temps d’arrêt. Par conséquent, un grand nombre des ses opérations principales diffèrent de celles d’Apache Cassandra. Nous recommandons donc des paramètres et des approches spécifiques pour éviter les erreurs courantes. 

Cet article décrit les erreurs et solutions courantes pour les applications qui utilisent l’API Cassandra dans Azure Cosmos DB. Si votre erreur n’est pas répertoriée ci-dessous et si vous rencontrez une erreur lors de l’exécution d’une [opération prise en charge dans l’API Cassandra](cassandra-support.md), où l’erreur est *absente lors de l’utilisation d’Apache Cassandra native*, [créez une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Il s’agit d’une exception de wrapper de niveau supérieur avec un grand nombre de causes possibles et d’exceptions internes, dont la plupart peuvent être liées au client. 
### <a name="solution"></a>Solution
Voici quelques causes et solutions populaires : 
- Délai d’inactivité d’Azure LoadBalancers : Cela peut également se manifester sous la forme `ClosedConnectionException`. Pour résoudre ce problème, définissez le paramètre Keep Alive dans le pilote (voir [ci-dessous](#enable-keep-alive-for-java-driver)) et augmentez les paramètres Keep Alive dans le système d’exploitation. Vous pouvez également [ajuster le délai d’inactivité dans Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Épuisement des ressources de l’application cliente :** Vérifiez que les ordinateurs clients disposent de suffisamment de ressources pour traiter la demande. 

## <a name="cannot-connect-to-host"></a>Impossible de se connecter à l’hôte
Le message d’erreur suivant peut s’afficher : `Cannot connect to any host, scheduling retry in 600000 milliseconds`. 

### <a name="solution"></a>Solution
Il peut s’agir de l’épuisement des ressources SNAT côté client. Pour écarter ce problème, suivez les étapes indiquées dans [SNAT pour les connexions sortantes](../load-balancer/load-balancer-outbound-connections.md). Il peut également s’agir d’un problème de délai d’inactivité où l’équilibreur de charge Azure dispose d’un délai d’inactivité de quatre minutes par défaut. Consultez la documentation à la rubrique [Délai d’inactivité de l’équilibreur de charge](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Activez tcp-keep alive à partir des paramètres du pilote (voir [ci-dessous](#enable-keep-alive-for-java-driver)) et définissez l’intervalle `keepAlive` sur le système d’exploitation sur une valeur inférieure à quatre minutes.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
Le nombre total d’unités de requête consommées est supérieur au nombre d’unités de requête approvisionnées sur l’espace de clés ou la table. Les requêtes sont donc limitées.
### <a name="solution"></a>Solution
Envisagez de mettre à l’échelle le débit affecté à un espace de clés ou à une table à partir du portail Azure (voir [ici](manage-scale-cassandra.md) pour les opérations de mise à l’échelle dans l’API Cassandra). Vous pouvez également implémenter une stratégie de nouvelle tentative. Pour Java, consultez exemples de nouvelles tentatives pour le [pilote v3.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) et le [pilote v4.x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Consultez également [Extensions Azure Cosmos Cassandra pour Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException, même avec un débit suffisant 
Le système semble limiter les demandes en dépit d’un débit suffisant pour être approvisionné pour le volume de demandes et/ou le coût d’unité de requête consommé. Il existe deux causes possibles de la limitation inattendue du débit :
- **Opérations au niveau du schéma :** L’API Cassandra implémente un budget de débit du système pour les opérations au niveau du schéma (CREATE TABLE, ALTER TABLE, DROP TABLE). Ce budget doit être suffisant pour les opérations de schéma dans un système de production. Toutefois, si vous avez un grand nombre d’opérations au niveau du schéma, il est possible que vous dépassiez cette limite. Ce budget n’étant pas contrôlé par l’utilisateur, vous devrez envisager de réduire le nombre d’opérations de schéma en cours d’exécution. Si cette action ne résout pas le problème, ou si elle n’est pas possible pour votre charge de travail, vous pouvez [créer une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Asymétrie des données :** Lorsque le débit est approvisionné dans l’API Cassandra, il est divisé de manière égale entre les partitions physiques, et chaque partition physique a une limite supérieure. Si une quantité importante de données est insérée ou interrogée à partir d’une partition particulière, il est possible d’être limité par le débit en dépit de la configuration d’une grande quantité de débit global (unités de requête) pour cette table. Examinez votre modèle de données et assurez-vous que vous n’avez pas d’asymétrie excessive pouvant provoquer des partitions actives. 

## <a name="intermittent-connectivity-errors-java"></a>Erreurs de connectivité intermittente (Java) 
La connexion s’interrompt ou expire de manière inattendue.

### <a name="solution"></a>Solution 
Les pilotes Apache Cassandra pour Java fournissent deux stratégies de reconnexion natives : `ExponentialReconnectionPolicy` et `ConstantReconnectionPolicy`. Par défaut, il s’agit de `ExponentialReconnectionPolicy`. Toutefois, pour l’API Cassandra dans Azure Cosmos DB, nous vous recommandons de `ConstantReconnectionPolicy` avec un délai de 2 secondes. Consultez la [documentation du pilote](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/) pour le pilote Java v4.x, et voyez [ici](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) pour obtenir de l’aide sur Java 3.x. Consultez également ci-dessous les exemples de [configuration de ReconnectionPolicy pour le pilote Java](#configuring-reconnectionpolicy-for-java-driver).

## <a name="error-with-load-balancing-policy"></a>Erreur avec la stratégie d’équilibrage de charge

Si vous avez implémenté une stratégie d’équilibrage de charge dans la version 3.x du pilote Java Datastax, avec un code similaire à ce qui suit :

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

Si la valeur de `withLocalDc()` ne correspond pas au centre de données du point de contact, il se peut que vous rencontriez une erreur très intermittente : `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`. 

### <a name="solution"></a>Solution 
Implémentez [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (vous devrez peut-être mettre à niveau la version mineure de Datastax pour la faire fonctionner) :

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Le décompte échoue sur une table volumineuse
Lors de l’exécution de `select count(*) from table` ou similaire pour un grand nombre de lignes, le délai d’attente du serveur expire.

### <a name="solution"></a>Solution 
Si vous utilisez un client CQLSH local, vous pouvez essayer de modifier les paramètres `--connect-timeout` ou `--request-timeout` (voir plus de détails [ici](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)). Si ce nombre n’est pas suffisant et que le délai d’attente du décompte continue d’expirer, vous pouvez obtenir le décompte des enregistrements à partir de la télémétrie principale d’Azure Cosmos DB en accédant à l’onglet Métriques dans Portail Azure, en sélectionnant la métrique `document count`, puis en ajoutant un filtre pour la base de données ou la collection (l’équivalent de la table dans Azure Cosmos DB). Vous pouvez alors pointer la souris sur le graphique résultant sur le moment pour lequel vous souhaitez connaître le décompte du nombre d’enregistrements.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="affichage des mesures":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Configuration de ReconnectionPolicy pour pilote Java

### <a name="version-3x"></a>Version 3.x

Pour la version 3.x du pilote Java, configurez la stratégie de reconnexion lors de la création d’un objet cluster :

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Version 4.x

Pour la version 4.x du pilote Java, configurez la stratégie de reconnexion en remplaçant les paramètres dans le fichier `reference.conf` :

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

## <a name="enable-keep-alive-for-java-driver"></a>Activer Keep Alive pour pilote Java

### <a name="version-3x"></a>Version 3.x

Pour la version 3.x du pilote Java, définissez Keep Alive lors de la création d’un objet cluster, puis assurez-vous que l’option Keep Alive est [activée dans le système d’exploitation](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) :

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

Pour la version 4.x du pilote Java, définissez Keep Alive en remplaçant les paramètres dans `reference.conf`, puis assurez-vous que l’option Keep Alive est [activée dans le système d’exploitation](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) :

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
