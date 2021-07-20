---
title: Vue d’ensemble de l’API d’automatisation des appels Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Fournit une vue d’ensemble de la fonctionnalité d’automatisation des appels et des API associées.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5cf4093f74fc95d0c012a6b77093231e966d2408
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106206"
---
# <a name="call-automation-apis-overview"></a>Vue d’ensemble des API d’automatisation des appels
Les API d’automatisation des appels vous permettent de communiquer avec vos utilisateurs à grande échelle à l’aide d’une logique métier automatisée. Vous pouvez utiliser ces API pour créer des appels sortants automatisés de rappel de rendez-vous, ou pour fournir des notifications concernant des événements tels que des coupures de courant ou des incendies de forêt. Les applications ajoutées à un appel peuvent superviser les mises à jour au fur et à mesure de l’arrivée ou du départ de participants, ce qui vous permet d’implémenter la création de rapports et la journalisation. 

![Applications In-Call et Out-of-Call](../media/call-automation-apps.png)

Les API d’automatisation des appels sont fournies pour les actions In-Call (application-participant ou app-participant) et les actions Out-of-Call. Les deux principales différences entre ces ensembles d’API sont les suivantes :
- Les API In-Call impliquent que votre application joigne l’appel en tant que participant. Les actions app-participant sont facturées selon les [tarifs standard RTC et VoIP](https://azure.microsoft.com/pricing/details/communication-services/).
- Les API In-Call utilisent l’élément `callConnectionId` associé à app-participant tandis que les API Out-of-Call utilisent l’élément `serverCallId` associé à l’instance d’appel. 

## <a name="use-cases"></a>Cas d'utilisation
| Cas d’usage                                                       | In-Call (app-participant) | Out-of-Call   |
| ---------------------------------------------------------------| ------------------------- | ------------- |
| Passer ou recevoir des appels 1:1 entre le bot et les participants humains  | X                         |               |
| Lire les invites audio et écouter les réponses                    | X                         |               |
| Surveiller les événements In-Call                                         | X                         |               |
| Créer des appels avec plusieurs participants                        | X                         |               |
| Obtenir les participants aux appels et les détails les concernant                  | X                         |               |
| Ajouter ou supprimer des participants aux appels                                | X                         | X             |
| Actions côté serveur lors des appels d’homologue à homologue (enregistrement, par exemple)     |                           | X             |
| Lire les annonces audio pour tous les participants                   |                           | X             |
| Démarrer et gérer l’enregistrement des appels                                |                           | X             |

## <a name="in-call-app-participant-apis"></a>API In-Call (app-participant)
> [!NOTE] 
> Les applications In-Call sont facturées en tant que participants à l’appel, aux [tarifs RTC et VoIP standard](https://azure.microsoft.com/pricing/details/communication-services/).

> [!NOTE] 
> Les actions In-Call sont attribuées à l’élément app-participant associé à l’élément `callConnectionId` utilisé dans l’appel d’API.

Les API In-Call permettent à une application d’effectuer des actions dans un appel en tant qu’élément app-participant. Lorsqu’une application répond ou joint un appel, un élément `callConnectionId` est attribué et utilisé pour les actions In-Call telles que :
- Ajouter ou supprimer des participants aux appels
- Lire les invites audio et écouter les réponses DTMF
- Écouter les mises à jour et événements de la liste d’appels
- Mettre fin à un appel

![Application In-Call](../media/call-automation-in-call.png)

### <a name="in-call-events"></a>Événements intra-appel
Les notifications des événements sont envoyées en tant que charges utiles JSON à l’application appelante via l’élément `callbackUri` défini pour rejoindre l’appel. Les événements envoyés aux participants sont les suivants :
- Événements CallState (Establishing, Established, Terminating, Terminated)
- DTMF reçu
- Lire le résultat audio
- Annuler le traitement multimédia
- Résultat de l’invitation d’un participant
- Participants mis à jour

## <a name="out-of-call-apis"></a>API hors appel
Les API Out-of-Call vous permettent d’effectuer des actions sur un appel ou une réunion sans qu’un élément app-participant soit présent. Les API Out-of-Call utilisent l’élément `serverCallId`, fourni par le kit de développement logiciel (SDK) client Calling ou généré lorsqu’un appel est créé via les API d’automatisation des appels. Les API Out-of-Call ne nécessitant pas d’élément app-participant, elles sont utiles pour implémenter la logique métier côté serveur dans des appels d’homologue à homologue. Par exemple, considérez un scénario d’appel de support démarré en tant qu’appel d’homologue à homologue lors duquel l’agent (participant B) souhaite apporter son aide à un expert en la matière. Le participant B déclenche un événement dans l’interface client pour une application serveur afin d’identifier un expert sur le sujet et l’inviter à participer à l’appel. L’état final du flux présenté ci-dessous correspond à un appel de groupe avec trois participants humains.

![Application Out-of-Call](../media/call-automation-out-of-call.png)

Les API Out-of-Call sont disponibles pour les actions suivantes :
- Ajouter ou supprimer des participants aux appels
- Démarrer/arrêter/suspendre/reprendre l’enregistrement des appels
                                                       
### <a name="out-of-call-events"></a>Événements Out-of-Call
Les notifications d’événements sont envoyées en tant que charges utiles JSON à l’application appelante via l’élément `callbackUri` fourni dans l’appel d’API d’origine. Les actions avec événements Out-of-Call correspondants sont les suivants :
- Enregistrement de l’appel (démarrer, arrêter, suspendre, reprendre)
- Résultat de l’invitation d’un participant

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez [Exemple de démarrage rapide d’automatisation des appels](../../quickstarts/voice-video-calling/call-automation-api-sample.md).

En savoir plus sur l’[Enregistrement des appels](./call-recording.md).
