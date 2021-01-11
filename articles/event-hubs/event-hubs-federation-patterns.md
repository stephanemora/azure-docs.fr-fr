---
title: Modèles de tâches de réplication des événements – Azure Event Hubs | Microsoft Docs
description: Cet article fournit des instructions détaillées concernant l’implémentation de modèles de tâches de réplication d’événements spécifiques.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 7702b1987faabfce8d97e7b5c9b18766df72caad
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803984"
---
# <a name="event-replication-tasks-patterns"></a>Modèles de tâches de réplication des événements

La [vue d’ensemble de la fédération](event-hubs-federation-overview.md) et la [vue d’ensemble des fonctions de réplication](event-hubs-federation-replicator-functions.md) expliquent les éléments de base des tâches de réplication et la logique sous-jacente. Nous vous recommandons de vous familiariser avec ces contenus avant de poursuivre la lecture de cet article.

Dans cet article, nous détaillons les recommandations d’implémentation pour plusieurs des modèles mis en évidence dans la section Vue d’ensemble.

## <a name="replication"></a>Réplication

Le modèle de réplication copie les événements d’un Event Hub vers le suivant, ou d’un Event Hub vers une autre destination, telle qu’une file d’attente Service Bus. Les événements sont transférés sans apporter de modifications à la charge utile d’événement.

L’implémentation de ce modèle est couverte par les exemples de [réplication d’événements entre Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) et de [réplication d’événements entre Event Hubs et Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus).

### <a name="streams-and-order-preservation"></a>Flux et conservation de l’ordre

