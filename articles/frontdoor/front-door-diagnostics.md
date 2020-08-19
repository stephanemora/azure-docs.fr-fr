---
title: Supervision des journaux et des métriques dans Azure Front Door | Microsoft Docs
description: Cet article décrit les différents journaux d’accès et métriques pris en charge par Azure Front Door.
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
ms.openlocfilehash: 249b2406f048709fd7e4f76f8272b3158708e5bb
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056429"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Supervision des journaux et des métriques dans Azure Front Door

Azure Front Door vous permet de superviser les ressources des manières suivantes :

- **Métriques**. Azure Front Door dispose actuellement de sept métriques pour afficher les compteurs de performances.
- **Journaux**. Les journaux d’activité et de diagnostic permettent d’enregistrer ou de consommer les données de performances, d’accès et autres provenant d’une ressource à des fins de supervision.

### <a name="metrics"></a>Mesures

Les métriques représentent une fonctionnalité de certaines ressources Azure qui vous permettent de voir les compteurs de performances dans le portail. Les métriques Front Door disponibles sont les suivantes :

| Métrique | Nom d’affichage de la métrique | Unité | Dimensions | Description |
| --- | --- | --- | --- | --- |
| RequestCount | Nombre de requêtes | Count | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre de requêtes de clients prises en charge par Front Door  |
| RequestSize | Taille de la requête | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que requêtes de clients à Front Door. |
| ResponseSize | Taille de la réponse | Octets | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Nombre d’octets envoyés en tant que réponses de Front Door aux clients. |
| TotalLatency | Latence totale | Millisecondes | httpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Temps calculé à partir du moment où la requête du client est reçue par Front Door jusqu’à ce que le client accuse réception du dernier octet de la réponse de Front Door. |
| BackendRequestCount | Nombre de requêtes de backend | Count | httpStatus</br>HttpStatusGroup</br>Backend | Nombre de requêtes envoyées de Front Door aux backends. |
| BackendRequestLatency | Latence de requête du backend | Millisecondes | Backend | Temps calculé à partir du moment où la requête est envoyée par Front Door au backend jusqu’à ce que Front Door reçoive le dernier octet de la réponse du backend. |
| BackendHealthPercentage | Pourcentage d’intégrité du backend | Pourcentage | Backend</br>BackendPool | Pourcentage de sondes d’intégrité réussies de Front Door vers les backends. |
| WebApplicationFirewallRequestCount | Nombre de requêtes du pare-feu d’applications web | Count | PolicyName</br>RuleName</br>Action | Nombre de requêtes de clients traitées par la sécurité de couche Application de Front Door. |

## <a name="activity-logs"></a><a name="activity-log"></a>Journaux d’activité

Les journaux d’activité fournissent des informations sur les opérations effectuées sur Front Door. Ils répondent également aux questions quoi, qui et quand pour toutes les opérations d’écriture (put, post ou delete) exécutées sur Front Door.

>[!NOTE]
>Les journaux d’activité n’incluent pas les opérations de lecture (get). Ils n’incluent pas non plus les opérations que vous effectuez à l’aide du portail Azure ou de l’API Gestion d’origine.

Vous pouvez accéder aux journaux d’activité de votre service Front Door ou à tous les journaux de vos ressources Azure dans Azure Monitor. Pour afficher les journaux d’activité :

1. Sélectionnez votre instance de Front Door.
2. Sélectionnez **Journal d’activité**.

    ![Journal d’activité](./media/front-door-diagnostics/activity-log.png)

3. Choisissez une étendue de filtrage, puis sélectionnez **Appliquer**.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Journaux de diagnostic
Les journaux de diagnostic offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit et le dépannage. Les journaux de diagnostic diffèrent des journaux d’activité.

Les journaux d’activité fournissent des informations détaillées sur les opérations effectuées sur des ressources Azure. Les journaux de diagnostic fournissent des informations détaillées sur les opérations effectuées par votre ressource. Pour plus d’informations, consultez [Journaux de diagnostic Azure Monitor](../azure-monitor/platform/platform-logs-overview.md).

![Journaux de diagnostic](./media/front-door-diagnostics/diagnostic-log.png)

Pour configurer les journaux de diagnostic pour votre service Front Door :

1. Sélectionnez votre service Azure Front Door.

2. Choisissez **Paramètres de diagnostic**.

