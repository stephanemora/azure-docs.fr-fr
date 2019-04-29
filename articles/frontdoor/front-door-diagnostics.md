---
title: Analyse des journaux de métriques et dans le Service de porte d’entrée Azure | Microsoft Docs
description: Cet article décrit les différentes mesures et journaux d’accès qui prend en charge du Service de porte d’entrée Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736778"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Analyse des journaux de métriques et dans le Service de porte d’entrée Azure

À l’aide de Service de porte d’Azure, vous pouvez surveiller les ressources de plusieurs manières :

- **Métriques**. Application Gateway a actuellement sept métriques pour afficher les compteurs de performances.
- **Journaux**. Journaux de diagnostic et d’activité permettent de performances, d’accès et d’autres données d’être enregistré ou consommés à partir d’une ressource à des fins de surveillance.

### <a name="metrics"></a>Mesures

Métriques sont une fonctionnalité de certaines ressources Azure qui vous permettent d’afficher les compteurs de performances dans le portail. Les métriques de porte d’entrée disponibles sont les suivantes :

| Métrique | Nom d’affichage de la métrique | Unité | Dimensions | Description |
| --- | --- | --- | --- | --- |
| RequestCount | Nombre de requêtes | Nombre | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre de requêtes de clients prises en charge par Front Door  |
| RequestSize | Taille de la requête | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que requêtes de clients à Front Door. |
| ResponseSize | Taille de la réponse | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que réponses de Front Door aux clients. |
| TotalLatency | Latence totale | Millisecondes | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | L’heure calculée à partir de la demande du client reçue par la porte d’entrée jusqu'à ce que le client accusé de réception du dernier octet de la réponse à partir de la porte d’entrée. |
| BackendRequestCount | Nombre de requêtes de backend | Nombre | httpStatus</br>HttpStatusGroup</br>Backend | Nombre de requêtes envoyées de Front Door aux backends. |
| BackendRequestLatency | Latence de requête du backend | Millisecondes | Backend | Temps calculé à partir du moment où la requête est envoyée par Front Door au backend jusqu’à ce que Front Door reçoive le dernier octet de la réponse du backend. |
| BackendHealthPercentage | Pourcentage d’intégrité du backend | Pourcentage | Backend</br>BackendPool | Pourcentage de sondes d’intégrité réussies de Front Door vers les backends. |
| WebApplicationFirewallRequestCount | Nombre de requêtes du pare-feu d’applications web | Nombre | PolicyName</br>RuleName</br>Action | Nombre de requêtes de clients traitées par la sécurité de couche Application de Front Door. |

## <a name="activity-log"></a>Journaux d’activité

Journaux d’activité fournissent des informations sur les opérations effectuées sur le Service de la porte d’entrée. Elles déterminent également quoi, qui et pour toutes les opérations (put, post ou delete) effectuées sur le Service de la porte d’entrée d’écriture.

>[!NOTE]
>Journaux d’activité n’incluent pas les opérations de lecture (get). Ils n’également incluent des opérations que vous effectuez à l’aide du portail Azure ou l’API de gestion d’origine.

Des journaux d’activité d’accès dans votre Service de porte d’entrée ou de tous les journaux de vos ressources Azure dans Azure Monitor. Pour afficher les journaux d’activité :

1. Sélectionnez votre instance de la porte d’entrée.
2. Sélectionnez **journal d’activité**.

    ![Journal d’activité](./media/front-door-diagnostics/activity-log.png)

3. Choisissez une portée de filtrage, puis sélectionnez **appliquer**.

## <a name="diagnostic-logging"></a>Journaux de diagnostic
Journaux de diagnostic offrent des informations détaillées sur les opérations et les erreurs qui sont importantes pour l’audit et la résolution des problèmes. Journaux de diagnostic diffèrent des journaux d’activité.

Journaux d’activité fournissent des informations sur les opérations effectuées sur des ressources Azure. Journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource. Pour plus d’informations, consultez [les journaux de diagnostic Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md).

![Journaux de diagnostic](./media/front-door-diagnostics/diagnostic-log.png)

Pour configurer les journaux de diagnostic pour votre Service de porte d’entrée :

1. Sélectionnez votre service Azure porte d’entrée.

2. Choisissez **les paramètres de Diagnostic**.

3. Sélectionnez **Activer les diagnostics**. Archiver des journaux de diagnostic et les mesures dans un compte de stockage, les transmettre à un concentrateur d’événements ou les envoyer aux journaux d’Azure Monitor.

Porte d’entrée Service fournit actuellement des journaux de diagnostic (par lot toutes les heures). Journaux de diagnostic fournissent des requêtes individuelles d’API avec chaque entrée a le schéma suivant :

| Propriété  | Description |
| ------------- | ------------- |
| ClientIp | Adresse IP du client à l’origine de la demande. |
| ClientPort | Le port IP du client qui a effectué la demande. |
| HttpMethod | Méthode HTTP utilisée par la requête. |
| HttpStatusCode | Le code d’état HTTP retourné par le proxy. |
| HttpStatusDetails | État résultant dans la demande. Vous trouverez la signification de cette valeur de chaîne à une table de référence d’état. |
| HttpVersion | Type de la demande ou de la connexion. |
| RequestBytes | La taille du message de demande HTTP en octets, y compris les en-têtes de demande et le corps de la demande. |
| RequestUri | URI de la requête reçue. |
| ResponseBytes | Octets envoyés par le serveur principal en tant que la réponse.  |
| RoutingRuleName | Le nom de la règle de routage correspondant à la demande. |
| SecurityProtocol | La version du protocole TLS/SSL utilisée par la requête ou null si aucun chiffrement. |
| TimeTaken | La durée pendant laquelle l’action a eu, en millisecondes. |
| UserAgent | Le type de navigateur utilisée par le client |
| TrackingReference | La chaîne de référence unique qui identifie une demande pris en charge par la porte d’entrée, est également envoyé en tant qu’en-tête X-Azure-Ref au client. Requis pour la recherche des détails dans les journaux d’accès pour une demande spécifique. |

## <a name="next-steps"></a>Étapes suivantes

- [Créer un profil de la porte d’entrée](quickstart-create-front-door.md)
- [Fonctionnement de la porte d’entrée](front-door-routing-architecture.md)
