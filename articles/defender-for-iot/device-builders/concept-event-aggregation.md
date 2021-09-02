---
title: Collecte d’événements du micro-agent (préversion)
description: Les agents de sécurité IoT collectent des données et événements système de votre appareil local, puis envoient ces données au cloud Azure à des fins de traitement et d’analyse.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6836c12f625645c5b9e0913f9d2f3f36fc06843e
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122535100"
---
# <a name="micro-agent-event-collection-preview"></a>Collecte d’événements du micro-agent (préversion)

Les agents de sécurité IoT collectent des données et événements système de votre appareil local, puis envoient ces données au cloud Azure à des fins de traitement et d’analyse. Le micro-agent Defender pour IoT collecte de nombreux types d’événements d’appareil, dont des événements liés à de nouveaux processus et connexions. De tels événements peuvent se produire fréquemment sur un appareil en une seconde. Si cette capacité est importante pour assurer une sécurité complète, le nombre de messages que les agents de sécurité envoient peut rapidement atteindre ou dépasser les limites de quotas et de coûts de votre IoT Hub. Dans tous les cas, ces événements contiennent des informations de sécurité très précieuses qui sont essentielles à la protection de votre appareil. 

Afin de réduire les quotas et coûts supplémentaires tout en conservant la protection de vos appareils, les agents Defender pour IoT agrègent les types d’événements suivants : 

- ProcessCreate (Linux uniquement) 

- ConnectionCreate réseau

- Informations système

- Ligne de base

Les collecteurs basés sur les événements sont des collecteurs déclenchés en fonction de l’activité correspondante à partir de l’appareil. Par exemple, un processus a été démarré dans l’appareil.  

Les collecteurs basés sur les déclencheurs sont des collecteurs qui sont déclenchés de manière planifiée en fonction des configurations du client.

## <a name="how-does-event-aggregation-work"></a>Fonctionnement de l’agrégation d’événements 

Les agents Defender pour IoT agrègent les événements pour la fenêtre de temps ou la période de l’intervalle. Une fois la période d’intervalle écoulée, l’agent envoie les événements agrégés au cloud Azure pour une analyse plus poussée. Les événements agrégés sont stockés en mémoire jusqu’à ce qu’ils soient envoyés au cloud Azure. 

L’agent collecte des événements identiques à ceux qui sont déjà stockés en mémoire. Cette collection oblige l’agent à augmenter le nombre d’accès de cet événement spécifique pour réduire l’empreinte mémoire de l’agent. Quand la fenêtre de temps d’agrégation se termine, l’agent envoie le nombre d’accès à chaque type d’événement survenu. L’agrégation d’événements est simplement l’agrégation des nombres d’accès de chaque type collecté d’événement. 

## <a name="process-events-event-based"></a>Événements de processus (basés sur un événement)

Les événements de processus sont pris en charge sur les système d'exploitation Linux. 

Ceux-ci sont considérés comme identiques quand la *ligne de commande* et le  *userid* sont identiques. 

La mémoire tampon par défaut des événements de processus s’élève à 256 processus. Lorsque cette limite est atteinte, la mémoire tampon est recyclée et l’événement le plus ancien est ignoré afin de libérer de l’espace pour le dernier événement traité. Un avertissement pour augmenter la taille du cache est journalisé.

### <a name="data-collection"></a>Collecte de données

Données collectées pour chaque événement :

- **TIMESTAMP** : première fois que le processus a été observé.

- **Process_id** : PID de Linux.

- **Parent_process_id** : PID parent de Linux, s’il existe.

- **Commandline** : la ligne de commande. 

- **Type** : il peut s’agir de `fork` ou `exec`.

- **hit_count** : somme d’agrégat. Nombre d’exécutions du même processus, pendant le même laps de temps, jusqu’à ce que les événements soient envoyés au cloud. 

## <a name="network-connection-events-event-based-collector"></a>Événements de connexion réseau (collecteur basé sur les événements)

Les événements de connexion réseau sont considérés comme identiques lorsque le port local, le port distant, le protocole de transport, l’adresse locale et l’adresse distante sont identiques.

La mémoire tampon par défaut pour les événements de connexion réseau est de 256. Dans les situations où le cache est plein : 

- **Appareils Azure RTOS** : aucun nouvel événement réseau n’est mis en cache jusqu’au cycle de collecte suivant.  

- **Appareils Linux** : l’événement le plus ancien sera remplacé par chaque événement nouveau. Un avertissement pour augmenter la taille du cache est journalisé.

Pour l’appareil Linux, seul IPv4 est pris en charge.

### <a name="data-collection"></a>Collecte de données

Données collectées pour chaque événement :

- **Adresse locale** : adresse source de la connexion.

- **Adresse distante** : adresse de destination de la connexion.

- **Port local** : port source de la connexion.

- **Port distant** : port de destination de la connexion.

- **Bytes_in** :somme des octets RX agrégés de la connexion.

- **Bytes_out** :somme des octets TX agrégés de la connexion.

- **Transport_protocol** : peut être TCP, UDP ou ICMP.

- **Protocole d’application** : protocole d’application associé à la connexion.

- **Propriétés étendues** : détails supplémentaires de la connexion. Par exemple : `host name`. 

## <a name="baseline-trigger-based"></a>Ligne de base (basée sur un déclencheur) 

Le collecteur de ligne de base effectue régulièrement des vérifications de CIS. Seuls les résultats ayant échoué sont envoyés vers le cloud. Le cloud agrège les résultats et fournit des recommandations. 

### <a name="data-collection"></a>Collecte de données

Données collectées pour chaque événement :

- **ID de contrôle** : en format CIS, CIS-debian-9-Filesystem-1.1.2.

- **Résultat du contrôle** : peut être `Error` ou `Fail`. Par exemple, `Error` dans une situation où le contrôle ne peut pas être effectué.

- **Erreur** : information et description de l’erreur.

- **Description** : description du contrôle depuis CIS.

- **Correction** : recommandation pour la correction depuis CIS.

## <a name="next-steps"></a>Étapes suivantes

Examinez vos [Alertes de sécurité IoT](concept-security-alerts.md).
