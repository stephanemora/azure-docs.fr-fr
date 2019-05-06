---
title: Guide des performances pour le Service Azure SignalR
description: Vue d’ensemble des performances du Service Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269439"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guide des performances pour le Service Azure SignalR

Un des principaux avantages de l’utilisation du Service Azure SignalR est la facilité de mise à l’échelle des applications SignalR. Dans un scénario à grande échelle, les performances sont un facteur important. 

Dans ce guide, nous allons présenter les facteurs qui affectent les performances des applications SignalR. Nous allons décrire les performances classiques dans différents scénarios de cas d’usage. Au final, nous allons présenter l’environnement et les outils que vous pouvez utiliser pour générer un rapport de performances.

## <a name="term-definitions"></a>Définitions des termes

*Trafic entrant*: Le message entrant pour le Service Azure SignalR.

*Sortant*: Le message sortant à partir du Service Azure SignalR.

*La bande passante*: La taille totale de tous les messages en 1 seconde.

*Mode par défaut*: Le mode de fonctionnement par défaut, lorsqu’une instance de Service Azure SignalR a été créée. Service Azure SignalR attend le serveur d’application pour établir une connexion avec elle avant de pouvoir accepter les connexions clientes.

*Mode sans serveur*: Un mode dans lequel le Service Azure SignalR accepte uniquement les connexions client. Aucune connexion au serveur n’est autorisée.

## <a name="overview"></a>Présentation

Service Azure SignalR définit sept niveaux Standard pour les capacités de performances différentes. Ce guide répond aux questions suivantes :

-   Nouveautés, les performances de Service Azure SignalR classique pour chaque niveau ?

-   Service Azure SignalR répond à mes exigences de débit de message (par exemple, envoi 100 000 messages par seconde) ?

-   Pour mon scénario spécifique, le niveau est approprié pour moi ? Ou, comment puis-je sélectionner le niveau approprié ?

-   Quel type de serveur d’applications (taille de machine virtuelle) est appropriée pour moi ? Combien d'entre eux déployer ?

Pour répondre à ces questions, ce guide fournit tout d’abord une explication des facteurs qui affectent les performances. Il illustre ensuite le nombre maximal de messages entrant et sortant pour chaque niveau de cas d’utilisation classiques : **echo**, **diffusion**, **envoi au groupe**, et **envoyer à connexion** (peer-to-peer Chat).

Ce guide ne peut pas couvrir tous les scénarios (et les différents cas d’usage, les tailles de message, modèles d’envoi de messages et ainsi de suite). Mais il fournit des méthodes pour vous aider à :

- Évaluez vos besoins approximative pour les messages entrants ou sortants.
- Recherchez les niveaux appropriés en vérifiant la table de performances.

## <a name="performance-insight"></a>Analyse des performances

Cette section décrit les méthodes d’évaluation de performances et répertorie ensuite tous les facteurs qui affectent les performances. Au final, il fournit des méthodes pour vous aider à évaluer les exigences de performances.

### <a name="methodology"></a>Méthodologie

*Débit* et *latence* sont deux aspects typiques de la vérification des performances. Pour le Service Azure SignalR, chaque niveau de référence (SKU) a sa propre stratégie de limitation de débit. Définit la stratégie *le nombre maximal autorisé de débit (bande passante entrante et sortante)* comme la valeur maximale réalisée débit lorsque 99 % des messages ont une latence est inférieure à 1 seconde.

La latence est l’intervalle de temps à partir de la connexion envoyant le message à recevoir le message de réponse à partir du Service Azure SignalR. Commençons par jeter **echo** comme exemple. Chaque connexion client ajoute un horodatage dans le message. Hub du serveur de l’application envoie le message d’origine vers le client. Par conséquent, le délai de propagation est facilement calculé par chaque connexion client. L’horodatage est attaché pour tous les messages dans **diffusion**, **envoi au groupe**, et **envoyer à la connexion**.

Pour simuler des milliers de connexions clientes simultanées, plusieurs machines virtuelles sont créées dans un réseau privé virtuel dans Azure. Toutes ces machines virtuelles se connectent à la même instance de Service Azure SignalR.

