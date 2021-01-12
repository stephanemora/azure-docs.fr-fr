---
title: Modèles de tâches de réplication des messages - Azure Service Bus | Microsoft Docs
description: Cet article fournit des instructions détaillées concernant l’implémentation de modèles de tâches de réplication des messages spécifiques.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663423"
---
# <a name="message-replication-tasks-patterns"></a>Modèles de tâches de réplication des messages

La [vue d’ensemble de la fédération](service-bus-federation-overview.md) et la [vue d’ensemble des fonctions de réplication](service-bus-federation-replicator-functions.md) expliquent les éléments de base des tâches de réplication et la logique sous-jacente. Nous vous recommandons de vous familiariser avec ces contenus avant de poursuivre la lecture de cet article.

Dans cet article, nous détaillons les recommandations d’implémentation pour plusieurs des modèles mis en évidence dans la section Vue d’ensemble. 

## <a name="replication"></a>Réplication 

Le modèle de réplication copie les messages d’une file d’attente ou d’une rubrique vers la suivante, ou d’une file d’attente ou d’une rubrique vers une autre destination comme un hub d’événements. Les messages sont transférés sans apporter aucune modification à la charge utile de message. 

L’implémentation de ce modèle est couverte par l’exemple de [réplication des messages vers et à partir d’Azure Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy).

### <a name="sequences-and-order-preservation"></a>Séquences et conservation de l’ordre

Le modèle de réplication n’a pas pour objectif de conserver l’ordre absolu des messages d’une file d’attente ou d’une rubrique source dans une file d’attente ou une rubrique cible, mais il fait en sorte, à chaque fois que cela est nécessaire, de préserver l’ordre relatif des messages là où l’application l’exige. L’application active ceci en activant la prise en charge de session pour l’entité source et en regroupant les messages associés avec la même [clé de session](message-sessions.md).

Les fonctions de réplication prédéfinies prenant en charge les sessions garantissent que les séquences de messages avec le même ID de session extraites à partir d’une entité source sont envoyées dans la file d’attente ou la rubrique cible en tant que lot dans la séquence d’origine et avec le même ID de session. 

### <a name="service-assigned-metadata"></a>Métadonnées attribuées par le service 

Les métadonnées attribuées par le service d’un message obtenu à partir de la file d’attente ou rubrique source, l’heure de mise en file d’attente d’origine et le numéro de séquence sont remplacés par de nouvelles valeurs attribuées par le service dans la file d’attente ou rubrique cible, mais avec les tâches de réplication par défaut fournies dans nos exemples, les valeurs d’origine sont conservées dans les propriétés utilisateur : `repl-enqueue-time` (chaîne ISO8601) et `repl-sequence`.

Ces propriétés sont de type chaîne et contiennent la valeur convertie en chaîne des propriétés d’origine respectives.  Si le message est transféré plusieurs fois, les métadonnées attribuées par le service de la source immédiate sont ajoutées aux propriétés existantes, avec des valeurs séparées par des points-virgules. 

### <a name="failover"></a>Basculement

Si vous utilisez la réplication à des fins de reprise d’activité, pour vous protéger contre les messages de disponibilité régionale dans le service Service Bus, ou contre les interruptions de réseau, tout scénario d’échec peut nécessiter un basculement d’une rubrique ou file d’attente à l’autre, indiquant aux producteurs et/ou aux consommateurs qu’ils doivent utiliser le point de terminaison secondaire.

Pour tous les scénarios de basculement, l’hypothèse est que les éléments requis des espaces de noms sont structurellement identiques, ce qui signifie que les files d’attente et rubriques sont nommés de manière identique et que les règles de signature d’accès partagé et/ou les règles de contrôle d’accès en fonction du rôle sont configurées de la même façon. Vous pouvez créer (et mettre à jour) un espace de noms secondaire en suivant les [instructions pour déplacer des espaces de noms](move-across-regions.md) et en omettant l’étape de nettoyage.

Pour forcer les producteurs et les consommateurs à effectuer le basculement, vous devez apporter les informations sur l’espace de noms à utiliser disponible pour recherche dans un emplacement facile à atteindre et à mettre à jour. Si des producteurs ou des consommateurs rencontrent des erreurs fréquentes ou persistantes, ils doivent consulter cet emplacement et ajuster leur configuration. Il existe de nombreuses façons de partager cette configuration, mais nous en évoquons deux ci-dessous : DNS et partages de fichiers.

#### <a name="dns-based-failover-configuration"></a>Configuration de basculement basée sur DNS

