---
title: Supervision de la sonde d’intégrité Azure Front Door Standard/Premium (préversion)
description: Cet article explique comment Azure Front Door supervise l’intégrité de votre back-end.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097806"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Supervision de la sonde d’intégrité Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door envoie périodiquement une requête HTTP ou HTTPS à chacun de vos back-ends. Ces requêtes permettent à Azure Front Door de déterminer l’intégrité de chaque point de terminaison dans le pool de back-ends. Front Door utilise alors les réponses fournies par la sonde pour identifier les « meilleurs » backends vers lesquels acheminer les demandes de vos clients. 

> [!WARNING]
> Dans la mesure où le service Front Door a de nombreux environnements de périphérie, le volume de sonde d’intégrité adressées à vos serveurs principaux peut être conséquent, allant de 25 à 1 200 requêtes par minute, en fonction de la fréquence configurée de la sonde d’intégrité. À la fréquence de sonde par défaut de 30 secondes, le volume des requêtes de sonde sur votre serveur principal doit être d’environ 200 requêtes par minute.

## <a name="supported-protocols"></a>Protocoles pris en charge

La porte d’entrée prend en charge l’envoi de sondes via les protocoles HTTP ou HTTPS. Ces sondes sont envoyées sur les mêmes ports TCP configurés pour le routage des demandes des clients et il n’est pas possible de les remplacer.

## <a name="supported-http-methods-for-health-probes"></a>Méthodes HTTP prises en charge pour les sondes d’intégrité

Le service Front Door prend en charge les méthodes HTTP suivantes pour l’envoi des requêtes de sonde d’intégrité :

* **GET :** La méthode GET consiste à récupérer toutes les informations (sous forme d’entités) identifiées par l’URI de requête.
* **HEAD :** La méthode HEAD est identique à la méthode GET, sauf que le serveur NE DOIT PAS retourner de corps de message dans la réponse. Pour les nouveaux profils Front Door, par défaut, la méthode de sonde est définie sur HEAD.

> [!NOTE]
> Afin de limiter la charge et les coûts qui pèsent sur vos serveurs principaux, le service Front Door recommande d’utiliser des requêtes HEAD pour les sondes d’intégrité.

## <a name="health-probe-responses"></a>Réponses des sondes d’intégrité

| Réponses  | Description | 
| ------------- | ------------- |
| Détermination de l’intégrité  |  Le code d’état 200 OK indique que le backend est sain. Tous les autres sont considérés comme un défaillance. Si, pour une raison quelconque (notamment une défaillance réseau), la réponse HTTP valide d’une sonde n’est pas reçue, la sonde est considérée comme défectueuse.|
| Mesure de la latence  | La latence est le temps horloge mesuré entre le moment qui précède immédiatement l’envoi de la demande d’analyse et le moment où le dernier octet de la réponse est reçu. Comme nous utilisons une nouvelle connexion TCP à chaque demande, cette mesure n’est pas orientée vers les backends ayant des connexions chaudes.  |

## <a name="how-front-door-determines-backend-health"></a>Comment une porte d’entrée détermine l’intégrité des backends

Pour déterminer l’intégrité, le service Azure Front Door utilise le même processus en trois étapes ci-dessous pour tous les algorithmes.

1. Excluez les backends désactivés.

1. Excluez les backends qui présentent des erreurs de sonde d’intégrité :

    * Pour effectuer cette sélection, examinez les _n_ dernières réponses de sonde d’intégrité. Si au moins _x_ sont saines, le backend est considéré comme sain.

    * Pour configurer la valeur _n_, modifiez la propriété SampleSize dans les paramètres d’équilibrage de charge.

    * Pour configurer la valeur _x_, modifiez la propriété SuccessfulSamplesRequired dans les paramètres d’équilibrage de charge.

1. Front Door mesure et maintient la latence (durée aller-retour) pour chaque ensemble de backends considérés comme sains.


## <a name="complete-health-probe-failure"></a>Mettre fin à l’échec des sondes d’intégrité

Si les sondes d’intégrité échouent pour chaque backend contenu dans un pool de backends, la porte d’entrée considère que tous les backends sont sains et leur achemine à tous le trafic sous la forme d’une distribution en tourniquet (round robin).

Dès qu’un serveur principal retrouve un état sain, le service Front Door reprend l’algorithme d’équilibrage de charge normal.

## <a name="disabling-health-probes"></a>Désactivation des sondes d’intégrité

Si vous avez un seul back-end dans votre pool de back-ends ou si un seul back-end est actif dans un pool de back-ends, vous pouvez choisir de désactiver les sondes d’intégrité. En faisant cela, vous réduisez la charge sur votre back-end d’application.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
