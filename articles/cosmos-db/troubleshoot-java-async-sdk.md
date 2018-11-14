---
title: Diagnostiquer et résoudre les problèmes du SDK Azure Cosmos DB Java Async | Microsoft Docs
description: Utiliser des fonctionnalités telles que la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre les problèmes liés à Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: 74813634aad95f163b06717521bb2c746ac3df6b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238827"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Résolution des problèmes liés à l’utilisation du SDK Java Async avec des comptes d’API SQL Azure Cosmos DB
Cet article traite des problèmes courants, des solutions de contournement, des étapes de diagnostic et des outils lors de l’utilisation du [SDK Java Async](sql-api-sdk-async-java.md) avec des comptes d’API SQL Azure Cosmos DB.
Le SDK Java Async fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

Commencez par cette liste :
    * Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement] dans cet article.
    * Notre SDK est [open source sur github](https://github.com/Azure/azure-cosmosdb-java) et nous disposons d’une [section sur les problèmes](https://github.com/Azure/azure-cosmosdb-java/issues) que nous surveillons attentivement. Vérifiez si vous trouvez un problème similaire au vôtre qui dispose déjà d’une solution de contournement.
    * Consultez les [conseils relatifs aux performances](performance-tips-async-java.md) et suivez les pratiques suggérées.
    * Lisez le reste de cet article. Si vous ne trouvez pas de solution, déposez un [problème sur GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problèmes réseau, échec du délai d’expiration de lecture Netty, débit faible, latence élevée

#### <a name="general-suggestions"></a>Recommandations d’ordre général
* Assurez-vous que l’application s’exécute dans la même région que votre compte Cosmos DB. 
* Vérifiez l’utilisation du processeur sur l’ordinateur hôte où l’application est en cours d’exécution. Si l’utilisation du processeur est égale ou supérieure à 90 %, envisagez d’exécuter votre application sur un ordinateur hôte avec une configuration plus élevée ou à répartir la charge sur plusieurs ordinateurs.

#### <a name="connection-throttling"></a>Limitation de la connexion
La limitation de la connexion peut se produire en raison d’une [limite de connexion sur l’ordinateur hôte] ou d’une [insuffisance de ports Azure SNAT (PAT)] :

##### <a name="connection-limit-on-host"></a>Limite de connexion sur l’ordinateur hôte
Certains systèmes Linux (par exemple, « Red Hat ») ont une limite supérieure sur le nombre total de fichiers ouverts. Les sockets dans Linux sont implémentés en tant que fichiers, donc ce nombre limite aussi le nombre total de connexions.
Exécutez la commande suivante :

```bash
ulimit -a
```
Le nombre de fichiers ouverts (« nofile ») doit être suffisamment élevé (au moins le double de la taille de votre pool de connexions). Pour plus d’informations, consultez les [conseils relatifs aux performances](performance-tips-async-java.md).

##### <a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur une machine virtuelle Azure sans adresse IP publique, par défaut les [ports Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) servent à établir des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées à partir de la machine virtuelle vers le point de terminaison Cosmos DB est limité par la [configuration Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

Les ports Azure SNAT sont utilisés uniquement lorsque votre machine virtuelle Azure a une adresse IP privée et qu’un processus à partir de la machine virtuelle tente d’établir une connexion avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation Azure SNAT :
    * Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel de machine virtuelle Azure comme expliqué dans [Activation du point de terminaison de service du réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Lorsque le point de terminaison de service est activé, les requêtes ne sont plus sont envoyées à partir d’une adresse IP publique à Cosmos DB. À la place, l’identité du sous-réseau et du réseau virtuel est envoyée. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, lors de l’activation du point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide des [ACL de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Assignez une adresse IP publique à votre machine virtuelle Azure.

#### <a name="http-proxy"></a>Proxy HTTP

Si vous utilisez un HttpProxy, assurez-vous qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Modèle de codage non valide : blocage du thread d’E/S Netty

Le SDK utilise la bibliothèque d’E/S [Netty](https://netty.io/) pour communiquer avec le service Azure Cosmos DB. Nous avons une API Async et nous utilisons les API E/S sans blocage de Netty. Les tâches d’E/S du SDK sont effectuées sur les threads d’E/S de Netty. Le nombre de threads d’E/S de Netty est configuré pour être le même que le nombre de cœurs de processeur de l’ordinateur de l’application. Les threads d’E/S de Netty sont uniquement destinés à être utilisés pour les tâches d’E/S sans blocage de Netty. Le SDK retourne le résultat d’appel de l’API sur l’un des threads d’E/S de Netty au code de l’application. Si l’application, après avoir reçu les résultats sur le thread Netty, effectue une opération de longue durée sur le thread Netty, le SDK risque de ne pas avoir un nombre suffisant de threads d’E/S pour effectuer son travail d’E/S interne. Ce type de codage d’application peut entraîner un débit faible, une latence élevée et des échecs `io.netty.handler.timeout.ReadTimeoutException`. La solution de contournement consiste à basculer le thread lorsque vous savez que l’opération prendra de temps.

   Par exemple, l’extrait de code suivant montre que si vous effectuez un travail de longue durée, qui prend plus que quelques millisecondes, sur le thread Netty, vous pourriez aboutir à un état où aucun thread d’E/S Netty n’est présent pour traiter le travail d’E/S, et par conséquent, vous obtenez ReadTimeoutException :
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   La solution de contournement consiste à modifier le thread sur lequel vous effectuez le travail qui prend du temps. Définissez une instance de singleton de Scheduler pour votre application :
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   À chaque fois que vous devez effectuer un travail de longue durée (par exemple, travail exigeant en calcul, blocage des E/S), basculez le thread vers un Worker fourni par votre `customScheduler` à l’aide de l’API `.observeOn(customScheduler)`.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
En utilisant `observeOn(customScheduler)`, vous libérez le thread d’E/S Netty et basculez vers votre propre thread personnalisé fourni par customScheduler. Cette modification résout le problème et vous ne recevrez plus d’échec `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="connection-pool-exhausted-issue"></a>Problème de pool de connexions épuisé

`PoolExhaustedException` est un échec côté client. Si vous obtenez souvent cet échec, cela indique que votre charge de travail d’application est plus élevée que ce que le pool de connexions SDK peut gérer. Augmenter la taille du pool de connexions ou répartir la charge sur plusieurs applications peuvent aider à résoudre le problème.

### <a name="request-rate-too-large"></a>Taux de requêtes trop élevé
Cet échec est un échec côté serveur indiquant que vous avez consommé votre débit provisionné et que devez réessayer ultérieurement. Si vous obtenez souvent cet échec, envisagez d’augmenter le débit de la collection.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Échec de connexion à l’émulateur Azure Cosmos DB

Le certificat HTTPS de l’émulateur Cosmos DB est auto-signé. Pour que le SDK fonctionne avec l’émulateur, vous devez importer le certificat de l’émulateur dans Java TrustStore. Cette procédure est expliquée [ici](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Activer la journalisation de SDK client

Le SDK Java asynchrone utilise SLF4j en tant que façade de journalisation qui prend en charge la journalisation dans les frameworks de journalisation populaires comme log4j et logback.

Par exemple, si vous souhaitez utiliser log4j en tant que framework de journalisation, ajoutez les bibliothèques suivantes dans votre chemin de classe Java :

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Ajoutez également une configuration log4j :
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Pour plus d’informations, consultez le [manuel de journalisation sfl4j](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Statistiques réseau du système d’exploitation
Exécutez la commande netstat pour avoir une idée du nombre de connexions dans l’état `Established`, dans l’état `CLOSE_WAIT`, etc.

Sous Linux, vous pouvez exécuter la commande suivante :
```bash
netstat -nap
```
Filtrez le résultat uniquement sur les connexions au point de terminaison Cosmos DB.

Le nombre de connexions au point de terminaison Cosmos DB dans l’état `Established` ne doit pas être supérieur à la taille du pool de connexions configuré.

S’il existe de nombreuses connexions au point de terminaison Cosmos DB dans l’état `CLOSE_WAIT` (par exemple, plus de 1 000 connexions), cela indique que les connexions sont établies et détruites rapidement, ce qui peut causer des problèmes. Pour plus d’informations, consultez la section [Problèmes courants et solutions de contournement].

 <!--Anchors-->
[Problèmes courants et solutions de contournement]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de connexion sur l’ordinateur hôte]: #connection-limit-on-host
[Insuffisance de ports Azure SNAT (PAT)]: #snat


