---
title: Qu’est-ce qu’Azure Relay ? | Microsoft Docs
description: Cet article fournit une vue d’ensemble du service Azure Relay, qui vous permet de développer des applications cloud qui utilisent des services locaux exécutés dans votre réseau d’entreprise sans ouvrir de connexion de pare-feu ni apporter de modifications intrusives à votre infrastructure réseau.
ms.topic: conceptual
ms.date: 09/02/2021
ms.custom: contperf-fy22q1
ms.openlocfilehash: af12067cd73b8767beb243e9e098d2cbbf9c456a
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081868"
---
# <a name="what-is-azure-relay"></a>Qu’est-ce qu’Azure Relay ?
Le service Azure Relay vous permet d’exposer en toute sécurité les services exécutés dans votre réseau d’entreprise sur le cloud public. Vous pouvez le faire sans ouvrir de port sur votre pare-feu ni apporter de changements intrusifs à votre infrastructure réseau d’entreprise. 

Le service de relais prend en charge les scénarios suivants entre les services locaux et les applications exécutées dans le cloud ou dans un autre environnement local. 

- Communication unidirectionnelle standard, de requête/réponse et d’homologue à homologue 
- Répartition des événements sur Internet pour activer des scénarios de publication/d’abonnement 
- Communication par socket bidirectionnelle et non mise en mémoire tampon au-delà des limites du réseau

Azure Relay est différent des technologies d’intégration au niveau du réseau telles que VPN. Un relais Azure peut être limité à un point de terminaison à une application sur une seule machine. La technologie VPN est beaucoup plus intrusive, car elle repose sur la modification de l’environnement réseau. 

## <a name="basic-flow"></a>Flux de base
Dans le modèle de transfert de données par relais, les étapes de base impliquées sont les suivantes :

1. Un service local se connecte au service de relais via un port sortant. 
2. Il crée un socket bidirectionnel pour la communication liée à une adresse spécifique. 
3. Le client peut ensuite communiquer avec le service local en envoyant le trafic vers le service de relais ciblant cette adresse. 
4. Le service de relais *relaie* ensuite les données au service local via le socket bidirectionnel dédié au client. Le client n’a pas besoin d’une connexion directe au service local. Il n’a pas besoin de connaître l’emplacement du service. De plus, le service local n’a pas besoin de ports entrants ouverts sur le pare-feu.


## <a name="features"></a>Fonctionnalités 
Azure Relay comprend deux fonctionnalités :

