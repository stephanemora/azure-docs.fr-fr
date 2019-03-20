---
title: Azure Front Door Service - Métriques et journalisation | Microsoft Docs
description: Cet article présente les différents journaux d’accès et métriques pris en charge par Azure Front Door Service.
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
ms.openlocfilehash: 5d4d591c465cae91c59e8f86ea9d3e421db0f952
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112107"
---
# <a name="monitoring-metrics-for-front-door"></a>Métriques de supervision pour Front Door

À l’aide de Service de porte d’Azure, vous pouvez surveiller les ressources de plusieurs manières :
* [Journaux](#diagnostic-logging) : les journaux permettent d’enregistrer ou d’utiliser les performances, les accès et les autres données à partir d’une ressource à des fins de supervision.

* [Métriques](#metrics) : Application Gateway a actuellement sept métriques pour afficher les compteurs de performances.

## <a name="metrics"></a>Mesures

Les mesures représentent une fonctionnalité de certaines ressources Azure, vous permettant d’afficher les compteurs de performances dans le portail. Pour Front Door, les métriques suivantes sont disponibles :

| Métrique | Nom d’affichage de la métrique | Unité | Dimensions | Description |
| --- | --- | --- | --- | --- |
| RequestCount | Nombre de requêtes | Nombre | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre de requêtes de clients prises en charge par Front Door  |
| RequestSize | Taille de la requête | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que requêtes de clients à Front Door. |
| ResponseSize | Taille de la réponse | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que réponses de Front Door aux clients. |
| TotalLatency | Latence totale | Millisecondes | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Temps calculé à partir du moment où la requête du client est reçue par Front Door jusqu’à ce que le client accuse réception du dernier octet de la réponse de Front Door. |
| BackendRequestCount | Nombre de requêtes de backend | Nombre | httpStatus</br>HttpStatusGroup</br>Backend | Nombre de requêtes envoyées de Front Door aux backends. |
| BackendRequestLatency | Latence de requête du backend | Millisecondes | Backend | Temps calculé à partir du moment où la requête est envoyée par Front Door au backend jusqu’à ce que Front Door reçoive le dernier octet de la réponse du backend. |
| BackendHealthPercentage | Pourcentage d’intégrité du backend | Pourcentage | Backend</br>BackendPool | Pourcentage de sondes d’intégrité réussies de Front Door vers les backends. |
| WebApplicationFirewallRequestCount | Nombre de requêtes du pare-feu d’applications web | Nombre | PolicyName</br>RuleName</br>Action | Nombre de requêtes de clients traitées par la sécurité de couche Application de Front Door. |

## <a name="activity-log"></a>Journaux d’activité

Journaux d’activité fournissent des informations sur les opérations qui ont été effectuées sur votre porte d’entrée. À l’aide des journaux d’activité, vous pouvez déterminer le « quoi, qui et quand » pour toutes les opérations (PUT, POST, DELETE) effectuées sur votre porte d’entrée d’écriture.

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations de lecture (GET) ou les opérations effectuées dans le portail Azure ou utilisant les API de gestion d’origine.

Vous pouvez accéder aux journaux d’activité dans votre porte d’entrée, ou accéder aux journaux de toutes vos ressources Azure dans Azure Monitor. 

Pour afficher les journaux d’activité :

1. Sélectionnez votre instance de la porte d’entrée.
2. Cliquez sur **Journal d’activité**.

    ![journal d’activité](./media/front-door-diagnostics/activity-log.png)

3. Sélectionnez l’étendue de filtrage souhaitée, puis cliquez sur **Appliquer**.

## <a name="diagnostic-logging"></a>Journaux de diagnostic
Les journaux de diagnostic offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit ainsi qu’à des fins de dépannage. Les journaux de diagnostic diffèrent des journaux d’activité. Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur vos ressources Azure. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource. En savoir plus sur [les journaux de diagnostic Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md). 

Pour configurer les journaux de diagnostic pour votre porte d’entrée :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Paramètres de diagnostic**.

    ![journaux de diagnostic](./media/front-door-diagnostics/diagnostic-log.png)

3. Cliquez sur **Activer les diagnostics**. Vous pouvez archiver les journaux de diagnostic avec les mesures dans un compte de stockage, les transmettre en continu à un hub d’événement ou les envoyer aux journaux Azure Monitor. 

Le Service de porte d’entrée Azure fournit actuellement des diagnostics demander de journaux (par lot toutes les heures) sur les API individuel avec chaque entrée a le schéma suivant :

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

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
