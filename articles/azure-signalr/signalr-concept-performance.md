---
title: Guide des performances pour le Service Azure SignalR
description: Vue d’ensemble des performances du Service Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502037"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guide des performances pour le Service Azure SignalR

Un des principaux avantages pour l’utilisation du Service Azure SignalR est la facilité à l’échelle des applications SignalR. Dans un scénario à grande échelle, les performances devient un facteur important. Dans ce guide, nous allons présenter les facteurs qui ont un impact sur les performances des applications SignalR et sous différents scénarios d’utilisation, quelles sont les performances classiques ? Au final, nous présenterons également l’environnement et les outils utilisés pour générer un rapport de performances.

## <a name="terms-definition"></a>Définition des termes du contrat

*ASRS*: Service Azure SignalR

*Trafic entrant*: le message entrant pour le Service Azure SignalR

*Sortant*: le message sortant à partir du Service Azure SignalR

*La bande passante*: taille totale de tous les messages dans 1 seconde

*Mode par défaut*: ASRS nécessite que le serveur d’application pour établir la connexion avec elle avant d’accepter les connexions clientes. Il est le mode de fonctionnement par défaut lorsqu’un ASRS a été créé.

*Mode sans serveur*: ASRS accepte uniquement les connexions clientes. Aucune connexion au serveur n’est autorisée.

## <a name="overview"></a>Présentation

ASRS définit sept niveaux Standard pour les capacités de performances différentes, et a l’intention de ce guide répondre aux questions suivantes :

-   Quelles sont les performances ASRS classique pour chaque niveau ?

-   ASRS correspond-elle à mes besoins de débit des messages, par exemple, envoi de 100 000 messages par seconde ?

-   Pour mon scénario spécifique, le niveau est approprié pour moi ? Ou, comment puis-je sélectionner le niveau approprié ?

-   Quel type de serveur d’applications (taille de machine virtuelle) est appropriée pour moi et combien d'entre eux doit déployer ?

Pour répondre à ces questions, ce guide des performances d’abord donne une explication sur les facteurs qui ont un impact sur les performances, puis illustre le nombre maximal de messages entrant et sortant pour chaque niveau de cas d’utilisation classiques : **echo**, **diffusion**, **envoi au groupe**, et **envoi-connexion** (conversation pair à pair).

Il est impossible pour ce document couvrir tous les scénarios (et différents cas d’usage, taille de message différent ou modèle d’envoi de messages etc..). Toutefois, il fournit des méthodes d’évaluation pour aider les utilisateurs à environ évaluer leurs besoins des messages entrants ou sortants, puis recherchez les niveaux appropriés en vérifiant la table de performances.

## <a name="performance-insight"></a>Analyse des performances

Cette section décrit les méthodes d’évaluation de performances, puis répertorie tous les facteurs qui ont un impact sur les performances. Au final, il fournit des méthodes pour aider à évaluer les exigences de performances.

### <a name="methodology"></a>Méthodologie

**Débit** et **latence** sont deux aspects typiques de la vérification des performances. Pour ASRS, autre niveau de référence (SKU) présente un débit autre stratégie de limitation. Ce document définit **le nombre maximal autorisé de débit (bande passante entrante et sortante)** comme le nombre maximal de réalisée débit lorsque 99 % des messages ont une latence inférieure à 1 seconde.

La latence est l’intervalle de temps à partir de la connexion envoyant le message à la réception du message de réponse d’ASRS. Commençons par jeter **echo** par exemple, chaque connexion client ajoute un horodatage dans le message. Hub du serveur de l’application envoie le message d’origine vers le client. Par conséquent, le délai de propagation est facilement calculé par chaque connexion client. L’horodatage est attaché pour tous les messages dans **diffusion**, **envoi au groupe**, et **envoi-connexion**.

Pour simuler des milliers de connexions de clients simultanés, plusieurs machines virtuelles sont créées dans un réseau privé virtuel dans Azure. Toutes ces machines virtuelles se connectent à la même instance ASRS.

Mode ASRS par défaut, les machines virtuelles du serveur d’applications sont également déployés dans le même réseau privé virtuel en tant que machines virtuelles du client.

Toutes les machines virtuelles du client et du serveur d’applications que des machines virtuelles sont déployées dans le même réseau de la même région afin d’éviter les temps de latence de région.

### <a name="performance-factors"></a>Facteurs de performances