- [Connexions hybrides](#hybrid-connections) : utilise les sockets web standard ouverts permettant des scénarios multi-plateformes.
- [Relais WCF](#wcf-relay) : utilise Windows Communication Foundation (WCF) pour activer les appels de procédure à distance. Le relais WCF est l’offre de relais héritée que de nombreux clients utilisent déjà avec leurs modèles de programmation WCF.

## <a name="hybrid-connections"></a>les connexions hybrides

La fonctionnalité Connexions hybrides dans Azure Relay est une évolution sécurisée et à protocole ouvert des fonctionnalités Relay qui existaient précédemment. Vous pouvez l’utiliser sur n’importe quelle plateforme et dans n’importe quel langage. La fonctionnalité Connexions hybrides dans Azure Relay est basée sur les protocoles HTTP et WebSockets. Elle vous permet d’envoyer des requêtes et de recevoir des réponses sur des sockets web ou HTTP(S). Cette fonctionnalité est compatible avec l’API WebSocket dans les navigateurs web courants. 

Pour plus d’informations sur le protocole Connexions hybrides, consultez le [guide du protocole Connexions hybrides](relay-hybrid-connections-protocol.md). Vous pouvez utiliser Connexions hybrides avec une bibliothèque de sockets web pour n’importe quel runtime/langage.

> [!NOTE]
> Connexions hybrides d’Azure Relay remplace l’ancienne fonctionnalité Connexions hybrides de BizTalk Services. La fonctionnalité Connexions hybrides dans BizTalk Services repose sur WCF Relay d’Azure Service Bus. La fonctionnalité Connexions hybrides dans Azure Relay vient compléter la fonctionnalité de WCF Relay existante. Ces deux fonctionnalités du service (WCF Relay et Connexions hybrides) coexistent dans le service Azure Relay. Elles partagent une passerelle commune, mais ont des implémentations différentes.

Pour prendre en main les connexions hybrides dans Azure Relay, consultez les démarrages rapides suivants : 

- [Connexions hybrides - WebSockets .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Connexions hybrides - WebSockets de nœud](relay-hybrid-connections-node-get-started.md)
- [Connexions hybrides - HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
- [Connexions hybrides - HTTP de nœud](relay-hybrid-connections-http-requests-node-get-started.md)

Pour obtenir d’autres exemples, consultez [Azure Relay - Exemples de connexions hybrides sur GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections).

## <a name="wcf-relay"></a>Relais WCF
WCF Relay fonctionne avec l’ensemble de .NET Framework et pour WCF. Vous établissez une connexion entre votre service local et le service de relais à l’aide d’une suite de liaisons de « relais » WCF. Les liaisons de relais mappent à de nouveaux éléments de liaison de transport destinés à créer des composants de canal WCF qui s’intègrent à Service Bus dans le cloud.

Pour prendre en main WCF Relay, consultez les démarrages rapides suivants : 

- [Exposer un service WCF local sur une application web dans le cloud](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
- [Exposer un service WCF sur site à un client WCF à l’extérieur de votre réseau](service-bus-relay-tutorial.md)
- [Exposer un service REST WCF local sur un client WCF à l’extérieur de votre réseau](service-bus-relay-rest-tutorial.md)

Pour obtenir d’autres exemples, consultez [Azure Relay - Exemples WCF Relay sur GitHub](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay).

## <a name="hybrid-connections-vs-wcf-relay"></a>Connexions hybrides et Relais WCF
Connexions hybrides et WCF Relay permettent une connexion sécurisée aux actifs existants au sein d’un réseau d’entreprise. L’utilisation de l’un par rapport à l’autre dépend de vos besoins particuliers, comme décrit dans le tableau suivant :

|  | Relais WCF | les connexions hybrides |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.js** | |x |
| **Protocole Open basé sur des normes** | |x |
| **Modèles de programmation RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architecture : Traitement des requêtes de relais entrantes
Le diagramme suivant illustre comment les requêtes de relais entrantes sont gérées par le service Azure Relay :

![Traitement des requêtes WCF Relay entrantes](./media/relay-what-is-it/ic690645.png)

1. Le client qui écoute envoie une requête d’écoute au service Azure Relay. L’équilibreur de charge Azure achemine la requête à l’un des nœuds de passerelle. 
2. Le service Azure Relay crée un relais dans le magasin de passerelles. 
3. Le client expéditeur envoie une requête pour se connecter au service d’écoute. 
4. La passerelle qui reçoit la requête recherche le relais dans le magasin de passerelles. 
5. La passerelle transfère la requête de connexion à la passerelle appropriée mentionnée dans le magasin de passerelles. 
6. La passerelle envoie une requête au client d’écoute pour qu’il crée un canal temporaire au nœud de passerelle le plus proche du client d’envoi. 
7. Le client d’écoute crée un canal temporaire vers la passerelle la plus proche du client d’envoi. Maintenant que la connexion entre les clients est établie via une passerelle, ils peuvent échanger des messages entre eux. 
8. La passerelle transfère tous les messages du client d’écoute au client d’envoi. 
9. La passerelle transfère tous les messages du client d’envoi au client d’écoute.  

## <a name="next-steps"></a>Étapes suivantes
Suivez un ou plusieurs démarrages rapides parmi les suivants, ou consultez [Exemples Azure Relay sur GitHub](https://github.com/Azure/azure-relay/tree/master/samples).

- les connexions hybrides
    - [Connexions hybrides - WebSockets .NET](relay-hybrid-connections-dotnet-get-started.md)
    - [Connexions hybrides - WebSockets de nœud](relay-hybrid-connections-node-get-started.md)
    - [Connexions hybrides - HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
    - [Connexions hybrides - HTTP de nœud](relay-hybrid-connections-http-requests-node-get-started.md)
- Relais WCF
    - [Exposer un service WCF local sur une application web dans le cloud](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
    - [Exposer un service WCF sur site à un client WCF à l’extérieur de votre réseau](service-bus-relay-tutorial.md)
    - [Exposer un service REST WCF local sur un client WCF à l’extérieur de votre réseau](service-bus-relay-rest-tutorial.md)

Pour la liste des questions fréquemment posées ainsi que leurs réponses, consultez [FAQ Relay](relay-faq.yml).

