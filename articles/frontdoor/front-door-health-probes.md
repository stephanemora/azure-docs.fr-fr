---
title: Azure Front Door Service - Surveillance de l’intégrité des backends | Microsoft Docs
description: Cet article vous aide à comprendre comment Azure Front Door Service supervise l’intégrité de vos backends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 256d530590fadc9e2aeb1ea1efb7a52608014978
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988561"
---
# <a name="health-probes"></a>Sondes d’intégrité

Pour déterminer l’intégrité de chaque backend, chaque environnement de porte d’entrée envoie régulièrement une requête HTTP/HTTPS synthétique à chacun de vos backends configurés. Sur la base des réponses fournies par les sondes, la porte d’entrée identifie les « meilleurs » backends vers lesquels elle doit acheminer les demandes réelles des clients.


## <a name="supported-protocols"></a>Protocoles pris en charge

La porte d’entrée prend en charge l’envoi de sondes via les protocoles HTTP ou HTTPS. Ces sondes sont envoyées sur les mêmes ports TCP configurés pour le routage des demandes des clients et il n’est pas possible de les remplacer.

## <a name="health-probe-responses"></a>Réponses des sondes d’intégrité

| Réponses  | Description | 
| ------------- | ------------- |
| Détermination de l’intégrité  |  Le code d’état 200 OK indique que le backend est sain. Tous les autres sont considérés comme un défaillance. Si, pour une raison quelconque (notamment une défaillance réseau), la réponse HTTP valide d’une sonde n’est pas reçue, la sonde est considérée comme défectueuse.|
| Mesure de la latence  | La latence est le temps horloge mesuré entre le moment qui précède immédiatement l’envoi de la demande d’analyse et le moment où le dernier octet de la réponse est reçu. Comme nous utilisons une nouvelle connexion TCP à chaque demande, cette mesure n’est pas orientée vers les backends ayant des connexions chaudes.  |

## <a name="how-front-door-determines-backend-health"></a>Comment une porte d’entrée détermine l’intégrité des backends

Pour déterminer l’intégrité, Azure Front Door Service utilise le même processus en trois étapes ci-dessous pour tous les algorithmes.

1. Excluez les backends désactivés.

2. Excluez les backends qui présentent des erreurs de sonde d’intégrité :
    * Pour effectuer cette sélection, examinez les _n_ dernières réponses de sonde d’intégrité. Si au moins _x_ sont saines, le backend est considéré comme sain.

    * Vous pouvez configurer le paramètre _n_ en modifiant la propriété SampleSize dans les paramètres d’équilibrage de charge.

    * Pour configurer le paramètre _x_, modifiez la propriété SuccessfulSamplesRequired dans les paramètres d’équilibrage de charge.

3. Par ailleurs, la porte d’entrée mesure et maintient la latence (durée aller-retour) pour chaque backend considéré comme sain.


## <a name="complete-health-probe-failure"></a>Mettre fin à l’échec des sondes d’intégrité

Si les sondes d’intégrité échouent pour chaque backend contenu dans un pool de backends, la porte d’entrée considère que tous les backends sont sains et leur achemine à tous le trafic sous la forme d’une distribution en tourniquet (round robin).

Dès lors qu’un backend retrouve un état sain, la porte d’entrée reprend l’algorithme d’équilibrage de charge normal.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne une porte d’entrée](front-door-routing-architecture.md).