La réplication, via Azure Functions ou Azure Stream Analytics, n’a pas pour objectif de garantir la création de clones exacts (1:1) d’un Event Hub source dans un Event Hub cible, mais se concentre sur la conservation de l’ordre relatif des événements là où l’application l’exige. L’application communique cela en regroupant les événements connexes avec la même clé de partition et [Event Hubs organise les messages dotés de la même clé de partition de manière séquentielle dans la même partition](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> Les informations de « décalage » sont uniques pour chaque Event Hub et les décalages pour les mêmes événements diffèrent entre différentes instances Event Hub. Pour localiser une position dans un flux d’événements copié, utilisez des décalages basés sur la durée et reportez-vous aux [métadonnées propagées attribuées par le service](#service-assigned-metadata).
>
> Les décalages basés sur la durée démarrent votre récepteur à un moment précis dans le temps :
> - *EventPosition.FromStart()* – Relit toutes les données conservées.
> - *EventPosition.FromEnd()* – Lit toutes les nouvelles données à partir de l’heure de la connexion.
> - *EventPosition.FromEnqueuedTime(dateTime)* – Toutes les données à partir d’une date et d’une heure spécifiques.
>
> Dans le processeur d’événements (EventProcessor), vous définissez la position via InitialOffsetProvider sur EventProcessorOptions. Avec les autres API du récepteur, la position est transmise par le constructeur. 


Les applications auxiliaires de fonction de réplication prédéfinies [fournies en tant qu’exemples](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) qui sont utilisées dans les conseils basés sur Azure Functions garantissent que les flux d’événements ayant la même clé de partition récupérée à partir d’une partition source sont envoyés dans le Event Hub cible sous la forme d’un lot dans le flux d’origine et avec la même clé de partition.

Si le nombre de partitions des Event Hubs source et cible est identique, tous les flux de la cible sont mappés aux mêmes partitions que dans la source.
Si le nombre de partitions est différent, ce qui revêt une importance dans certains des autres modèles décrits dans ce qui suit, le mappage est différent, mais les flux sont toujours conservés ensemble et dans l’ordre.

L’ordre relatif d’événements appartenant à différents flux ou d’événements indépendants sans clé de partition dans une partition cible peut toujours différer de la partition source.

### <a name="service-assigned-metadata"></a>Métadonnées attribuées par le service

Les métadonnées attribuées par le service d’un événement obtenu à partir du Event Hub source, le temps de mise en file d’attente d’origine, le numéro de séquence et le décalage sont remplacés par les nouvelles valeurs attribuées par le service dans le Event Hub cible, mais avec les [fonctions d’application auxiliaire](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), les tâches de réplication fournies dans nos exemples, les valeurs d’origine sont conservées dans les propriétés de l’utilisateur : `repl-enqueue-time` (chaîne ISO8601), `repl-sequence`, `repl-offset`.

Ces propriétés sont de type chaîne et contiennent la valeur convertie en chaîne des propriétés d’origine respectives. Si l’événement est transféré plusieurs fois, les métadonnées attribuées par le service de la source immédiate sont ajoutées aux propriétés déjà existantes, avec des valeurs séparées par des points-virgules.

### <a name="failover"></a>Basculement

Si vous utilisez la réplication à des fins de récupération d’urgence, pour vous protéger contre les événements de disponibilité régionaux dans le service Event Hubs, ou contre les interruptions de réseau, tout scénario d’échec peut nécessiter un basculement d’un Event Hub à l’autre, indiquant aux producteurs et/ou aux consommateurs d’utiliser le point de terminaison secondaire.

Pour tous les scénarios de basculement, l’hypothèse est que les éléments requis des espaces de noms sont structurellement identiques, ce qui signifie que les Event Hubs et les groupes de consommateurs sont nommés de manière identique et que les règles de signature d’accès partagé et/ou les règles de contrôle d’accès en fonction du rôle sont configurées de la même façon. Vous pouvez créer (et mettre à jour) un espace de noms secondaire en suivant les [instructions pour déplacer des espaces de noms](move-across-regions.md) et en omettant l’étape de nettoyage.

Pour forcer les producteurs et les consommateurs à effectuer le basculement, vous devez apporter les informations sur l’espace de noms à utiliser disponible pour recherche dans un emplacement facile à atteindre et à mettre à jour. Si des producteurs ou des consommateurs rencontrent des erreurs fréquentes ou persistantes, ils doivent consulter cet emplacement et ajuster leur configuration. Il existe de nombreuses façons de partager cette configuration, mais nous en évoquons deux ci-dessous : DNS et partages de fichiers.

#### <a name="dns-based-failover-configuration"></a>Configuration de basculement basée sur DNS

L’une des approches possibles consiste à stocker les informations dans des enregistrements SRV DNS dans un DNS que vous contrôlez et faites pointer vers les points de terminaison Event Hub respectifs. 

> [!IMPORTANT] 
> Souvenez-vous qu’Event Hubs ne permet pas à ses points de terminaison d’être directement associés à des enregistrements CNAME, ce qui signifie que vous allez utiliser le DNS comme mécanisme de recherche résilient pour les adresses de point de terminaison et non pour résoudre directement les informations d’adresse IP.

Supposons que vous êtes propriétaire du domaine `example.com` et, pour votre application, d’une zone `test.example.com`. Pour deux autres Event Hubs, vous allez maintenant créer deux autres zones imbriquées et un enregistrement SRV dans chacune d’elles.

Les enregistrements SRV sont, selon la convention commune, dotés du préfixe `_azure_eventhubs._amqp` et conservent deux enregistrements de point de terminaison : Un pour AMQP-sur-TLS sur le port 5671 et un pour AMQP-sur-WebSocket sur le port 443, les deux pointant vers le point de terminaison Event Hubs de l’espace de noms correspondant à la zone.

| Zone                   | Enregistrement SRV                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

Dans la zone de votre application, vous allez ensuite créer une entrée CNAME qui pointe vers la zone subordonnée correspondant à votre Event Hub principal :

| Enregistrement CNAME                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

À l’aide d’un client DNS qui autorise l’interrogation explicite des enregistrements CNAME et SRV (les clients intégrés de Java et .NET autorisent uniquement la résolution simple des noms en adresses IP), vous pouvez alors résoudre le point de terminaison souhaité. Avec [DnsClient.NET](https://dnsclient.michaco.net/), par exemple, la fonction de recherche est la suivante :

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

La fonction retourne le nom d’hôte cible inscrit pour le port 5671 de la zone actuellement associée à l’alias CNAME, comme indiqué ci-dessus.

Pour effectuer un basculement, vous devez modifier l’enregistrement CNAME et le faire pointer vers l’autre zone.

L’avantage de l’utilisation d’un DNS, et en particulier d’[Azure DNS](../dns/dns-overview.md), est que les informations Azure DNS sont répliquées à l’échelle mondiale et, par conséquent, résilientes contre les interruptions dans une même région.

Cette procédure est similaire à la façon dont la [géorécupération d’urgence Azure Event Hubs](event-hubs-geo-dr.md) fonctionne, mais elle est entièrement sous votre propre contrôle et fonctionne également avec les scénarios de type « actif/passif ».

#### <a name="file-share-based-failover-configuration"></a>Configuration de basculement basée sur un partage de fichiers

L’alternative la plus simple à l’utilisation du DNS pour le partage des informations de point de terminaison consiste à placer le nom du point de terminaison principal dans un fichier de texte brut et à utiliser le fichier à partir d’une infrastructure résiliente en cas de pannes tout en autorisant les mises à jour.

Si vous avez déjà exécuté une infrastructure de site web à haut niveau de disponibilité avec une disponibilité mondiale et la réplication de contenu, ajoutez-y un tel fichier et republiez-le si un changement est nécessaire.

> [!CAUTION]
> Vous devez uniquement publier le nom du point de terminaison de cette manière, et non une chaîne de connexion complète incluant des secrets.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Considérations supplémentaires relatives au basculement des consommateurs

Pour les consommateurs du Event Hub, les considérations supplémentaires relatives à la stratégie de basculement dépendent des besoins du processeur d’événements.

En cas de sinistre nécessitant la reconstruction d’un système, notamment des bases de données, à partir des données de sauvegarde, et si les bases de données sont remplies directement ou via un traitement intermédiaire à partir des événements détenus dans le Event Hub, vous allez restaurer la sauvegarde et démarrer la relecture des événements dans le système à partir du moment où la sauvegarde de la base de données a été créée et non à partir du moment où le système d’origine a été détruit.

Si une défaillance concerne uniquement un segment d’un système ou un seul Event Hub, qui est devenu inaccessible, vous voudrez probablement continuer à traiter les événements à partir de la position où le traitement a été interrompu.

Pour réaliser l’un ou l’autre scénario et utiliser le processeur d’événements de votre SDK Azure respectif, [vous allez créer un nouveau magasin de points de contrôle](event-processor-balance-partition-load.md#checkpointing) et fournir une position de partition initiale, en fonction de l’_horodateur_ à partir duquel vous souhaitez reprendre le traitement.

Si vous avez toujours accès au magasin de points de contrôle du Event Hub que vous quittez, les [métadonnées propagées](#service-assigned-metadata) abordées ci-dessus vous aideront à ignorer les événements qui ont déjà été traités et à reprendre précisément là où vous vous êtes arrêté.

## <a name="merge"></a>Fusionner

Le modèle de fusion a une ou plusieurs tâches de réplication pointant vers une cible, éventuellement en même temps que les producteurs normaux envoient des événements à la même cible.

Les variantes de ces modèles sont les suivantes :

- Au moins deux fonctions de réplication acquièrent simultanément des événements à partir de sources distinctes et les envoient à la même cible.
- Une autre fonction de réplication acquérant des événements à partir d’une source, tandis que la cible est également utilisée directement par les producteurs.
- Le modèle précédent, mais mis en miroir entre deux ou plusieurs Event Hubs (contenant donc les mêmes flux), indépendamment de l’endroit où les événements sont produits.

Les deux premières variations de modèle sont insignifiantes et ne diffèrent pas des simples tâches de réplication.

Le dernier scénario nécessite l’exclusion de la réplication des événements déjà répliqués. La technique est illustrée et expliquée dans l’exemple [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge).

## <a name="editor"></a>Éditeur

Le modèle d’éditeur s’appuie sur le modèle de [réplication](#replication), mais les messages sont modifiés avant d’être transférés. 

Voici quelques exemples de ces modifications :

- **_Transcodage_**  : si le contenu de l’événement (également appelé « corps » ou « charge utile ») arrive de la source encodée à l’aide du format _Apache Avro_ ou d’un format de sérialisation propriétaire, mais que le système propriétaire de la cible s’attend à ce que le contenu soit _encodé en JSON_, une tâche de réplication de transcodage commence par désérialiser la charge utile de _Apache Avro_ dans un graphe d’objets en mémoire, puis sérialise ce graphique au format _JSON_ pour l’événement qui est transféré. Le transcodage inclut également **des tâches de compression et de décompression du contenu**.
- **_Transformation_**  : les événements qui contiennent des données structurées peuvent nécessiter la remodelage de ces données pour faciliter la consommation par les consommateurs en aval. Cela peut impliquer des tâches telles que l’aplatissement des structures imbriquées, le nettoyage des éléments de données superflus ou la mise en forme de la charge utile pour correspondre exactement à un schéma donné.
- **_Traitement par lots_**  : les événements peuvent être reçus par lots (plusieurs événements en un seul transfert) à partir d’une source, mais doivent être transférés individuellement vers une cible, ou vice versa. Une tâche peut donc transférer plusieurs événements basés sur un seul transfert d’événements d’entrée ou agréger un ensemble d’événements qui sont ensuite transférés ensemble.
- **_Validation_**  : les données d’événements provenant de sources externes doivent souvent être vérifiées pour déterminer si elles sont conformes à un ensemble de règles avant qu’elles ne soient transférées. Les règles peuvent être exprimées à l’aide de schémas ou de code. Les événements qui ne sont pas conformes peuvent être supprimés, en notant le problème dans les journaux, ou peuvent être transférés à une destination cible spéciale pour les gérer plus précisément.
- **_Enrichissement_**  : les données d’événements provenant de certaines sources peuvent nécessiter l’enrichissement avec un contexte plus poussé pour être utilisables dans les systèmes cibles. Cela peut impliquer la recherche de données de référence et l’incorporation de ces données avec l’événement, ou l’ajout d’informations sur la source connues de la tâche de réplication, mais non contenues dans les événements.
- **_Filtrage_**  : il se peut que certains événements arrivant d’une source doivent être retirés de la cible en fonction d’une règle. Un filtre teste l’événement par rapport à une règle et supprime l’événement si ce dernier ne correspond pas à la règle. Le filtrage des événements en double en observant certains critères et en supprimant les événements suivants avec les mêmes valeurs est une forme de filtrage.
- **_Chiffrement_**  : une tâche de réplication peut devoir déchiffrer le contenu arrivant de la source et/ou chiffrer le contenu transféré vers une cible, et/ou elle peut être amenée à vérifier l’intégrité du contenu et des métadonnées par rapport à une signature associée à l’événement, ou à attacher une telle signature.
- **_Attestation_**  : une tâche de réplication peut attacher des métadonnées, potentiellement protégées par une signature numérique, à un événement qui atteste que l’événement a été reçu via un canal spécifique ou à un moment donné.
- **_Chaînage_**  : une tâche de réplication peut appliquer des signatures à des flux d’événements afin que l’intégrité du flux soit protégée et que des événements manquants puissent être détectés.

Les modèles de transformation, de traitement par lots et d’enrichissement sont généralement mieux implémentés avec les travaux [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). 

Tous ces modèles peuvent être implémentés à l’aide d’Azure Functions, à l’aide du [déclencheur Event Hubs](../azure-functions/functions-bindings-event-hubs-trigger.md) pour l’acquisition d’événements et de la [liaison de sortie Event Hub](../azure-functions/functions-bindings-event-hubs-output.md) pour leur livraison.

## <a name="routing"></a>Routage

Le modèle de routage s’appuie sur le modèle de [réplication](#replication), mais au lieu d’avoir une source et une cible, la tâche de réplication a plusieurs cibles, illustrées ici en C# :

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

La fonction de routage prend en compte les métadonnées de message et/ou la charge utile de message, puis choisit l’une des destinations disponibles pour l’envoi.

Dans Azure Stream Analytics, vous pouvez obtenir le même résultat avec la définition de plusieurs sorties, puis l’exécution d’une requête par sortie.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Projection de journal

Le modèle de projection de journal aplatit le flux d’événements dans une base de données indexée, les événements devenant des enregistrements dans la base de données. En règle générale, les événements sont ajoutés à la même collection ou table, et la clé de partition du Event Hub devient la partie de la clé primaire qui cherche à rendre l’enregistrement unique.

La projection de journal peut produire un spécialiste d’historique de série chronologique de vos données d’événement ou une vue compactée, dans laquelle seul l’événement le plus récent est conservé pour chaque clé de partition. La forme de la base de données cible est votre choix et dépend des besoins de votre application. Ce modèle est également appelé « sourcing d’événements ».

> [!TIP]
> Vous pouvez facilement créer des projections de journal dans [Azure SQL Database](../stream-analytics/sql-database-output.md) et [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ou dans Azure Stream Analytics (cette dernière option étant préférable).

La fonction Azure suivante projette le contenu d’un Event Hub compacté dans une collection Azure CosmosDB.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Applications de réplication d’événements dans Azure Functions][1]
- [Réplication d’événements entre Event Hubs][2]
- [Réplication d’événements sur Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
