---
title: Configurations du micro-agent (préversion)
description: Le collecteur envoie toutes les données actuelles immédiatement après une modification de la configuration. Les modifications sont alors appliquées.
ms.date: 10/04/2021
ms.topic: conceptual
ms.openlocfilehash: 43a26a73f97c9d424998a4ff35d1304c0baf6fc0
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428442"
---
# <a name="micro-agent-configurations-preview"></a>Configurations du micro-agent (préversion)

Cet article décrit les différents types de configurations prises en charge par le micro-agent. Les clients peuvent configurer le micro-agent en fonction des besoins de leurs appareils et des environnements réseau.  

Le comportement du micro-agent est configuré par un ensemble de propriétés du jumeau de module. Vous pouvez configurer le micro-agent en fonction de vos besoins. Par exemple, vous pouvez exclure des événements automatiquement, diminuer la consommation d’énergie et réduire la bande passante réseau.

Après toute modification de la configuration, le collecteur envoie immédiatement toutes les données d’événement non envoyées. Une fois les données envoyées, les modifications sont appliquées et tous les collecteurs redémarrent.

> [!Note]
> Les paramètres du mode Agrégation sont pris en charge, mais ils ne sont pas configurables.

## <a name="event-based-collectors-configurations"></a>Configurations des collecteurs basées sur les événements

Ces configurations incluent les collecteurs d’activité réseau et de processus.

| Nom du paramètre | Option de paramétrage | Description | Paramètre par défaut |
|--|--|--|--|
| **Intervalle** | Élevé, Moyen ou Faible | Définir la fréquence d’envoi. | Moyenne |
| **Mode d’agrégation** | True ou False | Indique s’il faut traiter l’agrégation d’événements pour un événement identique.  | True |
| **Taille du cache** | Cycle FIFO | Nombre d’événements collectés entre les envois de données. | 256 |
| **Désactiver le collecteur** | True ou False | Indique si le collecteur est opérationnel. | Faux |

## <a name="trigger-based-collectors-configurations"></a>Configurations des collecteurs basées sur un déclencheur

Ces configurations incluent les informations système et les collecteurs de ligne de base.

| Nom du paramètre | Option de paramétrage | Description | Paramètre par défaut |
|--|--|--|--|
| **Intervalle** | Élevé, Moyen ou Faible | Fréquence d’envoi des données. | Faible |
| **Désactiver le collecteur** | True ou False | Indique si le collecteur est opérationnel. | Faux |

## <a name="general-configuration"></a>Configuration générale

Définissez la fréquence à laquelle les messages sont envoyés pour chaque niveau de priorité. Les valeurs par défaut sont indiquées ci-dessous :

| Fréquence | Période (en minutes) |
|--|--|
| Faible | 1440 (24 heures) |
| Moyenne | 120 (2 heures) |
| Élevé | 30 (0,5 heure) |

Pour réduire le nombre de messages envoyés au cloud, chaque priorité doit être définie en tant que multiple de celle qui se trouve au-dessous. Par exemple, Élevé : 60 minutes, Moyen : 120 minutes, Faible : 480 minutes.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Collecte d’événements du micro-agent (préversion)](concept-event-aggregation.md).
