---
title: Utilisation du support de flux de modification dans Azure Cosmos DB | Microsoft Docs
description: Utilisez le support de flux de modification d’Azure Cosmos DB pour suivre les modifications dans les documents et effectuer des opérations de traitement basées sur les événements tels que des déclencheurs et la mise à jour des systèmes de cache et d’analyse.
keywords: change feed
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 3170ee1b48aa332a8730ba835396761ca5ef44c7
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287323"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Utilisation du support de flux de modification dans Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) est une base de données répliquée mondialement à la fois rapide et flexible, idéale pour les applications d’IoT, de jeux, de vente au détail et de journalisation des compteurs. Ces applications intègrent souvent un modèle de conception qui consiste à utiliser des modifications de données pour déclencher des actions supplémentaires. Voici quelques exemples d’actions supplémentaires : 

* Déclenchement d’une notification ou d’un appel à une API lorsqu’un document est inséré ou modifié ;
* Traitement de flux pour l’IoT ou exécution d’analyses ;
* Déplacement de données supplémentaire via une synchronisation avec un cache, un moteur de recherche ou un entrepôt de données, ou archivage de données dans un stockage à froid.

Le **support de flux de modification** d’Azure Cosmos DB vous permet de créer des solutions efficaces et évolutives pour chacun de ces modèles, comme illustré dans l’image suivante :

