---
title: Géo-reprise d’activité après sinistre et haute disponibilité Azure Functions
description: Découvrez comment utiliser les régions géographiques pour la redondance et basculer dans Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 27730e2b6c1ed760f43a054901d16b58ca80acd9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645224"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Géo-reprise d’activité après sinistre dans Azure Functions

Lorsque des régions ou des centres de données Azure entiers subissent un temps d’arrêt, votre code stratégique doit poursuivre son traitement dans une autre région. Cet article explique certaines des stratégies que vous pouvez adopter pour déployer des fonctions assurant la récupération d’urgence.

## <a name="basic-concepts"></a>Concepts de base

Azure Functions s’exécute dans une application de fonction dans une région spécifique. Aucune redondance intégrée n’est disponible. Pour éviter la perte d’exécution pendant les pannes, vous pouvez déployer les mêmes fonctions de manière redondante pour des applications de fonction dans plusieurs régions.  

Lorsque vous exécutez le même code de fonction dans plusieurs régions, il y a deux modèles à prendre en compte :

| Modèle | Description |
| --- | --- |
|**Actif/actif** | Les fonctions des deux régions s’exécutent activement et traitent les événements, soit de manière dupliquée, soit en rotation. Nous recommandons d’utiliser un modèle actif/actif en combinaison avec [Azure Front Door](../frontdoor/front-door-overview.md) pour vos fonctions critiques déclenchées par HTTP. |
|**Actif/passif** | Les fonctions s’exécutent activement dans la région qui reçoit les événements, tandis que les mêmes fonctions dans une deuxième région restent inactives.  Lorsque le basculement est nécessaire, la deuxième région est activée et prend en charge le traitement. Nous recommandons ce modèle pour vos fonctions non déclenchées par HTTP et pilotées par des événements telles que les fonctions déclenchées par Service Bus ou un Event Hub.

Pour en savoir plus sur les déploiements multirégions, consultez l’aide dans [Application web multirégion hautement disponible](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="redundancy-for-http-trigger-functions"></a>Redondance pour les fonctions de déclencheur HTTP

Le modèle actif/actif est le modèle de déploiement le mieux adapté aux fonctions de déclencheur HTTP. Dans ce cas, vous devez utiliser [Azure Front Door](../frontdoor/front-door-overview.md) pour coordonner les demandes entre les deux régions. Azure Front Door peut acheminer les requêtes HTTP et les distribuer par tourniquet entre les fonctions exécutées dans plusieurs régions et vérifier périodiquement l’intégrité de chaque point de terminaison. Lorsqu’une fonction d’une région cesse de répondre aux contrôles d’intégrité, Azure Front Door la retire de la rotation et achemine le trafic uniquement vers les autres fonctions saines.  

![Architecture autour d’Azure Front Door et des applications de fonction](media/functions-geo-dr/front-door.png)  

## <a name="redundancy-for-non-http-trigger-functions"></a>Redondance pour les fonctions de déclencheur non HTTP

La redondance des fonctions qui consomment des événements provenant d’autres services nécessite un modèle différent, qui fonctionne avec le modèle de basculement des services connexes. 

### <a name="activepassive-redundancy-for-non-http-trigger-functions"></a>Redondance active/passive pour les fonctions de déclencheur non HTTP

Le modèle actif/passif permet à une fonction unique de traiter chaque message. Il offre cependant un mécanisme assurant le basculement vers une région secondaire en cas de sinistre. Les applications de fonction fonctionnent avec les comportements de basculement des services partenaires, tels que la [géorécupération d’Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md) et la [géorécupération d’Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md). L’application de fonction secondaire est considérée comme _passive_, car le service de basculement auquel elle est connectée n’est pas actif actuellement, et l’application de fonction est donc essentiellement _inactive_.

Prenons un exemple de topologie utilisant un déclencheur Azure Event Hubs. Dans ce cas, le modèle actif/passif nécessite l’implication des composants suivants :

* Azure Event Hub est déployé dans une région primaire et une région secondaire.
* La [géorécupération d’urgence est activée](../service-bus-messaging/service-bus-geo-dr.md) pour jumeler les Event Hubs primaire et secondaire. Ceci crée également un _alias_ que vous pouvez utiliser pour vous connecter aux Event Hubs et passer du hub primaire au hub secondaire sans changer les informations de connexion.
* Les applications de fonction sont déployées dans les régions primaire et secondaire (basculement), l’application de la région secondaire étant essentiellement inactive, car les messages n’y sont pas envoyés.
* Les applications de fonction se déclenchent sur la chaîne de connexion *directe* (et non l’alias) pour l’Event Hub correspondant. 
* Les serveurs de publication pointant vers le hub d’événements doivent publier sur la chaîne de connexion d’alias. 

![Exemple d’architecture actif/passif](media/functions-geo-dr/active-passive.png)

Avant le basculement, les serveurs de publication effectuant des envois vers l’alias partagé sont routés vers l’Event Hub primaire. L’application de fonction primaire écoute exclusivement le hub d’événements primaire. L’application de fonction secondaire est passive et inactive. Dès que le basculement est déclenché, les serveurs de publication qui effectuent des envois vers l’alias partagé sont routés vers l’Event Hub secondaire. L’application de fonction secondaire devient alors active et son déclenchement commence automatiquement.  Il est possible de réaliser en intégralité un basculement efficace vers une région secondaire à partir du hub d’événements, les fonctions devenant actives uniquement quand le hub d’événements correspondant est actif.

Découvrez d’autres informations et éléments à prendre en compte pour le basculement avec [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) et [Event Hubs](../event-hubs/event-hubs-geo-dr.md).

### <a name="activeactive-redundancy-for-non-http-trigger-functions"></a>Redondance active/active pour les fonctions de déclencheur non HTTP

Vous pouvez toujours déployer des fonctions non déclenchées par HTTP suivant le modèle actif/actif. Toutefois, vous devez réfléchir à la façon dont les deux régions actives vont interagir ou se coordonner entre elles. Lorsque vous déployez la même application de fonction dans deux régions, chacune d’entre elles se déclenchant sur la même file d’attente Service Bus, celles-ci agissent en tant que consommateurs concurrents pour retirer les messages de cette file d’attente. Chaque message est alors traité uniquement par l’une ou l’autre des instances, mais cela signifie aussi qu’il existe toujours un point de défaillance unique sur l’instance unique de Service Bus. 

Au lieu de cela, vous pouvez déployer deux files d’attente Service Bus, l’une dans une région primaire et l’autre dans une région secondaire. Dans ce cas, vous pouvez avoir deux applications de fonction, chacune pointant vers la file d’attente Service Bus active dans sa région. La difficulté de cette topologie réside dans la manière dont les messages de la file d’attente sont distribués entre les deux régions.  Cela signifie souvent que chaque serveur de publication tente de publier un message dans *les deux* régions et que chaque message est traité par les deux applications de fonction actives. Bien que cela crée le modèle actif/actif souhaité, d’autres difficultés apparaissent concernant la duplication du calcul ainsi que la façon dont les données sont regroupées (et à quel moment). En raison de ces difficultés, nous recommandons d’utiliser le modèle actif/passif pour les fonctions de déclencheur non HTTPS.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une porte d’entrée Azure](../frontdoor/quickstart-create-front-door.md)
* [Considérations relatives au basculement avec Event Hubs](../event-hubs/event-hubs-geo-dr.md#considerations)