3. Sélectionnez **Activer les diagnostics**. Archivez les journaux de diagnostic et les métriques dans un compte de stockage, envoyez-les en streaming à un hub d’événements ou envoyez-les aux journaux Azure Monitor.

Front Door fournit actuellement des journaux de diagnostic (par lot toutes les heures). Les journaux de diagnostic présentent chaque requête d’API individuellement, chaque entrée ayant le schéma suivant :

| Propriété  | Description |
| ------------- | ------------- |
| BackendHostname | Si la demande a été transférée à un serveur principal, ce champ représente le nom d’hôte du serveur principal. Ce champ est vide si la requête a été redirigée ou transférée vers un cache régional (lorsque la mise en cache est activée pour la règle d’acheminement). |
| CacheStatus | Pour les scénarios de mise en cache, ce champ définit une absence/une correspondance dans le cache au niveau POP |
| ClientIp | Adresse IP du client à l’origine de la demande. S’il existait un en-tête X-Forwarded-For dans la demande, l’adresse IP du client est sélectionnée de la même façon. |
| ClientPort | Adresse IP du port du client qui a effectué la requête. |
| HttpMethod | Méthode HTTP utilisée par la requête. |
| HttpStatusCode | Code d’état HTTP retourné par le proxy. |
| HttpStatusDetails | État résultant de la requête. Vous trouverez la signification de cette valeur de chaîne dans la Table de référence des états. |
| HttpVersion | Type de la requête ou de la connexion. |
| POP | Nom abrégé de la périphérie où la demande est arrivée. |
| RequestBytes | Taille du message de requête HTTP en octets, en-têtes de requête et corps de requête compris. |
| RequestUri | URI de la requête reçue. |
| ResponseBytes | Octets envoyés en tant que réponse par le serveur back-end.  |
| RoutingRuleName | Nom de la règle de routage correspondant à la requête. |
| RulesEngineMatchNames | Noms des règles correspondant à la demande. |
| SecurityProtocol | Version du protocole TLS/SSL utilisée par la requête, ou Null si aucun chiffrement. |
| SentToOriginShield | Champ booléen indiquant s’il y a eu une absence dans le cache sur le premier environnement et que la demande a été envoyée au cache régional. Ignorez ce champ si la règle d’acheminement est une redirection ou si la mise en cache n’est pas activée. |
| TimeTaken | Durée, en secondes, écoulée entre le premier octet de la requête Front Door et le dernier octet de la réponse. |
| TrackingReference | Chaîne de référence unique qui identifie une requête traitée par Front Door, également envoyée en tant qu’en-tête X-Azure-Ref au client. Nécessaire pour pouvoir effectuer une recherche détaillée dans les journaux d’accès pour une requête spécifique. |
| UserAgent | Type de navigateur utilisé par le client. |

**Remarque :** Pour les différentes configurations de routage et les différents comportements de trafic, certains des champs, comme backendHostname, cacheStatus, sentToOriginShield et POP peuvent renvoyer des valeurs différentes. Le tableau ci-dessous explique les différentes valeurs que contiennent ces champs dans différents scénarios :

| Scénarios | Nombre d’entrées de journal | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Règle d’acheminement sans mise en cache | 1 | Code POP Edge | Serveur principal où la demande a été envoyée | False | CONFIG_NOCACHE |
| Règle d’acheminement avec mise en cache. Correspondance dans le cache au niveau de la périphérie POP | 1 | Code POP Edge | Vide | False | HIT |
| Règle d’acheminement avec mise en cache. Absence dans le cache au niveau de la périphérie POP, mais correspondance dans cache au niveau POP du cache parent | 2 | 1. Code POP Edge</br>2. Code POP du cache parent | 1. Nom d’hôte POP du cache parent</br>2. Vide | 1. True</br>2. False | 1. MISS</br>2. PARTIAL_HIT |
| Règle d’acheminement avec mise en cache. Absence dans le cache à la fois dans la périphérie et dans le POP du cache parent | 2 | 1. Code POP Edge</br>2. Code POP du cache parent | 1. Nom d’hôte POP du cache parent</br>2. Serveur principal qui permet de remplir le cache | 1. True</br>2. False | 1. MISS</br>2. MISS |


## <a name="next-steps"></a>Étapes suivantes

- [Créer un profil Front Door](quickstart-create-front-door.md)
- [Fonctionnement de Front Door](front-door-routing-architecture.md)