En théorie, la capacité ASRS est limitée par les ressources de calcul : Processeur, mémoire et réseau. Par exemple, plus les connexions à ASRS, davantage de mémoire ASRS consommées. Pour le trafic des messages plus volumineux, par exemple, chaque message est supérieure à 2 048 octets, elle nécessite ASRS à dépenser plus de cycles processeur pour traiter également. Pendant ce temps, la bande passante réseau Azure impose également une limite pour le trafic maximal.

Le type de transport, [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [événement envoyé serveur](https://en.wikipedia.org/wiki/Server-sent_events), ou [interrogation longue](https://en.wikipedia.org/wiki/Push_technology), est un autre facteur affecte les performances. WebSocket est un protocole de communication bidirectionnelle et duplex intégral sur une connexion TCP unique. Toutefois, événement serveur envoyé doit unidirectionnelle protocole message push du serveur au client. Interrogation longue demande que les clients interrogent régulièrement les informations du serveur via une requête HTTP. Pour la même API sous la même condition, WebSocket offre les meilleures performances, événement serveur envoyé est plus lent et interrogation longue est la plus lente. ASRS recommande WebSocket par défaut.

En outre, le coût de routage de message limite également les performances. ASRS joue un rôle en tant que routeur de messages, qui achemine le message à partir d’un ensemble de clients ou serveurs pour d’autres clients ou serveurs. Scénario différent ou l’API nécessite une stratégie de routage différente. Pour **echo**, le client envoie un message à elle-même, et la destination de routage est également lui-même. Ce modèle est le coût de routage le plus bas. Mais pour **diffusion**, **envoi au groupe**, **envoi-connexion**, ASRS a besoin de rechercher les connexions cibles via la structure interne de données distribuées, qui consomme plus d’UC, mémoire et même la bande passante réseau. Par conséquent, les performances sont plus lent que **echo**.

Dans le mode par défaut, le serveur d’applications peut également être un goulot d’étranglement pour certains scénarios, car Azure SignalR SDK doit appeler le concentrateur, il maintient la connexion en direct avec chaque client par le biais des signaux de pulsation dans le même temps.

En mode sans serveur, le client envoie le message par HTTP post, ce qui n’est pas aussi efficace que WebSocket.

Un autre facteur est un protocole : JSON et [MessagePack](https://msgpack.org/index.html). MessagePack est la plus petite taille et est remis plus rapidement que JSON. Intuitivement, MessagePack tire avantage des performances, mais les performances de ASRS ne sont pas sensibles à l’aide de protocoles dans la mesure où elle ne décode pas la charge utile de message pendant le transfert de messages à partir de clients aux serveurs ou vice versa.

En résumé, les facteurs suivants ont un impact sur la capacité de trafic entrante et sortante :

-   Niveau de référence (processeur/mémoire)

-   nombre de connexions

-   Taille de message

-   taux d’envoi de message

-   type de transport (WebSocket/serveur-envoi-événement / d’interrogation longue)

-   Utilisez le scénario de cas (coût de routage)

-   connexions serveur et le service d’application (en mode de serveur)


### <a name="find-a-proper-sku"></a>Trouver une référence appropriée

Comment évaluer la capacité entrant/sortant ou comment trouver le niveau est adapté à un cas d’usage spécifiques ?

Nous supposons que le serveur d’applications est suffisamment puissant et n’est pas le goulot d’étranglement de performances. Ensuite, nous pouvons vérifier la trafic entrante et sortante bande passante maximale pour chaque niveau.

#### <a name="quick-evaluation"></a>Évaluation rapide

Nous allons simplifier tout d’abord la version d’évaluation en supposant certains paramètres par défaut : WebSocket est utilisé, taille du message est égale à 2 048 octets, ssage toutes les secondes, et il est par défaut en mode.

Chaque niveau possède son propre bande passante entrante maximale et la bande passante sortante. Expérience utilisateur n’est pas garanti une fois que le trafic entrant ou sortant dépasse la limite.

**Echo** donne la bande passante entrante maximale, car il possède le coût de routage le plus bas. **Diffusion** définit la bande passante maximale des messages sortants.

Faire **pas** dépasser les valeurs en surbrillance dans les deux tables suivantes.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Bande passante entrante (octets/s)** | **2M**    | **4M**    | **10M**   | **20 MILLIONS**    | **40M**    | **100 MILLIONS**   | **200 MO**    |
| Bande passante sortante (octets/s) | 2M    | 4M    | 10M   | 20 MILLIONS    | 40M    | 100 MILLIONS   | 200 MO    |


|     Diffusion             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Bande passante entrante (octets/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Bande passante sortante (octets/s)** | **4M**    | **8 Mo**    | **20 MILLIONS**    | **40M**    | **80 MO**    | **200 MO**    | **400M**   |

La bande passante entrante et les formules de la bande passante sortante :
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: le nombre de connexions en envoyant le message

*outboundConnections*: le nombre de connexions de réception de message

*messageSize*: la taille d’un message unique (valeur moyenne). Pour un petit message dont la taille est inférieure à 1 024 octets, il a l’impact des performances similaires en tant que message de 1024 octets.

*sendInterval*: l’heure d’envoi d’un message, en général, il est de 1 seconde par message, ce qui signifie qu’envoyant un message de chaque seconde. SendInterval plus petit signifie ssage plus donné période de temps. Par exemple, 0,5 seconde par message signifie que l’envoi de deux messages par seconde.

*Connexions* est le seuil maximal ASRS validé pour chaque niveau. Si le nombre de connexions est augmenté de plus, il subira à partir de la limitation de la connexion.

*Bande passante entrante* et *la bande passante sortante* sont la taille totale du message par seconde. Suis ici » signifie mégaoctet par souci de simplicité.

#### <a name="evaluation-for-complex-use-cases"></a>Évaluation des cas d’utilisation complexes

##### <a name="bigger-message-size-or-different-sending-rate"></a>Plus grande taille de message ou différents taux d’envoi

Le cas d’utilisation réelle est plus complexe. Il peut envoyer un message supérieur à 2 048 octets ou taux d’envoi message n’est pas un message par seconde. Prenons les diffusion d’unit100 comme un exemple pour découvrir comment évaluer ses performances.

Le tableau suivant illustre un cas réel de **diffusion**, mais la taille de message, le nombre de connexions et le message envoi taux sont différents de ce que nous avons supposé dans la section précédente. La question est la façon dont nous pouvons déduire un de ces éléments (taille de message, nombre de connexions ou taux d’envoi message) si nous ne savons que 2 d'entre eux.

| Diffusion  | Taille de message (en octets) | Trafic entrant (message/s) | connexions | Envoyer les intervalles (secondes) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KO                 | 1                        | 100 000     | 5.                       |
| 2 | 256 KO                | 1                        | 8 000       | 5.                       |

La formule suivante est facilement pour être déduit en fonction de la formule précédente existante :

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Pour unit100, nous savons que la bande passante sortante maximale est de 400M à partir du tableau précédent, puis pour la taille de message de 20 Ko, le nombre maximal de connexions sortant doit être de 400 millions \* 5 / 20 Ko = 100 000, qui correspond à la valeur réelle.

##### <a name="mixed-use-cases"></a>Cas d’usage mixte

Le cas d’usage réel généralement associe les quatre cas d’utilisation de base : **echo**, **diffusion**, **envoi au groupe**, ou **envoyer à la connexion**. La méthodologie utilisée pour évaluer la capacité consiste à diviser les cas d’usage mixte en quatre cas d’utilisation de base, **calculer la bande passante maximale des messages entrants et sortants** séparément à l’aide de formules ci-dessus et additionner pour obtenir le total bande passante entrante/sortante maximale. Sélectionne le niveau approprié à partir des tables de la bande passante entrante/sortante maximale.

Pendant ce temps, pour l’envoi de message pour des centaines ou des milliers de petits groupes, des milliers de clients envoyant des messages entre eux, le coût de routage devient dominant. Cet impact doit être pris en compte. Plus de détails sont traités dans les sections suivantes de la « Case study ».

Dans le cas d’utilisation de l’envoi de message pour les clients, assurez-vous que le serveur d’applications est **pas** le goulot d’étranglement. « Étude de cas » section donne les instructions sur les serveurs d’applications combien vous avez besoin et le nombre de connexions de serveur doit être configuré.

## <a name="case-study"></a>Étude de cas

Les sections suivantes passent par quatre scénarios d’utilisation classiques pour le transport WebSocket : **echo**, **diffusion**, **envoi au groupe**, et **envoi-connexion** . Pour chaque scénario, il répertorie le ASRS actuel entrant et sortante capacité, dans le même temps présente les principaux facteurs sur les performances.

Par défaut le mode serveur d’applications, via le SDK de Service Azure SignalR par défaut, crée les cinq connexions au serveur avec ASRS. Dans les performances de test résultat ci-dessous, les connexions sont augmentées à 15 (ou plus pour la diffusion et l’envoi du message au groupe big) de serveur.

Différents cas d’usage ont exigence différents sur les serveurs d’applications. **Diffusion** doit petit nombre de serveurs d’applications. **Echo** ou **envoi-connexion** a besoin de nombreux serveurs d’applications.

Dans tous les cas d’utilisation, la taille de message par défaut est de 2 048 octets et intervalle d’envoi de message est de 1 seconde.

## <a name="default-mode"></a>Mode par défaut

Les clients, serveurs d’applications web et ASRS sont impliqués dans ce mode. Chaque client représente une connexion unique.

### <a name="echo"></a>Echo

Tout d’abord, les applications web se connectent à ASRS. Deuxièmement, nombreux clients se connectent à l’application web, qui redirige les clients à ASRS avec le jeton d’accès et le point de terminaison. Ensuite, les clients établissent des connexions WebSocket avec ASRS.

Une fois que tous les clients établissent des connexions, ils commencer à envoyer de message, qui contient un horodatage pour le Hub spécifique à chaque seconde. Le Hub renvoie le message à son client d’origine. Tous les clients calcule le temps de latence lorsqu’il reçoit le message d’écho précédent.

Les étapes 5\~8 (trafic mis en surbrillance rouge) se trouvent dans une boucle, ce qui s’exécute pour une durée par défaut (5 minutes) et obtenir les statistiques de tous les temps de latence de message.
Le guide des performances indique le nombre maximal numéro de connexion du client.

![Echo](./media/signalr-concept-performance/echo.png)

**Echo**du comportement détermine que la bande passante entrante maximale est égale à la bande passante sortante maximale. Consultez le tableau suivant.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Trafic entrant/sortant (message/s) | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Bande passante du trafic entrant/sortant (octets/s) | 2M    | 4M    | 10M   | 20 MILLIONS    | 40M    | 100 MILLIONS   | 200 MO    |

Dans ce cas, chaque client appelle le concentrateur défini dans le serveur d’application. Le hub appelle simplement la méthode définie dans le côté client d’origine. Ce concentrateur est le concentrateur pesé plus clair pour **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Même pour ce hub simple, la pression de trafic sur le serveur d’applications est également visible en tant que le **echo** entrants message augmente. Par conséquent, il nécessite plusieurs serveurs d’applications pour les niveaux de référence (SKU) volumineux. Le tableau suivant répertorie le nombre de serveurs d’application pour chaque niveau.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> La client connexion number, taille des messages, message d’envoi de taux, niveau de référence et d’UC/mémoire du serveur d’applications ont l’impact sur les performances globales de **echo**.

### <a name="broadcast"></a>Diffusion

Pour **diffusion**, lors de l’application web reçoit le message, il diffuse à tous les clients. Plus aux clients de diffusion, le trafic des messages plus à tous les clients. Consultez le diagramme suivant.

![Diffusion](./media/signalr-concept-performance/broadcast.png)

La caractéristique de diffusion est qu’il existe un petit nombre de clients de diffusion, ce qui signifie que la bande passante de message entrant est petite, mais la bande passante sortante est énorme. La bande passante de message sortant augmente en tant que la connexion cliente ou taux de diffusion augmente.

Les connexions clientes maximale, le nombre de messages entrant et sortant et la bande passante sont résumées dans le tableau suivant.

|     Diffusion             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Trafic entrant (message/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Sortant (message/s) | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Bande passante entrante (octets/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Bande passante sortante (octets/s) | 4M    | 8 Mo    | 20 MILLIONS    | 40M    | 80 MO    | 200 MO    | 400M    |

Les clients de diffusion qui publient des messages sont pas plus de 4, nécessite donc moins de serveurs application comparés **echo** dans la mesure où la quantité de message entrant est faible. Deux serveurs d’applications sont suffisants pour prendre en compte les performances et de contrat SLA. Mais les connexions du serveur par défaut doivent être augmentées pour éviter un problème non équilibré en particulier pour Unit50 et Unit100.

|   Diffusion      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Augmenter les connexions du serveur par défaut de 5 à 40 sur chaque serveur d’applications afin d’éviter les connexions de serveur déséquilibrée possibles à ASRS.
>
> Le nombre de connexions client, taille de message, fréquence d’envoi message et niveau de référence (SKU) ont un impact sur les performances globales de **diffusion**

### <a name="send-to-group"></a>Envoi au groupe

**Envoi au groupe** a le modèle de trafic similaires, sauf qu’après l’établissement de connexions WebSocket avec ASRS de clients, ils doivent joindre groupes avant d’envoyer des messages à un groupe spécifique. Le flux de trafic est illustré par le diagramme suivant.

![Envoyer à un groupe](./media/signalr-concept-performance/sendtogroup.png)

Membre du groupe et le nombre de groupes sont deux facteurs avec un impact sur les performances. Pour simplifier l’analyse, nous définissons deux types de groupes : groupe petit et grand groupe.

- `small group`: 10 connexions dans chaque groupe. Le numéro de groupe est égal à (nombre de connexions maximal) / 10. Par exemple, pour l’unité 1, s’il existe des décomptes de connexion de 1000, puis nous avons 1000 / 10 = 100 groupes.

- `Big group`: Numéro de groupe est toujours de 10. Le nombre de membres de groupe est égal à (nombre de connexions maximal) / 10. Par exemple, pour 1 unité, s’il existe des décomptes de connexion de 1000, puis chaque groupe a 1000 / 10 = 100 membres.

**Groupe de l’envoi** apporte de coût de routage à ASRS, car il doit rechercher les connexions cibles via une structure de données distribuées. Comme les connexions envoi augmentent, le coût augmente également.

#### <a name="small-group"></a>Petit groupe

Le coût de routage est important pour l’envoi de message à de nombreux petits groupes. Actuellement, l’implémentation de ASRS atteint la limite de coût de routage à unit50. Ajout de plus d’UC et mémoire ne pas vous aider à, donc unit100 ne peut pas améliorer davantage en conception. Si vous exigez la bande passante entrante de plus, contactez le support technique pour la personnalisation.

|   Envoyer à un petit groupe     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000 | 100 000
| Nombre de membres de groupe        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Nombre de groupes               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Trafic entrant (message/s)  | 200   | 400   | 1 000  | 2 500  | 4 000  | 7 000  | 7 000   |
| Bande passante entrante (octets/s)  | 400 K  | 800 K  | 2M     | 5 Mo     | 8 Mo     | 14M    | 14M     |
| Sortant (message/s) | 2 000 | 4 000 | 10 000 | 25 000 | 40 000 | 70,000 | 70,000  |
| Bande passante sortante (octets/s) | 4M    | 8 Mo    | 20 MILLIONS    | 50 MILLIONS     | 80 MO    | 140M   | 140M    |

Il existe de nombreuses connexions client appelant le hub, par conséquent, nombre de serveur d’application est également critique pour les performances. Le nombre de serveurs d’application suggérée est répertorié dans le tableau suivant.

|  Envoyer à un petit groupe   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Le nombre de connexions client, taille de message, fréquence d’envoi message, coût de routage, niveau de référence et d’UC/mémoire du serveur d’applications ont un impact sur les performances globales de **envoi au groupe petit**.

#### <a name="big-group"></a>Groupe Big

Pour **envoi-big-groupe**, la bande passante sortante devienne le goulot d’étranglement avant d’atteindre le routage limite de coût. Le tableau suivant répertorie la bande passante sortante maximale qui est presque identique en tant que **diffusion**.

|    Envoyer à un grand groupe      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000
| Nombre de membres de groupe        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Nombre de groupes               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Trafic entrant (message/s)  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Bande passante entrante (octets/s)  | 80 KO   | 40 K   | 40 K    | 20 KO    | 40 K    | 40 K     | 40 K     |
| Sortant (message/s) | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Bande passante sortante (octets/s) | 8 Mo    | 8 Mo    | 20 MILLIONS    | 40M    | 80 MO    | 200 MO    | 400M    |

Le nombre de connexions envoi n’est pas plus de 40, la charge pesant sur le serveur d’applications est petite, donc le nombre d’application web suggéré est également petit.

|  Envoyer à un grand groupe  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Augmenter les connexions du serveur par défaut de 5 à 40 sur chaque serveur d’applications afin d’éviter les connexions de serveur déséquilibrée possibles à ASRS.
> 
> Le nombre de connexions client taille de message, fréquence d’envoi message, coût de routage et niveau de référence (SKU) ont un impact sur les performances globales de **envoi-big-groupe**.

### <a name="send-to-connection"></a>Envoyer à la connexion

Dans ce cas, lorsque les clients établissent les connexions à ASRS, chaque client appelle un concentrateur spécial pour obtenir leur propre ID de connexion. Le test d’évaluation des performances est chargé de collecter tous les ID de connexion, les mélanger et réaffectez-les à tous les clients comme une cible d’envoi. Les clients de continuer à envoyer message à la connexion cible jusqu'à ce que le test de performances se termine.

![Envoyer au client](./media/signalr-concept-performance/sendtoclient.png)

Le routage des coûts **envoi-connexion** est similaire à la **envoi au groupe petit**.

À mesure que le nombre de connexions augmente, les performances globales sont limité par le coût de routage. Unité 50 a atteint la limite. Par conséquent, les 100 unités ne peut pas améliorer encore plus.

Le tableau suivant est une statistique résumée après de nombreux essais d’exécution **envoi-connexion** banc d’essai

|   Envoyer à la connexion   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| connexions                        | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Entrant / sortant (message/s) | 1 000 | 2 000 | 5 000 | 8 000  | 9 000  | 20 000 | 20 000 |
| Bande passante entrante / sortante (octets/s) | 2M    | 4M    | 10M   | 16 Mo    | 18M    | 40M       | 40M       |

Ce cas d’usage nécessite une charge élevée sur le côté de serveur d’application. Voir le serveur d’applications suggérées compter dans le tableau suivant.

|  Envoyer à la connexion  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Le nombre de connexions client, taille de message, fréquence d’envoi message, coût de routage, niveau de référence et d’UC/mémoire du serveur d’applications ont un impact sur les performances globales de **envoi-connexion**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>ASP.NET SignalR echo/diffusion/envoi-vers-connexion

ASRS fournit les mêmes capacités de performances d’ASP.NET SignalR. Cette section donne le nombre d’application web suggéré pour ASP.NET SignalR **echo**, **diffusion**, et **envoi au groupe petit**.

Le test de performances utilise Azure application Web de [Standard S3 de Plan de Service](https://azure.microsoft.com/pricing/details/app-service/windows/) pour ASP.NET SignalR.

- `echo`

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Diffusion       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Envoyer à un petit groupe     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Mode sans serveur

Les clients et ASRS sont impliqués dans ce mode. Chaque client représente une connexion unique. Le client envoie des messages via l’API REST à un autre client diffusion des messages ou tous.

Envoi de messages à haute densité via l’API REST n’est pas aussi efficace que WebSocket, car il a besoin pour créer une nouvelle connexion HTTP chaque fois - un coût en mode sans serveur supplémentaire.

### <a name="broadcast-through-rest-api"></a>Diffusion via l’API REST
Tous les clients WebSocket de connexions établissent avec ASRS. Puis certains clients de démarrer la diffusion via l’API REST. (Entrant) d’envoi du message sont soutenue HTTP Post, ce qui n’est pas efficace comparé avec WebSocket.

|   Diffusion via l’API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Trafic entrant (message/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Sortant (message/s) | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Bande passante entrante (octets/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Bande passante sortante (octets/s) | 4M    | 8 Mo    | 20 MILLIONS    | 40M    | 80 MO    | 200 MO    | 400M    |

### <a name="send-to-user-through-rest-api"></a>Envoyer à l’utilisateur via l’API REST
Le test d’évaluation attribue des noms d’utilisateur pour tous les clients avant qu’ils commencent à se connecter à ASRS. Une fois que les clients établissement une connexion WebSocket avec ASRS, ils commencer à envoyer des messages à d’autres personnes via HTTP Post.

|   Envoyer à l’utilisateur via l’API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Trafic entrant (message/s)  | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000  |
| Sortant (message/s) | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000 |
| Bande passante entrante (octets/s)  | 600 KO  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10M     | M 36    |
| Bande passante sortante (octets/s) | 600 KO  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10M     | M 36    |

## <a name="performance-test-environments"></a>Environnements de test de performances

Le test de performances pour tous les cas d’utilisation ci-dessus ont été effectués dans l’environnement Azure. À la plupart des machines virtuelles de 50 clients et le serveur d’application 20 machines virtuelles sont utilisées.

Taille de machine virtuelle du client : StandardDS2V2 (2 processeurs virtuels, de mémoire 7G)

Taille de machine virtuelle du serveur d’applications : StandardF4sV2 (4 processeurs virtuels, mémoire de 8G)

Connexions au serveur SignalR SDK Azure : 15

## <a name="performance-tools"></a>Outils de performances

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous obtenez une vue d’ensemble des performances du Service de SignalR dans les scénarios de cas d’utilisation classiques.

Pour obtenir plus d’informations sur les mécanismes internes de SignalR Service et de mise à l’échelle pour le SignalR Service, lisez le guide suivant.

* [Éléments internes de Service Azure SignalR](signalr-concept-internals.md)
* [Service Azure SignalR mise à l’échelle](signalr-howto-scale-multi-instances.md)