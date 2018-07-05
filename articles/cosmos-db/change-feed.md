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
ms.openlocfilehash: 8475c79782730e989f9590566c31ccd50af9f144
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302044"
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

Dans la vidéo suivante, le responsable du programme Azure Cosmos DB, Andrew Liu, montre comment fonctionne le flux de modification Azure Cosmos DB.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

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
> Au lieu de `ChangeFeedOptions.PartitionKeyRangeId`, vous pouvez utiliser `ChangeFeedOptions.PartitionKey` pour spécifier une clé de partition unique pour laquelle obtenir un flux de modification. Par exemple : `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
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

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

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