![Utilisation du flux de modification d’Azure Cosmos DB pour alimenter les analyses en temps réel et les scénarios de calcul pilotés par les événements](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Le support de flux de modification est fourni pour tous les conteneurs et modèles de données dans Azure Cosmos DB. Toutefois, le flux de modification est lu à l’aide du client SQL et sérialise les éléments au format JSON. En raison du formatage JSON, les clients MongoDB seront confrontés à une incompatibilité entre les documents au format BSON et le flux de modification au format JSON.

## <a name="how-does-change-feed-work"></a>Comment fonctionne le flux de modification ?

Le support de flux de modification dans Azure Cosmos DB consiste à identifier les modifications apportées à une collection Azure Cosmos DB. Il renvoie ensuite la liste chronologique de documents qui ont été modifiés, dans l’ordre dans lequel ils ont été modifiés. Les modifications sont conservées et peuvent être traitées de manière asynchrone et incrémentielle, puis réparties sur un ou plusieurs consommateurs pour un traitement en parallèle. 

Vous pouvez lire le flux de modification de trois manières différentes, comme expliqué plus loin dans cet article :

*   [Utilisation d’Azure Functions](#azure-functions)
*   [Utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB](#sql-sdk)
*   [Utilisation de la bibliothèque du processeur de flux de modification Azure Cosmos DB](#change-feed-processor)

Le flux de modification est disponible pour chaque plage de clés de partition dans la collection de documents et peut ainsi être distribué vers un ou plusieurs consommateurs pour un traitement en parallèle, comme indiqué dans l’image suivante.

![Traitement distribué du flux de modification d’Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Informations supplémentaires :
* Le flux de modification est activé par défaut pour tous les comptes.
* Vous pouvez utiliser votre [débit configuré](request-units.md) dans votre région d’écriture ou toute [région de lecture](distribute-data-globally.md) pour lire à partir du flux de modification comme toute autre opération effectuée à partir d’Azure Cosmos DB.
* Le flux de modification inclut les insertions et les opérations de mise à jour apportées aux documents de la collection. Vous pouvez capturer des suppressions en définissant un indicateur de « suppression réversible » dans vos documents à la place des suppressions. Vous pouvez également définir un délai d’expiration limité pour vos documents via la [fonctionnalité de durée de vie (TTL)](time-to-live.md), par exemple 24 heures, et utiliser la valeur de cette propriété pour capturer les suppressions. Avec cette solution, vous devez traiter les modifications dans un intervalle de temps inférieur à la période d’expiration de durée de vie.
* Chaque modification apportée à un document n’apparaît qu’une seule fois dans le flux des modifications. En outre, les clients gèrent leur logique de points de contrôle. La bibliothèque du processeur de flux des modifications fournit des points de contrôle automatiques, ainsi qu’une sémantique de type « au moins une fois ».
* Seule la dernière modification d’un document donné est incluse dans le journal des modifications. Les modifications intermédiaires peuvent ne pas être disponibles.
* Le flux de modification est trié par ordre de modification au sein de chaque valeur de clé de partition. Il n’existe aucun ordre garanti entre les valeurs de clé de partition.
* Les modifications peuvent être synchronisées à partir de n’importe quel moment donné ; autrement dit, il n’existe aucune période de rétention de données fixes pendant laquelle les modifications sont disponibles.
* Les modifications sont disponibles dans des blocs de plages de clés de partition. Cette fonctionnalité permet le traitement en parallèle de modifications de grandes collections par plusieurs consommateurs/serveurs.
* Les applications peuvent demander plusieurs flux de modification simultanément sur la même collection.
* ChangeFeedOptions.StartTime peut servir à fournir un point de départ initial, par exemple, pour trouver le jeton de continuation correspondant à une heure donnée. S’il est spécifié, ContinuationToke l’emporte sur les valeurs StartTime et StartFromBeginning. La précision de ChangeFeedOptions.StartTime est ’environ 5 secondes. 

## <a name="use-cases-and-scenarios"></a>Cas d'utilisation et scénarios

Le flux de modification permet un traitement efficace des jeux de données importants avec un volume d’écritures élevé et offre une alternative à l’interrogation des jeux de données pour identifier les changements apportés. 

Par exemple, avec un flux de modification, vous pouvez effectuer efficacement les tâches suivantes :

* Mettre à jour un cache, l’index de recherche ou un entrepôt de données avec les données stockées dans Azure Cosmos DB.
* Implémenter la hiérarchisation et l’archivage des données de niveau application, autrement dit, stocker les « données à chaud » dans Azure Cosmos DB et archiver les « données à froid » dans [Stockage Blob Azure](../storage/common/storage-introduction.md) ou [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Effectuer des migrations sans aucun temps d’arrêt vers un autre compte Azure Cosmos DB avec un schéma de partitionnement différent.
* Implémenter des [pipelines lambda sur Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) avec Azure Cosmos DB. Azure Cosmos DB fournit une solution de base de données évolutive qui peut gérer l’ingestion et l’interrogation, et implémenter des architectures lambda avec un faible coût total de possession. 
* Recevoir et stocker des données d’événement à partir d’appareils, de capteurs, de l’infrastructure et des applications, et traiter ces événements en temps réel avec [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) ou [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

L’image suivante montre de quelle manière des pipelines lambda utilisés à la fois pour ingérer et interroger des données à l’aide d’Azure Cosmos DB peuvent utiliser le support de flux de modification : 

![Pipeline lambda Azure Cosmos DB pour l’ingestion et l’interrogation](./media/change-feed/lambda.png)

En outre, dans les applications web et mobiles [sans serveur](http://azure.com/serverless), vous pouvez suivre les événements tels que les modifications apportées au profil, aux préférences ou à l’emplacement de votre client pour déclencher certaines actions, comme l’envoi de notifications Push à leurs appareils avec [Azure Functions](#azure-functions). Si vous utilisez Azure Cosmos DB pour créer un jeu, vous pouvez, par exemple, utiliser le flux de modification pour implémenter des classements en temps réel basés sur des scores de jeux terminés.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Utilisation d’Azure Functions 

Si vous utilisez Azure Functions, la façon la plus simple pour vous connecter à un flux de modification Azure Cosmos DB consiste à ajouter un déclencheur Azure Cosmos DB à votre application Azure Functions. Lorsque vous créez un déclencheur Azure Cosmos DB dans une application Azure Functions, vous sélectionnez la collection Azure Cosmos DB à laquelle vous souhaitez vous connecter, et la fonction se déclenche chaque fois qu’une modification est apportée à la collection sélectionnée. 

Les déclencheurs peuvent être créés via le portail Azure Functions, via le portail Azure Cosmos DB ou par programmation. Pour en savoir plus, consultez l’article [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

Le [SDK SQL](sql-api-sdk-dotnet.md) pour Azure Cosmos DB comprend toutes les fonctionnalités nécessaires pour lire et gérer un flux de modification. De telles fonctionnalités impliquent également un certain nombre de responsabilités. Si vous voulez gérer les points de contrôle et les numéros de séquence des documents, et bénéficier d’un contrôle granulaire sur les clés de partition, alors utiliser le Kit de développement logiciel (SDK) peut être une bonne alternative.

Cette section vous explique comment utiliser le SDK SQL pour exploiter les flux de modification.

1. Commencez par consulter les ressources suivantes disponibles dans appconfig. Des instructions sur la récupération de la clé d’autorisation et du point de terminaison sont disponibles dans [Mise à jour de votre chaîne de connexion](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Créez le client comme suit :

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Récupérez les plages de clés de partition :

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Appelez ExecuteNextAsync pour chaque plage de clés de partition :

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> Au lieu de `ChangeFeedOptions.PartitionKeyRangeId`, vous pouvez utiliser `ChangeFeedOptions.PartitionKey` pour spécifier une clé de partition unique pour laquelle obtenir un flux de modification. Par exemple : `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Si vous disposez de plusieurs lecteurs, vous pouvez utiliser **ChangeFeedOptions** pour répartir la charge de lecture sur plusieurs threads ou clients.

Ces quelques lignes de code suffisent pour commencer à lire le flux de modification. Vous pouvez obtenir le code complet utilisé dans cet article à partir du [référentiel GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

Dans le code de l’étape 4 ci-dessus, la valeur **ResponseContinuation** de la dernière ligne comprend le dernier numéro de séquence logique du document, que vous utiliserez la prochaine fois que vous lirez de nouveaux documents après ce numéro de séquence. L’élément **StartTime** de la méthode **ChangeFeedOption** vous permet d’élargir le champ de récupération des documents. Ainsi, si la valeur **ResponseContinuation** est nulle mais que la valeur **StartTime** renvoie à une date antérieure, vous obtiendrez tous les documents modifiés depuis l’heure correspondant à la valeur **StartTime**. Par contre, si la valeur **ResponseContinuation** n’est pas nulle, le système collectera tous les documents modifiés depuis ce numéro de séquence logique.

Ainsi, votre tableau de points de contrôle conserve uniquement le numéro de séquence logique pour chaque partition. Toutefois, si vous ne souhaitez pas gérer les partitions, les points de contrôle, le numéro de séquence logique, l’heure de début, etc., l’option la plus simple consiste à utiliser la bibliothèque du processeur de flux de modification.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Utilisation de la bibliothèque du processeur de flux de modification 

La [bibliothèque du processeur de flux de modification Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) vous permet de répartir facilement le traitement des événements sur plusieurs consommateurs. Cette bibliothèque simplifie la lecture des modifications sur plusieurs partitions et threads exécutés en parallèle.

Le principal avantage de la bibliothèque du processeur de flux de modification est que vous n’êtes pas obligé de gérer chaque partition et jeton de liaison, et que vous n’avez pas à interroger chaque collection manuellement.

La bibliothèque du processeur de flux de modification simplifie la lecture des modifications sur plusieurs partitions et threads exécutés en parallèle.  Elle gère automatiquement la lecture des modifications sur les différentes partitions à l’aide d’un mécanisme de bail. Comme vous pouvez le voir dans l’image suivante, si vous démarrez deux clients qui utilisent la bibliothèque du processeur de flux de modification, ces deux clients se partagent la charge de travail. Plus vous ajoutez de clients, plus la charge de travail est répartie entre ces clients.

![Traitement distribué du flux de modification d’Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Le client de gauche a été démarré en premier et a commencé à analyser l’ensemble des partitions. Puis, lors du démarrage du deuxième client, le premier client lui a transmis certains des baux. Comme vous pouvez le voir, cela permet de distribuer facilement la charge de travail entre différents ordinateurs et clients.

Notez que si vous avez deux fonctions Azure sans serveur qui analysent la même collection et utilisent le même bail, les deux fonctions peuvent obtenir des documents différents en fonction de la manière dont la bibliothèque du processeur décide de traiter les partitions.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Présentation de la bibliothèque du processeur de flux de modification

L’implémentation de la bibliothèque du processeur de flux de modification fait appel à quatre composants principaux : la collection analysée, la collection de baux, l’hôte de processeur et les consommateurs. 

> [!WARNING]
> La création d’une collection a des conséquences d’un point de vue tarifaire. En effet, vous réservez une part du débit pour que l’application puisse communiquer avec Azure Cosmos DB. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Collecte analysé :** la collection analysée est constituée des données à partir desquelles le flux de modification est généré. Toutes les insertions et les modifications apportées à la collection analysée sont répercutées dans le flux de modification de la collection. 

**Collection de baux :** la collection de baux coordonne le traitement du flux de modification parmi les threads de travail. Une collection distincte est utilisée pour stocker les baux avec un bail par partition. Il est préférable de stocker cette collection de baux sur un autre compte, avec une zone d’écriture plus proche de l’endroit où le processeur de flux de modification s’exécute. Un objet de bail contient les attributs suivants : 
* Propriétaire : spécifie l’hôte détenteur du bail.
* Continuation : spécifie la position (jeton de continuation) d’une partition particulière dans le flux de modification.
* Horodatage : heure de dernière mise à jour du bail. Vous pouvez utiliser l’horodatage pour vérifier si le bail est considéré comme ayant expiré. 

**Hôte de processeur :** chaque hôte détermine le nombre de partitions à traiter en fonction de la quantité d’autres instances d’hôtes ayant des baux actifs. 
1.  Quand un hôte démarre, il acquiert des baux pour équilibrer la charge de travail parmi tous les hôtes. Un hôte renouvelle périodiquement les baux afin que ceux-ci restent actifs. 
2.  Un hôte crée un point de contrôle pour le dernier jeton de continuation de son bail pour chaque lecture. Pour garantir la sécurité de l’accès concurrentiel, un hôte vérifie l’ETag pour chaque mise à jour de bail. D’autres stratégies de point de contrôle sont également prises en charge.  
3.  En cas d’arrêt, un hôte libère tous les baux mais conserve les informations de continuation, afin de pouvoir reprendre ultérieurement la lecture à partir du point de contrôle stocké. 

À ce stade, le nombre d’hôtes ne peut pas être supérieur au nombre de partitions (baux).

**Consommateurs :** les consommateurs, ou Workers, sont des threads qui exécutent le traitement du flux de modification initié par chaque hôte. Chaque hôte de processeur peut avoir plusieurs consommateurs. Chaque consommateur lit le flux de modification à partir de la partition à laquelle il est assigné, et il informe son hôte des modifications et des baux expirés.

Pour mieux comprendre comment ces quatre éléments du processeur de flux de modification interagissent, examinons un exemple à l’aide du schéma suivant. La collection analysée stocke des documents et utilise « city » comme clé de partition. Nous constatons que la partition bleue contient des documents avec le champ « city » de « A-E », et ainsi de suite. Il existe deux hôtes, chacun avec deux consommateurs lisant à partir des quatre partitions en parallèle. Les flèches indiquent les consommateurs lisant à partir d’un point spécifique dans le flux de modification. Dans la première partition, le bleu foncé représente les modifications non lues, tandis que le bleu clair représente les modifications déjà lues sur le flux de modification. Les hôtes utilisent la collection de baux pour stocker une valeur de « continuation » afin d’assurer le suivi de la position de lecture actuelle pour chaque consommateur. 

![Utilisation de l’hôte du processus de flux de modification Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Utilisation de la bibliothèque du processeur de flux de modification

Avant d’installer le package NuGet du processeur de flux de modification, vous devez d’abord installer : 

* Microsoft.Azure.DocumentDB, dernière version.
* Newtonsoft.Json, dernière version.

Installez ensuite le [package NuGet Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) et ajoutez-le en tant que référence.

Pour implémenter la bibliothèque du processeur de flux de modification, procédez comme suit :

1. Implémentez un objet **DocumentFeedObserver** qui implémente un objet **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Implémentez un objet **DocumentFeedObserverFactory** qui implémente un objet **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Définissez *CancellationTokenSource* et *ChangeFeedProcessorBuilder*.

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. Générez **ChangeFeedProcessorBuilder** après avoir défini les objets pertinents. 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
    ```

Et voilà ! Après ces quelques étapes, des documents commenceront à s’afficher au sein de la méthode **DocumentFeedObserver ProcessChangesAsync**.

Le code ci-dessus est fourni à des fins d’illustration pour afficher différents types d’objets et leur interaction. Vous devez définir les variables appropriées et les initier avec les valeurs correctes. Vous pouvez obtenir le code complet utilisé dans cet article à partir du [référentiel GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> Vous ne devez jamais avoir une clé principale dans votre code ni dans le fichier de configuration, comme indiqué dans le code ci-dessus. Consultez la page [Utiliser Key Vault pour récupérer des clés](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## <a name="faq"></a>Forum Aux Questions

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Quelles sont les différentes méthodes de lecture du flux de modification ? et quand utiliser chaque méthode ?

Vous pouvez lire les flux de modification de trois façons différentes :

* **[Utilisation du kit de développement logiciel (SDK) .NET de l’API SQL Azure Cosmos DB](#sql-sdk)**
   
   Cette méthode de lecture vous offre un niveau inférieur de contrôle sur le flux de modification. Vous pouvez gérer le point de contrôle, accéder à une clé de partition spécifique, etc. Si vous disposez de plusieurs lecteurs, vous pouvez utiliser [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) pour répartir la charge de lecture sur plusieurs threads ou clients. .

* **[Utilisation de la bibliothèque du processeur de flux de modification Azure Cosmos DB](#change-feed-processor)**

   Si vous souhaitez externaliser les opérations complexes associées au flux de modification, vous pouvez utiliser la bibliothèque du processeur de flux de modification. Cette bibliothèque réduit la complexité de manière considérable, tout en vous laissant un contrôle total sur le flux de modification. Cette bibliothèque suit un [modèle Observateur](https://en.wikipedia.org/wiki/Observer_pattern) et votre fonction de traitement est appelée par le kit de développement logiciel (SDK). 

   Si vous disposez d’un flux de modification à débit élevé, vous pouvez instancier plusieurs clients pour lire le flux de modification. Étant donné que vous utilisez « la bibliothèque du processeur de flux de modification », celle-ci répartit automatiquement la charge entre les différents clients. Vous n’avez rien à faire. Toute la complexité est gérée par le kit de développement logiciel. Toutefois, si vous souhaitez avoir votre propre équilibreur de charge, vous pouvez implémenter IParitionLoadBalancingStrategy pour mettre en place une stratégie de partition personnalisée. Implémentez IPartitionProcessor pour traiter les modifications de manière personnalisée sur une partition. Si vous pouvez traiter une plage de partition avec le kit de développement logiciel, vous devez néanmoins utiliser le kit de développement logiciel de l’API SQL pour traiter une clé de partition spécifique.

* **[Utilisation d’Azure Functions](#azure-functions)** 
   
   Nous vous recommandons d’utiliser cette dernière option, qui est la plus simple. Lorsque vous créez un déclencheur Azure Cosmos DB dans une application Azure Functions, vous sélectionnez la collection Azure Cosmos DB à laquelle vous souhaitez vous connecter, et la fonction se déclenche chaque fois qu’une modification est apportée à la collection sélectionnée. voir une [capture vidéo](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) de l’utilisation d’Azure Functions et du flux de modification

   Les déclencheurs peuvent être créés via le portail Azure Functions, via le portail Azure Cosmos DB ou par programmation. Visual Studio et Visual Studio Code offrent une prise en charge étendue de l’écriture pour les fonctions Azure. Vous pouvez écrire et déboguer le code sur votre bureau, puis déployer la fonction avec un seul clic. Pour en savoir plus, consultez l’article [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](serverless-computing-database.md).

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Quel est l’ordre de tri des documents dans le flux de modification ?

Les documents s’affichent dans le flux de modification dans l’ordre où ils ont été modifiés. Cet ordre de tri est uniquement garanti par partition.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Pour un compte multirégion, que se passe-t-il au niveau du flux de modification en cas de basculement de la région d’écriture ? Le flux de modification bascule-t-il également ? Le flux de modification continuera-t-il d’afficher des enregistrements contigus ou sera-t-il réinitialisé au moment du basculement ?

Le flux de modification continuera de s’exécuter de manière contiguë tout au long de l’opération de basculement manuel.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Combien de temps le flux de modification conservera-t-il les données modifiées si je définis la propriété Durée de vie du document sur -1 ?

Le flux de modification conservera les données indéfiniment. Tant que les données ne sont pas supprimées, elles restent dans le flux de modification.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Comment puis-je configurer Azure Functions pour lire à partir d’une région particulière, étant donné que le flux de modification est disponible dans toutes les régions de lecture par défaut ?

Pour le moment, il n’est pas possible de configurer Azure Functions pour lire à partir d’une région spécifique. Un problème GitHub au niveau du référentiel Azure Functions empêche la configuration de régions préférées pour les systèmes de déclencheurs et de liaisons Azure Cosmos DB.

Azure Functions utilise la stratégie de connexion par défaut. Vous pouvez configurer le mode de connexion dans Azure Functions. Par défaut, la lecture s’effectue à partir de la région d’écriture, et il est donc préférable de placer Azure Functions dans la même région.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Quelle est la taille par défaut des lots dans Azure Functions ?

100 documents à chaque appel d’Azure Functions. Vous pouvez toutefois modifier ce nombre dans le fichier function.json. Voici la [liste complète des options de configuration](../azure-functions/functions-run-local.md). Si vous effectuez un développement local, mettez à jour les paramètres d’application dans le fichier [local.settings.json](../azure-functions/functions-run-local.md).

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Je suis en train d’analyser une collection et de lire son flux de modification, mais certains documents ne s’affichent pas. Comment cela se fait-il ?

Assurez-vous qu’aucune autre fonction ne lit actuellement la même collection avec la même collection de baux. Cela m’est arrivé et j’ai découvert plus tard que les documents manquants étaient traités par d’autres fonctions Azure, qui utilisaient le même bail.

Par conséquent, si vous créez plusieurs fonctions Azure pour lire le même flux de modification, celles-ci doivent utiliser des collections de baux différentes ou la configuration « leasePrefix » pour partager la même collection. Toutefois, lorsque vous utilisez la bibliothèque du processeur de flux de modification, vous pouvez démarrer plusieurs instances de votre fonction et le kit de développement logiciel (SDK) répartit automatiquement les documents dans les différentes instances pour vous.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Le document est mis à jour chaque seconde, et je ne reçois pas toutes les modifications dans l’instance Azure Functions qui écoute le flux de modification.

Azure Functions interroge le flux de modification toutes les 5 secondes. Toute modification intervenant pendant ce laps de temps est donc perdue. Azure Cosmos DB enregistre une seule version toutes les 5 secondes. Vous obtiendrez donc la cinquième modification du document. Toutefois, si vous souhaitez réduire l’intervalle de 5 secondes et interroger le flux de modification toutes les secondes, vous pouvez configurer l’intervalle d’interrogation « feedPollTime » (voir [Liaisons Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration)). Cet intervalle est défini en millisecondes et configuré sur 5000 par défaut. Il est possible, mais déconseillé, de définir un intervalle inférieur à 1 seconde, car vous utiliserez le processeur de manière plus intensive.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>J’ai ajouté un document à la collection de l’API Mongo, mais lorsque j’obtiens le document dans le flux de modification, il affiche une valeur d’ID différente. D’où vient le problème ?

Votre collection est une collection d’API Mongo. Rappelez-vous que le flux de modification est lu à l’aide du client SQL et qu’il sérialise les éléments au format JSON. En raison du formatage JSON, les clients MongoDB seront confrontés à une incompatibilité entre les documents au format BSON et le flux de modification au format JSON. Ce que vous voyez est la représentation d’un document BSON au format JSON. Si vous utilisez des attributs binaires dans un compte Mongo, ceux-ci sont convertis au format JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Existe-t-il un moyen de configurer le flux de modification pour qu’il affiche uniquement les mises à jour et non les ajouts ?

Non, cela n’est pas possible pour le moment, mais cette fonctionnalité figure sur la feuille de route. Aujourd’hui, vous pouvez ajouter un marqueur logiciel sur le document pour les mises à jour.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Existe-t-il un moyen d’afficher les suppressions dans le flux de modification ?

Pour le moment, le flux de modification ne consigne pas les suppressions. Le flux de modification est amélioré en permanence et cette fonctionnalité figure sur la feuille de route. Aujourd’hui, vous pouvez ajouter un marqueur logiciel sur le document pour les suppressions. Ajoutez un attribut sur le document appelé « deleted » et définissez-le sur la valeur « true ». Puis, définissez la durée de vie du document afin qu’il puisse être supprimé automatiquement.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Puis-je lire le flux de modification pour des documents anciens (par exemple, des documents ajoutés il y a 5 ans) ?

Oui, si le document n’a pas été supprimé, vous pouvez lire le flux de modification correspondant en remontant jusqu’à la date de création de votre collection.

### <a name="can-i-read-change-feed-using-javascript"></a>Puis-je lire un flux de modification à l’aide de JavaScript ?

Oui, le kit de développement logiciel (SDK) Node.js prend en charge depuis peu les flux de modification. Il peut être utilisé tel qu’indiqué dans l’exemple suivant. Veillez à installer la dernière version du module documentdb avant d’exécuter le code :

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Puis-je lire un flux de modification à l’aide de Java ?

La bibliothèque Java pour lire le flux de modification est disponible dans le [référentiel Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). La bibliothèque Java a quelques versions de retard sur la bibliothèque .NET, mais les bibliothèques seront bientôt synchronisées.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Puis-je utiliser les données _etag, _lsn ou _ts de comptabilité interne que j’obtiens en réponse ?

Le format _etag est un format interne. Vous ne devez pas vous y fier (ni l’analyser), car il peut changer à tout moment.
Le format _ts représente l’horodatage de création ou de modification. Vous pouvez utiliser les données _ts à des fins de comparaison chronologique.
Le format _lsn est un ID de lot qui est ajouté uniquement pour les flux de modification. Il représente l’ID de transaction au niveau du magasin. De nombreux documents peuvent avoir la même valeur _lsn.
Notez enfin que la valeur ETag dans FeedResponse est différente de la valeur _etag que vous voyez sur le document. La valeur _etag est un identificateur interne utilisé pour l’accès concurrentiel et qui indique la version du document. La valeur ETag est utilisée pour le séquencement du flux.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>La lecture du flux de modification entraîne-t-elle des frais supplémentaires ?

Vous êtes facturé pour les unités de requête consommées. Par exemple, des unités de requête sont consommées lorsque vous déplacez des données vers et à partir des collections Azure Cosmos DB. La facturation tient compte de toutes les unités de requête consommées par la collection de baux.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Plusieurs instances d’Azure Functions peuvent-elles lire le flux de modification d’une même collection ?

Oui. Plusieurs instances d’Azure Functions peuvent lire le flux de modification d’une même collection. Toutefois, les instances d’Azure Functions doivent avoir une valeur leaseCollectionPrefix différente.

### <a name="should-the-lease-collection-be-partitioned"></a>La collection de baux doit-elle être partitionnée ?

Non, la collection de baux peut être fixe. Les collections de baux partitionnées ne sont pas requises, ni prises en charge actuellement.

### <a name="can-i-read-change-feed-from-spark"></a>Puis-je lire un flux de modification à l’aide de Spark ?

Oui, vous pouvez. Consultez l’article [Connecteur Spark Azure Cosmos DB](spark-connector.md). Cette [capture vidéo](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) explique comment traiter les flux de modification en tant que flux structurés.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Je traite un flux de modification à l’aide d’Azure Functions pour un lot de 10 documents et j’obtiens une erreur au niveau du 7e document. Dans ce cas, les trois derniers documents ne sont pas traités. Comment puis-je démarrer le traitement à partir du document à l’origine de l’erreur (c’est-à-dire le 7e document) dans le prochain flux ?

Pour résoudre cette erreur, nous vous recommandons d’encapsuler votre code avec un bloc try-catch. Interceptez l’erreur et placez ce document dans une file d’attente (lettres mortes), puis définissez une logique pour traiter les documents qui ont généré l’erreur. Si vous avez un lot de 200 documents et qu’un seul document génère une erreur, cette méthode vous évite d’annuler le lot complet.

En cas d’erreur, vous n’avez pas besoin de rembobiner le point de vérification jusqu’au début. Les documents continueront à s’afficher dans le flux de modification. N’oubliez pas que le flux de modification conserve le dernier instantané final de chaque document. Vous risquez donc de perdre le précédent instantané du document. Le flux de modification conserve uniquement la dernière version du document et peut, entre autres, revenir au document pour le modifier.

À mesure que vous modifiez votre code, les documents disparaîtront les uns après les autres de la file d’attente de lettres mortes.
Azure Functions est appelé automatiquement par le système de flux de modification et le point de vérification est géré en interne par la fonction Azure. Si vous souhaitez restaurer le point de vérification et en contrôler chaque aspect, envisagez d’utiliser le kit de développement logiciel (SDK) du processeur de flux de modification.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure Cosmos DB avec Azure Functions, consultez l’article [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](serverless-computing-database.md).

Pour plus d’informations sur l’utilisation de la bibliothèque du processeur de flux de modification, utilisez les ressources suivantes :

* [Page d’informations](sql-api-sdk-dotnet-changefeed.md) 
* [Package NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exemple de code illustrant les étapes 1 à 6 ci-dessus](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Exemples supplémentaires sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Pour plus d’informations sur l’utilisation du flux de modification avec le Kit de développement logiciel (SDK), utilisez les ressources suivantes :

* [Page d’informations sur le Kit de développement logiciel (SDK)](sql-api-sdk-dotnet.md)