L’une des approches possibles consiste à stocker les informations dans des enregistrements SRV DNS dans un système DNS que vous contrôlez et faites pointer vers les points de terminaison de file d’attente ou de rubrique respectifs. Souvenez-vous que Message Hubs ne permet pas à ses points de terminaison d’être directement associés à des enregistrements CNAME, ce qui signifie que vous utiliserez DNS comme mécanisme de recherche résilient pour les adresses de point de terminaison et non pour résoudre directement les informations d’adresse IP. 

Supposez que vous êtes propriétaire du domaine `example.com` et, pour votre application, d’une zone `test.example.com`. Pour deux autres Service Bus, vous allez maintenant créer deux autres zones imbriquées et un enregistrement SRV dans chacune d’elles. 

Les enregistrements SRV sont, selon la convention commune, dotés du préfixe `_azure_servicebus._amqp` et conservent deux enregistrements de point de terminaison : Un pour AMQP-sur-TLS sur le port 5671 et un pour AMQP-sur-WebSockets sur le port 443, les deux pointant vers le point de terminaison Service Bus de l’espace de noms correspondant à la zone.

| Zone                 | Enregistrement SRV
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

Dans la zone de votre application, vous allez ensuite créer une entrée CNAME qui pointe vers la zone subordonnée correspondant à votre file d’attente ou rubrique principale :

| Enregistrement CNAME                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

