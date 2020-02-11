---
title: Mode de création des recommandations de réservation Azure
description: Découvrez comment les recommandations de réservation Azure sont créées pour les machines virtuelles.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991046"
---
# <a name="how-reservation-recommendations-are-created"></a>Mode de création des recommandations de réservation
Les recommandations d’achat d’instances réservées Azure sont fournies via l’[API de recommandation de réservation](/rest/api/consumption/reservationrecommendations) d’Azure Consumption, via [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) et via de l’expérience d’achat de réservation dans le portail Azure.

Les étapes suivantes définissent comment les recommandations sont calculées : 
1. Le moteur de recommandation évalue l’utilisation horaire de vos ressources dans l’étendue donnée au cours des 7, 30 et 60 derniers jours.
2. En fonction des données d’utilisation, le moteur simule vos coûts avec et sans réservations.
3. Les coûts sont simulés pour différentes quantités, et la quantité qui optimise les économies est recommandée.
4. Si vos ressources sont arrêtées régulièrement, la simulation ne trouve aucune économie et aucune recommandation d’achat n’est fournie.

## <a name="recommendations-in-azure-advisor"></a>Recommandations dans Azure Advisor
Les recommandations d’achat de réservation pour les machines virtuelles sont disponibles dans Azure Advisor. Gardez à l’esprit les points suivants : 
- Advisor produit des recommandations dont l’étendue est limitée à un seul abonnement.
- Les recommandations calculées avec une période de recherche de 30 jours sont disponibles dans Advisor.
- Si vous achetez une réservation d’étendue partagée, les recommandations d’achat de réservations d’Advisor peuvent prendre jusqu’à 30 jours pour disparaître.

## <a name="other-expected-api-behavior"></a>Autre comportement attendu de l’API
- Si vous utilisez une période de recherche de 7 jours, vous risquez de ne pas obtenir de recommandations quand les machines virtuelles sont arrêtées pendant plus d’une journée.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez [comment la remise de réservation Azure est appliquée aux machines virtuelles](../manage/understand-vm-reservation-charges.md).