Dans le mode par défaut du Service Azure SignalR, les machines virtuelles du serveur d’applications sont déployés dans le même réseau privé virtuel en tant que machines virtuelles du client. Toutes les machines virtuelles du client et du serveur d’applications des machines virtuelles sont déployées dans le même réseau de la même région afin d’éviter la latence entre les régions.

### <a name="performance-factors"></a>Facteurs de performances

En théorie, la capacité du Service Azure SignalR est limitée par les ressources de calcul : Processeur, mémoire et réseau. Par exemple, davantage de connexions au Service Azure SignalR provoque le service utilise davantage de mémoire. Pour le trafic des messages plus volumineux (par exemple, chaque message est supérieure à 2 048 octets), Service Azure SignalR a besoin de passer plus de cycles processeur pour traiter le trafic. Pendant ce temps, la bande passante réseau Azure impose également une limite pour le trafic maximal.

Le type de transport est un autre facteur qui affecte les performances. Les trois types sont [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [événement envoyé serveur](https://en.wikipedia.org/wiki/Server-sent_events), et [interrogation longue](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket est bidirectionnelle et protocole de communication en duplex intégral sur une connexion TCP unique. Événement serveur envoyé est un protocole unidirectionnel pour transmettre les messages du serveur au client. Interrogation longue demande que les clients interrogent régulièrement les informations à partir du serveur via une requête HTTP. Pour la même API dans les mêmes conditions, WebSocket offre les meilleures performances, événement serveur envoyé est plus lent et interrogation longue est la plus lente. Service Azure SignalR recommande WebSocket par défaut.

Le coût de routage de message limite également les performances. Service Azure SignalR joue un rôle en tant que routeur de messages, qui achemine le message à partir d’un ensemble de clients ou serveurs pour d’autres clients ou serveurs. Un scénario différent ou l’API nécessite une stratégie de routage différente. 

Pour **echo**, le client envoie un message à elle-même, et la destination de routage est également lui-même. Ce modèle est le coût de routage le plus bas. Mais pour **diffusion**, **envoi au groupe**, et **envoyer à la connexion**, Service Azure SignalR a besoin de rechercher les connexions cibles via les données distribuées internes structure. Ce traitement supplémentaire utilise plus de processeur, de mémoire et de la bande passante réseau. Par conséquent, les performances sont plus lentes.

Le mode par défaut, le serveur d’applications peut également devenir un goulot d’étranglement pour certains scénarios. Le Kit de développement SignalR doit appeler le hub, tandis qu’il maintient une connexion active avec chaque client par le biais des signaux de pulsation.

En mode sans serveur, le client envoie un message par HTTP post, ce qui n’est pas aussi efficace que WebSocket.

Un autre facteur est un protocole : JSON et [MessagePack](https://msgpack.org/index.html). MessagePack est la plus petite taille et est remis plus rapidement que JSON. MessagePack ne peut pas améliorer les performances, cependant. Les performances de Service Azure SignalR ne sont pas sensible aux protocoles, car elle ne décoder la charge utile de message pendant le transfert de messages à partir de clients aux serveurs ou vice versa.

En résumé, les facteurs suivants affectent la capacité de trafic entrante et sortante :

-   Niveau de référence (processeur/mémoire)

-   Nombre de connexions

-   Taille des messages

-   taux d’envoi de message

-   Type de transport (WebSocket, événement serveur envoyé ou interrogation longue)

-   Scénario d’utilisation (coût de routage)

-   connexions serveur et le service d’application (en mode de serveur)


### <a name="finding-a-proper-sku"></a>Recherche une référence SKU appropriée

Comment pouvez évaluer la capacité entrant/sortant ou trouver quel niveau convient pour un cas d’usage spécifiques ?

Supposons que le serveur d’application est suffisamment puissant et qu’il n’est pas le goulot d’étranglement de performances. Vérifiez ensuite la trafic entrante et sortante bande passante maximale pour chaque niveau.

#### <a name="quick-evaluation"></a>Évaluation rapide

Nous allons simplifier tout d’abord la version d’évaluation en supposant certains paramètres par défaut : 

- Le type de transport est WebSocket.
- La taille du message est de 2 048 octets.
- Un message est envoyé à toutes les secondes.
- Service Azure SignalR est en mode par défaut.

Chaque niveau possède son propre bande passante entrante maximale et la bande passante sortante. Une fois que la connexion entrante ou sortante dépasse la limite, une expérience utilisateur n’est pas garantie.

**Echo** donne la bande passante entrante maximale, car il possède le coût de routage le plus bas. **Diffusion** définit la bande passante maximale des messages sortants.

Faire *pas* dépasser les valeurs en surbrillance dans les deux tables suivantes.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Bande passante entrante** | **2 Mo/s**    | **4 Mo/s**    | **10 Mo/s**   | **20 Mo/s**    | **40 Mo/s**    | **100 Mo/s**   | **200 Mo/s**    |
| Bande passante sortante | 2 Mo/s   | 4 Mo/s   | 10 Mo/s  | 20 Mo/s   | 40 Mo/s   | 100 Mo/s  | 200 Mo/s   |


|     Diffusion             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Bande passante entrante  | 4 Kbits/s   | 4 Kbits/s   | 4 Kbits/s    | 4 Kbits/s    | 4 Kbits/s    | 4 Kbits/s     | 4 Kbits/s    |
| **Bande passante sortante** | **4 Mo/s**    | **8 Mo/s**    | **20 Mo/s**    | **40 Mo/s**    | **80 Mo/s**    | **200 Mo/s**    | **400 Mo/s**   |

*Bande passante entrante* et *la bande passante sortante* sont la taille totale du message par seconde.  Voici les formules pour eux :
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Le nombre de connexions en envoyant le message.

- *outboundConnections*: Le nombre de connexions de réception du message.

- *messageSize*: La taille d’un message unique (valeur moyenne). Un petit message d’octets inférieur à 1 024 a un impact sur les performances est similaire à un message de 1 024 octets.

- *SendInterval*: L’heure d’envoi d’un message. En règle générale, il est 1 seconde par message, ce qui signifie qu’envoyant un message de chaque seconde. Un intervalle est petit, envoi de message plus dans un laps de temps. Par exemple, 0,5 seconde par message signifie que l’envoi de deux messages par seconde.

- *Connexions* : Le seuil maximal validé pour le Service Azure SignalR pour chaque niveau. Si le nombre de connexions est augmenté de plus, il subira à partir de la limitation de la connexion.

#### <a name="evaluation-for-complex-use-cases"></a>Évaluation des cas d’utilisation complexes

##### <a name="bigger-message-size-or-different-sending-rate"></a>Plus grande taille de message ou différents taux d’envoi

Le cas d’utilisation réelle est plus complexe. Il peut envoyer un message supérieur à 2 048 octets, ou le taux d’envoi message n’est pas un message par seconde. Prenons les diffusion de Unit100 comme un exemple pour découvrir comment évaluer ses performances.

Le tableau suivant présente un cas d’usage réel de **diffusion**. Mais la taille de message, le nombre de connexions et le message envoi taux diffèrent de ce que nous avons supposé dans la section précédente. La question est la façon dont nous pouvons déduire un de ces éléments (taille de message, nombre de connexions ou taux d’envoi message) si nous savons que seuls deux d'entre eux.

| Diffusion  | Taille des messages | Messages entrants par seconde | connexions | Envoyer des intervalles |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KO                | 1                        | 100 000     | 5 secondes                      |
| 2 | 256 KB               | 1                        | 8 000       | 5 secondes                      |

La formule suivante est facile de déduire selon la formule précédente :

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Pour Unit100, la bande passante sortante maximale est de 400 Mo à partir de la table précédente. Pour une taille de message de 20 Ko, le nombre maximal de connexions sortant doit être de 400 Mo \* 5 / 20 Ko = 100 000, qui correspond à la valeur réelle.

##### <a name="mixed-use-cases"></a>Cas d’usage mixte

Le cas d’usage réel généralement associe les quatre cas d’utilisation de base : **echo**, **diffusion**, **envoi au groupe**, et **envoyer à la connexion**. La méthodologie qui vous permet d’évaluer la capacité consiste à :

1. Diviser les cas d’usage mixte en quatre cas d’utilisation de base.
1. Calculer la bande passante maximale des messages entrants et sortants en utilisant les formules précédentes séparément.
1. Additionner les calculs de la bande passante pour obtenir la total entrant/sortant la bande passante maximale. 

Sélectionne le niveau approprié à partir des tables de la bande passante entrante/sortante maximale.

> [!NOTE]
> Pour envoyer un message à des centaines voire des milliers de petits groupes, ou pour des milliers de clients envoyant un message à l’autre, le coût de routage deviendra dominant. Prenez cet impact en compte.

Dans le cas d’utilisation de l’envoi d’un message aux clients, assurez-vous que le serveur d’applications est *pas* le goulot d’étranglement. La section « Case study » suivante donne des indications sur les serveurs d’applications combien vous avez besoin et le nombre de connexions de serveur, vous devez configurer.

## <a name="case-study"></a>Étude de cas

Les sections suivantes passent par quatre scénarios d’utilisation classiques pour le transport WebSocket : **echo**, **diffusion**, **envoi au groupe**, et **envoyer à la connexion**. Pour chaque scénario, la section répertorie la capacité actuelle de trafic entrante et sortante pour le Service Azure SignalR. Il explique également les principaux facteurs qui affectent les performances.

Dans le mode par défaut, le serveur de l’application crée cinq connexions au serveur avec le Service Azure SignalR. Le serveur d’application utilise le SDK du Service Azure SignalR par défaut. Dans les résultats de test de performances suivants, les connexions au serveur sont augmentées à 15 (ou plus pour la diffusion et envoyer un message à un grand groupe).

Différents cas d’utilisation ont des exigences différentes pour les serveurs d’applications. **Diffusion** doit petit nombre de serveurs d’applications. **Echo** ou **envoyer à la connexion** a besoin de nombreux serveurs d’applications.

Dans tous les cas d’utilisation, la taille de message par défaut est de 2 048 octets, et l’intervalle d’envoi de message est de 1 seconde.

### <a name="default-mode"></a>Mode par défaut

Les clients, serveurs d’applications web et Service Azure SignalR sont impliqués dans le mode par défaut. Chaque client représente une connexion unique.

#### <a name="echo"></a>Echo

Tout d’abord, une application web se connecte au Service Azure SignalR. En second lieu, de nombreux clients se connectent à l’application web, qui redirige les clients au Service Azure SignalR avec le jeton d’accès et le point de terminaison. Ensuite, les clients établissent des connexions de WebSocket avec le Service Azure SignalR.

Une fois que tous les clients établissent des connexions, ils commencer à envoyer un message qui contient un horodatage pour le hub spécifique à chaque seconde. Le hub renvoie le message à son client d’origine. Tous les clients calcule le temps de latence lorsqu’il reçoit le message d’écho précédent.

Dans le diagramme suivant, 5 à 8 (trafic mis en surbrillance rouge) sont dans une boucle. La boucle s’exécute pour une durée par défaut (5 minutes) et obtient les statistiques de tous les temps de latence de message.

![Trafic pour le cas d’usage echo](./media/signalr-concept-performance/echo.png)

Le comportement de **echo** détermine que la bande passante entrante maximale est égale à la bande passante sortante maximale. Pour plus d’informations, consultez le tableau suivant.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Messages entrants/sortants par seconde | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Bande passante entrante/sortante | 2 Mo/s   | 4 Mo/s   | 10 Mo/s  | 20 Mo/s   | 40 Mo/s   | 100 Mo/s  | 200 Mo/s   |

Dans ce cas, chaque client appelle le concentrateur défini dans le serveur d’application. Le hub appelle simplement la méthode définie dans le côté client d’origine. Ce hub est le hub plus légère pour **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Même dans ce hub simple, la pression de trafic sur le serveur d’applications est visible en tant que le **echo** entrants message charge augmente. La pression de trafic nécessite plusieurs serveurs d’applications pour les niveaux de référence (SKU) volumineux. Le tableau suivant répertorie le nombre de serveurs d’application pour chaque niveau.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> La client connexion number, taille des messages, le message d’envoi taux, niveau de référence et de processeur/mémoire du serveur d’application affecte les performances globales de **echo**.

#### <a name="broadcast"></a>Diffusion

Pour **diffusion**, lorsque l’application web reçoit le message, il diffuse à tous les clients. Les clients plus il sont à la diffusion, le trafic des messages plus il est à tous les clients. Consultez le diagramme suivant.

![Trafic pour le cas d’usage de diffusion](./media/signalr-concept-performance/broadcast.png)

Un petit nombre de clients diffusant. La bande passante de message entrant est faible, mais la bande passante sortante est énorme. La bande passante de message sortant augmente en tant que la connexion cliente ou taux de diffusion augmente.

Le tableau suivant récapitule les connexions clientes maximale, nombre de messages entrant et sortant et la bande passante.

|     Diffusion             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Messages entrants par seconde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messages sortants par seconde | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Bande passante entrante  | 4 Kbits/s   | 4 Kbits/s   | 4 Kbits/s    | 4 Kbits/s    | 4 Kbits/s    | 4 Kbits/s     | 4 Kbits/s     |
| Bande passante sortante | 4 Mo/s   | 8 Mo/s   | 20 Mo/s   | 40 Mo/s   | 80 Mo/s   | 200 Mo/s   | 400 Mo/s   |

Les clients de diffusion qui publient des messages sont pas plus de quatre. Ils ont besoin les serveurs d’applications moins par rapport aux **echo** , car la quantité de message entrant est faible. Deux serveurs d’applications sont suffisants pour considérations sur les performances et de contrat SLA. Mais vous devez augmenter les connexions du serveur par défaut pour éviter un déséquilibre, en particulier pour Unit50 et Unit100.

|   Diffusion      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Augmenter les connexions du serveur par défaut de 5 à 40 sur chaque serveur d’applications afin d’éviter les connexions serveur déséquilibrée possibles pour le Service Azure SignalR.
>
> Le numéro de connexion du client, taille de message, fréquence d’envoi message et niveau de référence (SKU) affectent les performances globales pour **diffusion**.

#### <a name="send-to-group"></a>Envoi au groupe

Le **envoi au groupe** cas d’usage a un modèle de trafic semblable aux **diffusion**. La différence est qu’une fois que les clients établissent des connexions WebSocket avec le Service Azure SignalR, ils doivent joindre groupes avant de pouvoir envoyer un message à un groupe spécifique. Le diagramme suivant illustre le flux de trafic.

![Trafic pour le cas d’utilisation de l’envoi au groupe](./media/signalr-concept-performance/sendtogroup.png)

Membre du groupe et le nombre de groupes sont deux facteurs qui affectent les performances. Pour simplifier l’analyse, nous définissons deux types de groupes :

- **Petit groupe**: Chaque groupe comporte 10 connexions. Le numéro de groupe est égal à (nombre de connexions maximal) / 10. Par exemple, pour Unit1, s’il existe 1 000 comptes de connexion, puis nous avons 1000 / 10 = 100 groupes.

- **Groupe Big**: Le numéro de groupe est toujours de 10. Le nombre de membres de groupe est égal à (nombre de connexions maximal) / 10. Par exemple, pour Unit1, s’il existe 1 000 comptes de connexion, puis chaque groupe a 1000 / 10 = 100 membres.

**Envoi au groupe** apporte un routage de coût pour le Service Azure SignalR, car il doit rechercher les connexions cibles via une structure de données distribuées. Comme les connexions envoi augmentent, le coût augmente.

##### <a name="small-group"></a>Petit groupe

Le coût de routage est important pour l’envoi de message à de nombreux petits groupes. Actuellement, l’implémentation de Service Azure SignalR atteint la limite de coût de routage à Unit50. Ajout de plus d’UC et mémoire persiste, donc Unit100 ne peut pas améliorer davantage en conception. Si vous avez besoin de plus de trafic entrant de la bande passante, contactez le support technique.

|   Envoyer à un petit groupe     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000 | 100 000
| Nombre de membres de groupe        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Nombre de groupes               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Messages entrants par seconde  | 200   | 400   | 1 000  | 2 500  | 4 000  | 7 000  | 7 000   |
| Bande passante entrante  | 400 Kbits/s  | 800 Kbits/s  | 2 Mo/s     | 5 Mo/s     | 8 Mo/s     | 14 Mo/s    | 14 Mo/s     |
| Messages sortants par seconde | 2 000 | 4 000 | 10 000 | 25 000 | 40 000 | 70,000 | 70,000  |
| Bande passante sortante | 4 Mo/s    | 8 Mo/s    | 20 Mo/s    | 50 Mo/s     | 80 Mo/s    | 140 Mo/s   | 140 Mo/s    |

Nombre de connexions client est appelant le hub, par conséquent, le nombre de serveur d’application est également critique pour les performances. Le tableau suivant répertorie le nombre de serveurs d’application suggérée.

|  Envoyer à un petit groupe   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> La client connexion number, taille des messages, le message d’envoi taux, routage coût, niveau de référence et de processeur/mémoire du serveur d’application affecte les performances globales de **envoyer à un petit groupe**.

##### <a name="big-group"></a>Groupe Big

Pour **envoient au groupe big**, la bande passante sortante devienne le goulot d’étranglement avant d’atteindre le routage limite de coût. Le tableau suivant répertorie la bande passante sortante maximale qui est presque identique à celle de **diffusion**.

|    Envoyer à un grand groupe      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000
| Nombre de membres de groupe        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Nombre de groupes               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Messages entrants par seconde  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Bande passante entrante  | 80 Kbits/s   | 40 Kbits/s   | 40 Kbits/s    | 20 Kbits/s    | 40 Kbits/s    | 40 Kbits/s     | 40 Kbits/s     |
| Messages sortants par seconde | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Bande passante sortante | 8 Mo/s    | 8 Mo/s    | 20 Mo/s    | 40 Mo/s    | 80 Mo/s    | 200 Mo/s    | 400 Mo/s    |

Le nombre de connexions envoi n’est pas plus de 40. La charge pesant sur le serveur d’applications est petite, donc le nombre suggéré d’applications web est faible.

|  Envoyer à un grand groupe  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Augmenter les connexions du serveur par défaut de 5 à 40 sur chaque serveur d’applications afin d’éviter les connexions serveur déséquilibrée possibles pour le Service Azure SignalR.
> 
> Le nombre de connexions client taille de message, fréquence d’envoi message, coût de routage et niveau de référence (SKU) affectent les performances globales de **envoient au groupe big**.

#### <a name="send-to-connection"></a>Envoyer à la connexion

Dans le **envoyer à la connexion** cas d’usage, lorsque les clients établissent les connexions au Service Azure SignalR, chaque client appelle un concentrateur spécial pour obtenir leur propre ID de connexion. Le test d’évaluation de performances collecte tous les ID de connexion mélange les et les réaffecte à tous les clients comme une cible d’envoi. Les clients de continuer à envoyer le message à la connexion cible jusqu'à ce que le test de performances se termine.

![Trafic pour le cas d’utilisation de l’envoi au client](./media/signalr-concept-performance/sendtoclient.png)

Le routage des coûts **envoyer à la connexion** est similaire au coût pour **envoyer à un petit groupe**.

À mesure que le nombre de connexions augmente, le coût de routage limite les performances globales. Unit50 a atteint la limite. Par conséquent, Unit100 ne peut pas améliorer encore plus.

Le tableau suivant est un résumé statistique après de nombreux essais d’exécution le **envoyer à la connexion** banc d’essai.

|   Envoyer à la connexion   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| connexions                        | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Messages entrants/sortants par seconde | 1 000 | 2 000 | 5 000 | 8 000  | 9 000  | 20 000 | 20 000 |
| Bande passante entrante/sortante | 2 Mo/s    | 4 Mo/s    | 10 Mo/s   | 16 Mo/s    | 18 Mo/s    | 40 Mo/s       | 40 Mo/s       |

Ce cas d’usage nécessite une charge élevée sur le côté de serveur d’application. Voir le serveur d’applications suggérées compter dans le tableau suivant.

|  Envoyer à la connexion  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> La client connexion number, taille des messages, le message d’envoi taux, routage coût, niveau de référence et de processeur/mémoire pour le serveur d’application affecte les performances globales de **envoyer à la connexion**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR echo, diffusion et les envoyer à petit groupe

Service Azure SignalR fournit les mêmes capacités de performances d’ASP.NET SignalR. 

Le test de performances utilise Azure Web Apps à partir de [Standard S3 de Plan de Service](https://azure.microsoft.com/pricing/details/app-service/windows/) pour ASP.NET SignalR.

Le tableau suivant donne le nombre d’application web suggéré pour ASP.NET SignalR **echo**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Le tableau suivant donne le nombre d’application web suggéré pour ASP.NET SignalR **diffusion**.

|  Diffusion       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Le tableau suivant donne le nombre d’application web suggéré pour ASP.NET SignalR **envoyer à un petit groupe**.

|  Envoyer à un petit groupe     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| connexions      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de serveurs d’application | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Mode sans serveur

Les clients et le Service Azure SignalR sont impliqués dans le mode sans serveur. Chaque client représente une connexion unique. Le client envoie des messages via l’API REST à un autre client diffusion des messages ou tous.

Envoi de messages à haute densité via l’API REST n’est pas aussi efficace que l’utilisation de WebSocket. Il vous oblige à générer une nouvelle connexion HTTP chaque fois, et c’est un coût en mode sans serveur supplémentaire.

#### <a name="broadcast-through-rest-api"></a>Diffusion via l’API REST
Tous les clients établissent des connexions WebSocket avec le Service Azure SignalR. Puis certains clients de démarrer la diffusion via l’API REST. (Entrant) d’envoi du message est soutenue HTTP Post, ce qui n’est pas efficace comparé avec WebSocket.

|   Diffusion via l’API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Messages entrants par seconde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Messages sortants par seconde | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Bande passante entrante  | 4 Kbits/s    | 4 Kbits/s    | 4 Kbits/s     | 4 Kbits/s     | 4 Kbits/s     | 4 Kbits/s      | 4 Kbits/s      |
| Bande passante sortante | 4 Mo/s    | 8 Mo/s    | 20 Mo/s    | 40 Mo/s    | 80 Mo/s    | 200 Mo/s    | 400 Mo/s    |

#### <a name="send-to-user-through-rest-api"></a>Envoyer à l’utilisateur via l’API REST
Le test d’évaluation attribue des noms d’utilisateur à tous les clients avant qu’ils commencent à se connecter au Service Azure SignalR. Une fois que les clients établissent des connexions WebSocket avec le Service Azure SignalR, ils commencer à envoyer des messages à d’autres personnes via HTTP Post.

|   Envoyer à l’utilisateur via l’API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Messages entrants par seconde  | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000  |
| Messages sortants par seconde | 300   | 600   | 900    | 1,300  | 2 000  | 10 000  | 18,000 |
| Bande passante entrante  | 600 Kbits/s  | 1.2 Mo/s  | 1.8 Mo/s   | 2.6 Mo/s   | 4 Mo/s     | 10 Mo/s     | 36 Mo/s    |
| Bande passante sortante | 600 Kbits/s  | 1.2 Mo/s  | 1.8 Mo/s   | 2.6 Mo/s   | 4 Mo/s     | 10 Mo/s     | 36 Mo/s    |

## <a name="performance-test-environments"></a>Environnements de test de performances

Pour tous les cas répertoriés précédemment d’utilisation, nous avons effectué les tests de performances dans un environnement Azure. Nous avons utilisé au plus 50 machines virtuelles du client et du serveur d’applications 20 machines virtuelles. Voici quelques détails :

- Taille de machine virtuelle du client : StandardDS2V2 (2 processeurs virtuels, de mémoire 7G)

- Taille de machine virtuelle du serveur d’applications : StandardF4sV2 (4 processeurs virtuels, mémoire de 8G)

- Connexions au serveur SignalR SDK Azure : 15

## <a name="performance-tools"></a>Outils de performances

Vous trouverez des outils de performances pour le Service Azure SignalR sur [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez obtenu une vue d’ensemble des performances du Service Azure SignalR dans les scénarios de cas d’usage classiques.

Pour obtenir des détails sur les données internes du service et la mise à l’échelle, lisez les guides suivants :

* [Éléments internes d’Azure SignalR Service](signalr-concept-internals.md)
* [Service Azure SignalR mise à l’échelle](signalr-howto-scale-multi-instances.md)
