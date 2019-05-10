---
title: Conseils sur les performances Azure Cosmos DB pour Java asynchrone
description: Découvrez les options de configuration clientes pour améliorer les performances de base de données Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: sngun
ms.openlocfilehash: 0da5f503c13ce03854964c0d18edd0031e25bb62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235609"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Conseils sur les performances pour Azure Cosmos DB et Java Async

> [!div class="op_single_selector"]
> * [Java asynchrone](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB est une base de données distribuée rapide et flexible qui peut être mise à l’échelle en toute transparence avec une latence et un débit garantis. Vous n’avez pas à apporter de modifications d’architecture majeures ou écrire de code complexe pour mettre à l’échelle votre base de données avec Azure Cosmos DB. La réduction et l’augmentation de l’échelle est aussi simple que le passage d’un appel d’API ou de Kit de développement logiciel (SDK). Toutefois, étant donné qu’Azure Cosmos DB est accessible via des appels réseau, vous pouvez apporter des optimisations côté client de manière à atteindre des performances de pointe quand vous utilisez le [Kit de développement logiciel (SDK) Java Async SQL](sql-api-sdk-async-java.md).

Si vous vous demandez comment améliorer les performances de votre base de données, lisez ce qui suit :

## <a name="networking"></a>Mise en réseau
   <a id="same-region"></a>
1. **Colocalisation des clients dans la même région Azure pour les performances**

    Dans la mesure du possible, placez toutes les applications appelant Azure Cosmos DB dans la même région que la base de données Azure Cosmos DB. Pour une comparaison approximative, les appels à Azure Cosmos DB dans la même région s’effectuent en 1 à 2 ms, mais la latence entre les côtes Ouest et Est des États-Unis est supérieure à 50 ms. Cette latence peut probablement varier d’une requête à l’autre, en fonction de l’itinéraire utilisé par la requête lorsqu’elle passe du client à la limite du centre de données Azure. Pour obtenir la latence la plus faible possible, l’application appelante doit être située dans la même région Azure que le point de terminaison Azure Cosmos DB configuré. Pour obtenir la liste des régions disponibles, voir [Régions Azure](https://azure.microsoft.com/regions/#services).

    ![Illustration de la stratégie de connexion Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Utilisation du kit de développement logiciel (SDK)
1. **Installation du kit de développement logiciel (SDK) le plus récent**

    Les SDK Azure Cosmos DB sont constamment améliorés pour fournir des performances optimales. Consultez les pages du [SDK Azure Cosmos DB](sql-api-sdk-async-java.md) pour déterminer quel est le SDK le plus récent et passer en revue les améliorations.
2. **Utiliser un client Azure Cosmos DB singleton pour la durée de vie de votre application**

    Chaque instance AsyncDocumentClient est thread-safe et effectue une gestion des connexions efficace et une mise en cache d’adresses. Pour permettre une gestion des connexions efficace et améliorer les performances par AsyncDocumentClient, nous vous recommandons d’utiliser une seule instance de AsyncDocumentClient par AppDomain pour la durée de vie de l’application.

   <a id="max-connection"></a>

3. **Paramètre ConnectionPolicy**

    Les requêtes Azure Cosmos DB sont effectuées par le biais de HTTPS/REST durant l’utilisation du Kit de développement logiciel (SDK) Async Java et sont soumises à la taille des pools de connexion maximale par défaut (1 000). Cette valeur par défaut doit être la solution idéale pour la plupart des cas d’usage. Toutefois, au cas où vous avez une grande collection avec plusieurs partitions, vous pouvez définir la taille des pools de connexion maximale à un plus grand nombre (par exemple, 1500) à l’aide de setMaxPoolSize.

4. **Paramétrage des requêtes parallèles pour les collections partitionnées**

    Le SDK SQL Async Java Azure Cosmos DB prend en charge les requêtes parallèles, qui vous permettent d’interroger une collection partitionnée en parallèle. Pour plus d’informations, voir les [exemples de code](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) concernant l’utilisation des kits SDK. Les requêtes parallèles sont conçues pour améliorer la latence des requêtes et le débit sur leur équivalent série.

    (a) Les requêtes parallèles ***Tuning setMaxDegreeOfParallelism\:*** interrogent plusieurs partitions en parallèle. Les données d’une collection partitionnée individuelle sont toutefois extraites en série dans le cadre de la requête. Utilisez donc le paramètre setMaxDegreeOfParallelism pour définir le nombre de partitions qui augmente les chances de résultats de la requête, sous réserve que toutes les autres conditions système restent inchangées. Si vous ne connaissez pas le nombre de partitions, vous pouvez utiliser le paramètre setMaxDegreeOfParallelism pour définir un nombre élevé, et le système sélectionne le minimum (nombre de partitions, entrée fournie par l’utilisateur) comme degré maximal de parallélisme.

    Il est important de noter que les requêtes parallèles produisent de meilleurs résultats si les données sont réparties de manière homogène entre toutes les partitions. Si la collection est partitionnée de telle façon que toutes les données retournées par une requête, ou une grande partie d’entre elles, sont concentrées sur quelques partitions (une partition dans le pire des cas), les performances de la requête sont altérées par ces partitions.

    (b) La requête parallèle ***Tuning setMaxBufferedItemCount\:*** pré-extrait les résultats tandis que le lot de résultats courant est en cours de traitement par le client. La pré-extraction permet d’améliorer la latence globale d’une requête. setMaxBufferedItemCount limite le nombre de résultats pré-extraits. Définir le paramètre setMaxBufferedItemCount sur le nombre de résultats retournés attendu (ou un nombre plus élevé) permet à la requête d’optimiser la pré-extraction.

    La pré-extraction fonctionne de la même façon, quel que soit le paramètre MaxDegreeOfParallelism, et il existe une seule mémoire tampon pour les données de toutes les partitions.

5. **Implémentation de l’interruption à intervalles définis par getRetryAfterInMilliseconds**

    Lors du test de performances, vous devez augmenter la charge jusqu’à une limite d’un petit nombre de requêtes. En cas de limitation, l’application client doit s’interrompre pour l’intervalle de nouvelle tentative spécifié sur le serveur. Le respect de l’interruption garantit un temps d’attente minimal entre chaque tentative.
6. **Augmentation de la taille des instances de votre charge de travail cliente**

    Si vous effectuez des tests à des niveaux de débit élevé (> 50 000 RU/s), l’application cliente peut devenir un goulet d’étranglement en raison du plafonnement sur l’utilisation du processeur ou du réseau. Si vous atteignez ce point, vous pouvez continuer à augmenter le compte Azure Cosmos DB en augmentant la taille des instances de vos applications clientes sur plusieurs serveurs.

7. **Utilisation de l’adressage en fonction du nom**

    Pour éviter de récupérer les ID de ressource de toutes les ressources utilisées pour construire le lien, utilisez l’adressage en fonction du nom dans lequel les liens ont pour format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, et non SelfLinks (\_self) dans lequel les liens ont pour format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`. En outre, ces ressources étant recréés (éventuellement avec le même nom), leur mise en cache peut s’avérer superflue.

   <a id="tune-page-size"></a>
8. **Réglage de la taille de la page des flux de lecture/requêtes pour de meilleures performances**

    Pendant une lecture groupée de documents à l’aide de la fonctionnalité de flux de lecture (par exemple, readDocuments) ou l’émission d’une requête SQL, les résultats sont retournés de façon segmentée si le jeu de résultats est trop grand. Par défaut, les résultats sont retournés dans des segments de 100 éléments ou de 1 Mo, selon la limite atteinte en premier.

    Afin de réduire le nombre de boucles réseau nécessaires pour récupérer tous les résultats applicables, vous pouvez augmenter la taille de la page à 1000 résultats à l’aide de l’en-tête de requête [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). Si vous avez besoin d’afficher uniquement quelques résultats, (par exemple, si votre interface utilisateur ou API d’application retourne seulement 10 résultats à la fois), vous pouvez également réduire la taille de la page à 10 résultats, afin de baisser le débit consommé pour les lectures et requêtes.

    Vous pouvez également définir la taille de la page à l’aide de la méthode setMaxItemCount.

9. **Utilisation du Scheduler approprié (éviter le vol de threads Netty E/S Eventloop)**

    Le Kit de développement logiciel (SDK) Java Async utilise [netty](https://netty.io/) pour les E/S non bloquantes. Le Kit de développement logiciel (SDK) utilise un nombre fixe de threads d’E/S netty eventloop (autant de cœurs de processeur présents sur votre machine) pour l’exécution d’opérations d’E/S. L’Observable retourné par l’API émet le résultat sur l’un des threads netty d’eventloop d’E/S partagés. Il est donc important de ne pas bloquer les threads netty eventloop d’E/S partagés. Un travail intensif de l’UC ou le blocage de l’opération sur le thread netty eventloop d’E/S peut provoquer un interblocage ou réduire considérablement le débit du Kit de développement logiciel (SDK).

    Par exemple, le code suivant exécute un travail intensif de l’UC sur le thread netty eventloop d’E/S :

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Une fois le résultat reçu, si vous souhaitez effectuer un travail intensif de l’UC sur le résultat, vous devez éviter de le faire sur le thread netty eventloop d’E/S. Vous pouvez fournir à la place votre propre Scheduler pour fournir votre propre thread pour l’exécution de votre travail.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    En fonction de votre type de travail, vous devez utiliser le Scheduler RxJava existant approprié pour votre travail. Lire ici [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Pour plus d’informations, consultez la [page GitHub](https://github.com/Azure/azure-cosmosdb-java) concernant le Kit de développement logiciel (SDK) Java Async.

10. **Désactivation de la journalisation du netty** La journalisation de la bibliothèque Netty est bavarde et doit être désactivée (la suppression du journal dans la configuration peut être insuffisante) afin d’éviter des coûts d’UC supplémentaires. Si vous n’êtes pas en mode débogage, désactivez la journalisation de netty en même temps. Par conséquent, si vous utilisez log4j pour supprimer les coûts supplémentaires de l’UC induits par ``org.apache.log4j.Category.callAppenders()`` de netty, ajoutez la ligne suivante à votre codebase :

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Limite des ressources des fichiers ouverts du système d’exploitation** Certains systèmes Linux (par exemple, Red Hat) ont une limite supérieure du nombre de fichiers ouverts et donc du nombre total de connexions. Exécutez la commande suivante pour afficher les limites actuelles :

    ```bash
    ulimit -a
    ```

    Le nombre de fichiers ouverts (nofile) doit être assez grand pour avoir suffisamment d’espace pour la taille de vos pools de connexion configurés et autres fichiers ouverts par le système d’exploitation. Il peut être modifié pour permettre une plus grande taille des pools de connexion.

    Ouvrir le fichier limits.conf :

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Ajoutez/modifiez les lignes suivantes :

    ```
    * - nofile 100000
    ```

12. **Utiliser l’implémentation SSL native pour netty** Netty peut utiliser OpenSSL directement pour la pile implémentation de SSL pour obtenir de meilleures performances. En l’absence de cette configuration, netty reviendra à l’implémentation de SSL par défaut de Java.

    sur Ubuntu :
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    et ajoutez la dépendance suivante aux dépendances maven de votre projet :
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Pour d’autres plateformes (Redhat, Windows, Mac, etc.), reportez-vous à ces instructions https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Stratégie d’indexation
 
1. **Exclusion des chemins d’accès inutilisés de l’indexation pour des écritures plus rapides**

    La stratégie d’indexation d’Azure Cosmos DB vous permet de spécifier les chemins d’accès au document à inclure ou exclure de l’indexation en tirant parti des chemins d’accès d’indexation (setIncludedPaths et setExcludedPaths). L’utilisation des chemins d’accès d’indexation peut offrir des performances d’écriture améliorées et réduire le stockage d’index pour les scénarios dans lesquels les modèles de requête sont connus d’avance, puisque les coûts d’indexation sont directement liés au nombre de chemins d’accès uniques indexés. Par exemple, le code suivant montre comment exclure toute une section de documents (également appelée sous-arborescence) de l’indexation à l’aide du caractère générique « * ».

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Pour plus d’informations, consultez [Stratégies d’indexation d’Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Débit
<a id="measure-rus"></a>

1. **Mesure et réglage pour réduire l’utilisation d’unités de requête par seconde**

    Azure Cosmos DB propose un riche ensemble d’opérations de base de données, dont les requêtes hiérarchiques et relationnelles avec les fonctions définies par l’utilisateur, les procédures stockées et les déclencheurs, qui fonctionnent toutes au niveau des documents d’une collection de base de données. Le coût associé à chacune de ces opérations varie en fonction du processeur, des E/S et de la mémoire nécessaires à l’exécution de l’opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l’exécution des opérations de base de données et à la réponse à la requête de l’application.

    Le débit est provisionné en fonction du nombre [d’unités de requête](request-units.md) défini pour chaque conteneur. La consommation d’unités de requête est évaluée en fonction d’un taux par seconde. Les applications qui dépassent le taux d’unités de requête configuré pour le conteneur associé sont limitées jusqu’à ce que le taux soit inférieur au niveau configuré pour le conteneur. Si votre application requiert un niveau de débit plus élevé, vous pouvez augmenter le débit en provisionnant des unités de requête supplémentaires.

    La complexité d’une requête a un impact sur le nombre d’unités de requête consommées pour une opération. Le nombre de prédicats, la nature des prédicats, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données sources ont tous une influence sur le coût des opérations de requête.

    Pour mesurer la surcharge de toute opération (création, mise à jour ou suppression), inspectez l’en-tête [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) afin de mesurer le nombre d’unités de requête consommées par ces opérations. Vous pouvez également examiner la propriété RequestCharge équivalente dans ResourceResponse<T> ou FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Les frais de la requête retournée dans cet en-tête correspondent à une fraction du débit provisionné. Par exemple, si 2 000 RU/seconde sont provisionnées et que la requête ci-dessus retourne 1000 documents de 1 Ko, le coût de l’opération est de 1000. Par conséquent, en une seconde, le serveur honore uniquement deux requêtes avant de limiter le taux de requêtes suivantes. Pour plus d’informations, consultez [Unités de requête](request-units.md) et la [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Gestion de la limite de taux/du taux de requête trop importants**

    Lorsqu’un client tente de dépasser le débit réservé pour un compte, les performances au niveau du serveur ne sont pas affectées et la capacité de débit n’est pas utilisée au-delà du niveau réservé. Le serveur met fin à la requête de manière préventive avec RequestRateTooLarge (code d’état HTTP 429) et il retourne l’en-tête [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indiquant la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Les kits de développement logiciel (SDK) interceptent tous implicitement cette réponse, respectent l’en-tête retry-after spécifiée par le serveur, puis relancent la requête. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

    Si plusieurs clients opèrent simultanément au-delà du taux de requête, le nombre de nouvelles tentatives par défaut actuellement défini sur 9 en interne par le client peut ne pas suffire. Dans ce cas, le client envoie une exception DocumentClientException avec le code d’état 429 à l’application. Vous pouvez modifier le nombre de nouvelles tentatives par défaut en utilisant setRetryOptions sur l’instance ConnectionPolicy. Par défaut, la DocumentClientException avec le code d’état 429 est retournée après un temps d’attente cumulé de 30 secondes si la requête continue à fonctionner au-dessus du taux de requête. Cela se produit même lorsque le nombre de nouvelles tentatives actuel est inférieur au nombre maximal de nouvelles tentatives, qu’il s’agisse de la valeur par défaut de 9 ou d’une valeur définie par l’utilisateur.

    Alors que le comportement de nouvelle tentative automatique permet d’améliorer la résilience et la facilité d’utilisation pour la plupart des applications, il peut se révéler contradictoire lors de l’exécution de tests de performances, en particulier lors de la mesure de la latence. La latence client observée atteindra un pic si l’expérience atteint la limite de serveur et oblige le kit de développement logiciel (SDK) client à effectuer une nouvelle tentative en silence. Pour éviter des pics de latence lors des expériences de performances, mesurez la charge renvoyée par chaque opération et assurez-vous que les requêtes fonctionnent en dessous du taux de requête réservé. Pour plus d’informations, consultez [Unités de requête](request-units.md).
3. **Conception de documents plus petits pour un débit plus élevé**

    Les frais de requête (le coût de traitement de requête) d’une opération donnée sont directement liés à la taille du document. Des opérations sur des documents volumineux coûtent plus cher que des opérations sur de petits documents.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la conception de votre application pour une mise à l’échelle et de hautes performances, consultez [Partitionnement, clés de partition et mise à l’échelle dans Cosmos DB](partition-data.md).
