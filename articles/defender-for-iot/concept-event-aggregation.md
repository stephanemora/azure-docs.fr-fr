---
title: Agrégation d’événements (préversion)
description: Les agents de sécurité IoT collectent des données et événements système de votre appareil local, puis envoient ces données au cloud Azure à des fins de traitement et d’analyse.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779322"
---
# <a name="event-aggregation-preview"></a>Agrégation d’événements (préversion)

Les agents de sécurité IoT collectent des données et événements système de votre appareil local, puis envoient ces données au cloud Azure à des fins de traitement et d’analyse. Le micro-agent Defender pour IoT collecte de nombreux types d’événements d’appareil, dont des événements liés à de nouveaux processus et connexions. De tels événements peuvent se produire fréquemment sur un appareil en une seconde. Si cette capacité est importante pour assurer une sécurité complète, le nombre de messages que les agents de sécurité envoient peut rapidement atteindre ou dépasser les limites de quotas et de coûts de votre IoT Hub. Toutefois, ces événements contiennent des informations de sécurité très précieuses, essentielles pour la protection de votre appareil. 

Afin de réduire les quotas et coûts supplémentaires tout en conservant la protection de vos appareils, les agents Defender pour IoT agrègent les types d’événements suivants : 

- ProcessCreate (Linux uniquement) 

- ConnectionCreate (Azure RTOS uniquement) 

## <a name="how-does-event-aggregation-work"></a>Fonctionnement de l’agrégation d’événements 

Les agents Defender pour IoT agrègent les événements pour la fenêtre de temps ou la période de l’intervalle. Une fois la période d’intervalle écoulée, l’agent envoie les événements agrégés au cloud Azure pour une analyse plus poussée. Les événements agrégés sont stockés en mémoire jusqu’à ce qu’ils soient envoyés au cloud Azure. 

Quand l’agent collecte un événement identique à un autre déjà conservé en mémoire, l’agent augmente le nombre d’accès à cet événement afin de réduire l’empreinte mémoire de l’agent. Quand la fenêtre de temps d’agrégation se termine, l’agent envoie le nombre d’accès à chaque type d’événement survenu. L’agrégation d’événements est simplement l’agrégation des nombres d’accès de chaque type collecté d’événement. 

## <a name="process-events"></a>Traiter des événements 

Actuellement, les systèmes d’exploitation Linux ne prennent pas en charge les événements de processus. 

Ceux-ci sont considérés comme identiques quand la *ligne de commande* et le  *userid* sont identiques. 

La mémoire tampon par défaut pour les événements de processus est dimensionnée pour 32 processus, après quoi elle opère par cycles successifs, les événements de processus les plus anciens étant écartés afin de libérer de l’espace pour les nouveaux.  

## <a name="network-connection-events"></a>Événements de connexion réseau 

Les événements de connexion réseau ne sont actuellement pris en charge que sur Azure RTOS. 

Les événements de connexion réseau sont considérés comme identiques lorsque le *port local*, le  *port distant*, le  *protocole de transport*, l’ *adresse locale* et l’ *adresse distante* sont identiques. 

La mémoire tampon par défaut pour les événements de connexion réseau est de 64. Aucun nouvel événement réseau n’est mis en cache jusqu’au cycle de collecte suivant. Un avertissement pour augmenter la taille du cache est journalisé.

## <a name="next-steps"></a>Étapes suivantes

Examinez vos [Alertes de sécurité IoT](concept-security-alerts.md).
