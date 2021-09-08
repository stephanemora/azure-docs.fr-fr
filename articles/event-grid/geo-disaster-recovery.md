---
title: Géorécupération d’urgence dans Azure Event Grid | Microsoft Docs
description: Décrit comment Azure Event Grid prend en charge la géorécupération d’urgence (GeoDR) automatiquement.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 47899ba8fe4dd114d1fc3c0fb729589abf3ebd92
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829972"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Géorécupération d’urgence côté serveur dans Azure Event Grid
Event Grid prend en charge la géo-reprise d'activité après sinistre automatique des métadonnées pour les rubriques, les domaines et les abonnements aux événements. Event Grid synchronise automatiquement votre infrastructure liée aux événements avec une région jumelée. Si une région Azure entière tombe en panne, les événements commencent à circuler vers la région géo-jumelée sans aucune intervention de votre part. 

Notez que les données d’événement ne sont pas répliquées dans la région jumelée. Seules les métadonnées sont répliquées. Si une région prend en charge les zones de disponibilité, les données d’événement sont répliquées entre les zones de disponibilité. 

La récupération d’urgence est mesurée à l’aide de deux métriques :

- Objectif de point de récupération (RPO) : minutes ou heures de données pouvant être perdues.
- Objectif de temps de récupération (RTO) : nombre de minutes ou d’heures pendant lesquelles le service peut être arrêté.

Le basculement automatique d’Event Grid utilise différents objectifs RPO et RTO pour vos métadonnées (rubriques, domaines, abonnements aux événements) et vos données (événements). Si vous avez besoin d’une spécification différente de celles ci-dessous, vous pouvez toujours implémenter votre propre [basculement côté client à l’aide des API d’intégrité de rubrique](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Objectif de point de récupération (RPO)
- **RPO de métadonnées** : zéro minutes. Chaque fois qu’une ressource est créée dans Event Grid, elle est répliquée instantanément dans toutes les régions. Lorsqu’un basculement se produit, aucune métadonnée n’est perdue.
- **RPO de données** : si votre système est sain et a rattrapé le trafic existant au moment du basculement régional, le RPO pour les événements est d’environ 5 minutes.

## <a name="recovery-time-objective-rto"></a>Objectif de délai de récupération (RTO)
- **RTO de métadonnées** : bien que cela se produise généralement beaucoup plus rapidement, dans les 60 minutes qui suivent, Event Grid commence à accepter les appels à créer/mettre à jour/supprimer des rubriques et des abonnements.
- **RTO de données** : comme les métadonnées, cela se produit généralement beaucoup plus rapidement. Toutefois, Event Grid commence à accepter le nouveau trafic dans les 60 minutes suivant un basculement régional.

> [!IMPORTANT]
> - Il n’existe aucun contrat de niveau de service (SLA) pour la récupération d’urgence côté serveur. Si la région jumelée n’a pas de capacité supplémentaire pour traiter le trafic en plus, Event Grid ne peut pas initier le basculement. Les objectifs de niveau de service correspondent aux meilleurs efforts uniquement. 
> - Le coût de GeoDR métadonnées sur Event Grid est de 0 $.


## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez implémenter votre propre logique de basculement côté client, consultez [Créer votre propre récupération d’urgence pour les rubriques personnalisées dans Event Grid](custom-disaster-recovery.md)
