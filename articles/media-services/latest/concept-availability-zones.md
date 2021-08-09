---
title: Zones de disponibilité
description: Media Services prend en charge des zones de disponibilité qui permettent d’isoler les pannes
services: media-services
author: johndeu
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: johndeu
ms.openlocfilehash: d50af7acadb17fe060ede90ad93896e465d3bad6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746948"
---
# <a name="availability-zones"></a>Zones de disponibilité

Azure Media Services utilise des [zones de disponibilité](../../availability-zones/az-overview.md), qui fournissent des endroits où isoler les pannes au sein d’une même région Azure. Media Services est redondant interzone par défaut dans les [régions disponibles](../../availability-zones/az-region.md#azure-regions-with-availability-zones), et aucune configuration supplémentaire sur le compte n’est nécessaire pour activer cette fonctionnalité.  Media Services stocke les données multimédias dans le ou les comptes de stockage associés.  Ces comptes de stockage doivent être créés en tant que stockage redondant interzone (ZRS) ou stockage géo-redondant interzone (GZRS) pour offrir le même niveau de redondance que le compte Media Services. Pour plus d’informations se rapportant à la configuration de la réplication sur le ou les comptes de stockage associés, consultez l’article [Modifier la manière dont un compte de stockage est répliqué](../../storage/common/redundancy-migration.md).

## <a name="how-media-services-components-handle-an-availability-zone-fault"></a>Comment les composants Media Services gèrent une panne de zone de disponibilité

| Composant             | Comportement en cas de panne de zone de disponibilité |
|-----------            |----------------------|
| Plan de contrôle (Gestion des ressources Azure) | Continue de fonctionner normalement |
| Remise de clés            | Continue de fonctionner normalement |
| travaux                    | Les travaux sont replanifiés dans une autre zone de disponibilité. Un délai s’ajoutera à la durée du traitement, car les travaux en cours de traitement sont replanifiés pour recommencer dans la zone de disponibilité. |
| Événements en direct             | Le streaming et l’ingestion de l’événement en direct continuent de fonctionner normalement. L’appel à « réinitialisation » sur un événement en direct n’est pas pris en charge lors d’une défaillance de zone de disponibilité. Il est recommandé en premier lieu d’arrêter et de redémarrer l’événement en direct plutôt que d’opérer une « réinitialisation ». Si un événement en direct était en passe d’atteindre l’état « En cours d’exécution » au moment d’une panne de zone, les clients peuvent voir l’événement en direct bloqué à l’état de « démarrage ». Dans ce cas, il est recommandé de démarrer un nouvel événement en direct, et de nettoyer les événements « en cours de démarrage » après la récupération de la zone.  |
| Points de terminaison de diffusion en continu     | Continue de fonctionner normalement |


## <a name="high-availability-streaming-and-encoding-for-vod"></a>Streaming et encodage de haute disponibilité pour VOD

Les zones de disponibilité augmentent l’isolement des pannes dans une seule région. Pour assurer une haute disponibilité en streaming et encodage à la demande, vous pouvez utiliser d’autres services Azure et créer une architecture qui répond à certains problèmes, tels que la redondance, la supervision de l’intégrité, l’équilibrage de charge, mais aussi la sauvegarde et la récupération des données. Une telle architecture est fournie dans l’article [Haute disponibilité avec la vidéo à la demande Media Services](architecture-high-availability-encoding-concept.md).
L’article et l’exemple de code fournissent une solution par rapport à la façon dont les comptes Media Services régionaux individuels peuvent être utilisés pour créer une architecture de haute disponibilité destinée à votre application VOD.

## <a name="media-services-support-for-availability-zones-by-region"></a>Prise en charge Media Services des zones de disponibilité par région

Les zones de disponibilité sont actuellement prises en charge dans certaines régions Azure uniquement. Pour en savoir plus sur la prise en charge des régions avec zones de disponibilité, consultez [Régions Azure avec Zones de disponibilité](../../availability-zones/az-region.md#azure-regions-with-availability-zones)

## <a name="further-reading"></a>Lectures supplémentaires

Pour plus d’informations sur les zones de disponibilité, consultez [Régions et zones de disponibilité dans Azure](../../availability-zones/az-overview.md).

Pour en savoir plus sur l’encodage et le streaming de haute disponibilité, consultez [Haute disponibilité avec la vidéo à la demande Media Services](architecture-high-availability-encoding-concept.md).

Pour apprendre à configurer correctement la réplication du compte de stockage afin de prendre en charge les zones de disponibilité, consultez [Modifier la manière dont un compte de stockage est répliqué](../../storage/common/redundancy-migration.md).
