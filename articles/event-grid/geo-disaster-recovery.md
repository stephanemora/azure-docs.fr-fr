---
title: Géorécupération d’urgence dans Azure Event Grid | Microsoft Docs
description: Décrit comment Azure Event Grid prend en charge la récupération d’urgence géographique (GeoDR) automatiquement.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307316"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Côté serveur géorécupération d’urgence dans Azure Event Grid
Event Grid a maintenant une récupération d’urgence géographique automatique (GeoDR) de métadonnées non seulement pour les nouveaux, mais tous les domaines existants, rubriques et abonnements aux événements. Si une région Azure entière tombe en panne, Event Grid aura déjà tous vos métadonnées infrastructure liées aux événements synchronisées vers une région jumelée. Les nouveaux événements commence à circuler à nouveau sans intervention par vous. 

Récupération d’urgence est mesurée avec deux mesures :

- [Objectif de Point de récupération (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): minutes ou des heures de données qui peuvent être perdues.
- [Temps de récupération (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): les minutes d’heures, le service peut être bas.

Basculement automatique de la grille d’événement a différents objectifs RPO et RTO pour vos métadonnées (abonnements aux événements etc.) et données (événements). Si vous avez besoin d’une spécification différentes de celles qui suivent, vous pouvez toujours implémenter votre propre [côté client basculer à l’aide de la rubrique API de contrôle d’intégrité](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Objectif de point de récupération (RPO)
- **Metadata RPO**: zéro minute. Chaque fois qu’une ressource est créée dans Event Grid, il est instantanément répliquée entre régions. Lorsqu’un basculement se produit, aucune métadonnée n’est perdue.
- **Données RPO**: Si votre système est sain et rattrapé sur le trafic existant au moment du basculement régional, le RPO pour les événements est d’environ 5 minutes.

## <a name="recovery-time-objective-rto"></a>Objectif de délai de récupération (RTO)
- **Métadonnées RTO**: Bien que généralement véridique beaucoup plus rapidement, dans les 60 minutes, Event Grid commence à accepter des appels de création/mise à jour/suppression de rubriques et abonnements.
- **Données RTO**: À l’instar des métadonnées, il est généralement dû beaucoup plus rapidement, toutefois dans les 60 minutes, Event Grid commence à accepter le trafic de nouveau après un basculement régional.

> [!NOTE]
> Le coût de métadonnées GeoDR sur Event Grid est : 0 $.


## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez vous implémenter une logique propre basculement côté client, consultez [# générer vos propres récupération d’urgence pour les rubriques personnalisées dans Event Grid](custom-disaster-recovery.md)