À l’aide d’un client DNS qui autorise l’interrogation explicite des enregistrements CNAME et SRV (les clients intégrés de Java et .NET autorisent uniquement la résolution simple des noms en adresses IP), vous pouvez alors résoudre le point de terminaison souhaité. Avec [DnsClient.NET](https://dnsclient.michaco.net/), par exemple, la fonction de recherche est la suivante :

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

La fonction retourne le nom d’hôte cible inscrit pour le port 5671 de la zone actuellement associée à l’alias CNAME, comme indiqué ci-dessus. 

Pour effectuer un basculement, vous devez modifier l’enregistrement CNAME et le faire pointer vers l’autre zone. 

L’avantage de l’utilisation de DNS, et en particulier d’[Azure DNS](../dns/dns-overview.md), est que les informations Azure DNS sont répliquées à l’échelle mondiale et, par conséquent, résilientes contre les interruptions dans une même région.

Cette procédure est similaire à la façon dont la [géoreprise d’activité Service Bus](service-bus-geo-dr.md) fonctionne, mais elle est entièrement sous votre propre contrôle et fonctionne également avec les scénarios de type « actif/passif ».

#### <a name="file-share-based-failover-configuration"></a>Configuration de basculement basée sur un partage de fichiers

L’alternative la plus simple à l’utilisation de DNS pour le partage des informations de point de terminaison consiste à placer le nom du point de terminaison principal dans un fichier de texte brut et à utiliser le fichier à partir d’une infrastructure résiliente en cas de panne tout en autorisant les mises à jour. 

Si vous exécutez déjà une infrastructure de site web à haut niveau de disponibilité avec une disponibilité mondiale et la réplication de contenu, ajoutez-y un tel fichier et republiez-le si un changement est nécessaire.

## <a name="merge"></a>Fusionner

Le modèle de fusion a une ou plusieurs tâches de réplication pointant vers une cible, éventuellement en même temps que les producteurs normaux envoient des messages à la même cible. 

Les variantes de ce modèle sont les suivantes :
- Au moins deux fonctions de réplication acquièrent simultanément des messages à partir de sources distinctes et les envoient à la même cible.
- Une autre fonction de réplication acquiert des messages à partir d’une source, tandis que la cible est également utilisée directement par les producteurs. 
- Le modèle précédent, mais avec les messages mis en miroir entre deux ou plusieurs rubriques, ce qui fait que ces rubriques contiennent les mêmes messages, quel que soit l’emplacement de production des messages.

Les deux premières variantes de modèle sont insignifiantes et ne diffèrent pas des simples tâches de réplication.

Le dernier scénario nécessite l’exclusion de la réplication des messages déjà répliqués. La technique est illustrée et expliquée dans l’exemple actif/actif.

## <a name="editor"></a>Éditeur

Le modèle d’éditeur s’appuie sur le modèle de [réplication](#replication), mais les messages sont modifiés avant d’être transférés. Voici quelques exemples de ces modifications :

- **_Transcodage_* _ : si le contenu du message (également appelé « corps » ou « charge utile ») arrive de la source encodée à l’aide du format _Apache Avro* ou d’un format de sérialisation propriétaire, mais que le système propriétaire de la cible s’attend à ce que le contenu soit encodé en *JSON*, une tâche de réplication de transcodage commence par désérialiser la charge utile d’*Apache Avro* dans un graphe d’objets en mémoire, puis sérialise ce graphique au format *JSON* pour le message qui est transféré. Le transcodage inclut également des tâches de **compression** et de décompression du contenu.
- **_Transformation_* _ : les messages qui contiennent des données structurées peuvent nécessiter le remodelage de ces données pour faciliter la consommation par les consommateurs en aval. Cela peut impliquer des tâches telles que l’aplatissement des structures imbriquées, le nettoyage des éléments de données superflus ou la mise en forme de la charge utile pour qu’elle corresponde exactement à un schéma donné.
- _*_Traitement par lots_*_  : les messages peuvent être reçus par lots (plusieurs messages en un seul transfert) à partir d’une source, mais doivent être transférés individuellement vers une cible, ou vice versa. Une tâche peut donc transférer plusieurs messages basés sur un seul transfert de message d’entrée, ou agréger un ensemble de messages qui sont ensuite transférés ensemble. 
- _*_Validation_*_  : les données de messages provenant de sources externes doivent souvent être vérifiées pour déterminer si elles sont conformes à un ensemble de règles avant d’être transférées. Les règles peuvent être exprimées à l’aide de schémas ou de code. Les messages qui ne sont pas conformes peuvent être supprimés, en notant le problème dans les journaux, ou peuvent être transférés à une destination cible spéciale en vue d’une gestion supplémentaire.   
- _*_Enrichissement_*_  : les données de message provenant de certaines sources peuvent nécessiter l’enrichissement avec un contexte plus poussé pour être utilisables dans les systèmes cibles. Cela peut impliquer la recherche de données de référence et l’incorporation de ces données avec le message, ou l’ajout d’informations sur la source connues de la tâche de réplication, mais non contenues dans les messages. 
- _*_Filtrage_*_  : il se peut qu’une règle impose que certains messages arrivant d’une source soient retirés de la cible. Un filtre teste le message par rapport à une règle, et le supprime s’il ne respecte pas la règle. Le filtrage des messages en double en observant certains critères et en supprimant les messages suivants avec les mêmes valeurs est une forme de filtrage.
- _*_Routage et partitionnement_*_  : certaines tâches de réplication peuvent autoriser plusieurs cibles alternatives, et définir des règles pour lesquelles la cible de réplication est choisie pour un message particulier en fonction des métadonnées ou du contenu du message. Le partitionnement est une forme spéciale de routage où la tâche affecte explicitement des partitions dans une cible de réplication conformément à des règles.
- _*_Chiffrement_*_  : une tâche de réplication peut devoir déchiffrer le contenu arrivant de la source et/ou chiffrer le contenu transféré vers une cible, et/ou elle peut être amenée à vérifier l’intégrité du contenu et des métadonnées par rapport à une signature associée au message, ou à attacher une telle signature. 
- _*_Attestation_*_  : une tâche de réplication peut attacher des métadonnées, potentiellement protégées par une signature numérique, à un message, attestant que celui-ci a été reçu par le biais d’un canal spécifique ou à un moment spécifique.     
- _ *_Chaînage_** : une tâche de réplication peut appliquer des signatures à une séquence de messages afin que l’intégrité de la séquence soit protégée et que les messages manquants puissent être détectés.

Tous ces modèles peuvent être implémentés à l’aide d’Azure Functions, à l’aide du [déclencheur Message Hubs](../azure-functions/functions-bindings-service-bus-trigger.md) pour l’acquisition de messages et de la [liaison de sortie de file d’attente ou de rubrique](../azure-functions/functions-bindings-service-bus-output.md) pour leur livraison. 

## <a name="routing"></a>Routage

Le modèle de routage s’appuie sur le modèle de [réplication](#replication), mais au lieu d’avoir une source et une cible, la tâche de réplication a plusieurs cibles, illustrées ici en C# :

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

La fonction de routage prend en compte les métadonnées de message et/ou la charge utile de message, puis choisit l’une des destinations disponibles pour l’envoi.

## <a name="next-steps"></a>Étapes suivantes

- [Applications de réplication de messages dans Azure Functions][1]
- [Réplication de messages entre Service Bus][2]
- [Réplication de messages vers Azure Event Hubs][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub