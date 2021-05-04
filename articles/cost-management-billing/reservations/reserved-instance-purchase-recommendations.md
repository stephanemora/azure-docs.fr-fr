---
title: Recommandations de réservation Azure
description: Apprenez-en davantage sur les recommandations de réservation Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767922"
---
# <a name="reservation-recommendations"></a>Reservation recommendations

Les recommandations d’achat d’instances réservées Azure sont fournies via l’[API de recommandation de réservation](/rest/api/consumption/reservationrecommendations) d’Azure Consumption, via [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) et via de l’expérience d’achat de réservation dans le portail Azure.

Les étapes suivantes définissent comment les recommandations sont calculées :

1. Le moteur de recommandation évalue l’utilisation horaire de vos ressources dans l’étendue donnée au cours des 7, 30 et 60 derniers jours.
2. En fonction des données d’utilisation, le moteur simule vos coûts avec et sans réservations.
3. Les coûts sont simulés pour différentes quantités, et la quantité qui optimise les économies est recommandée.
4. Si vos ressources sont arrêtées régulièrement, la simulation ne trouve aucune économie et aucune recommandation d’achat n’est fournie.
5. Les calculs de recommandation tiennent compte des remises spéciales qui peuvent s’appliquer à vos tarifs d’utilisation à la demande.

## <a name="recommendations-in-the-azure-portal"></a>Recommandations dans le portail Azure

Les recommandations d’achat de réservation sont également indiquées dans le portail Azure dans l’expérience d’achat. Les recommandations sont indiquées avec la **quantité recommandée**. Une fois achetée, la quantité recommandée par Azure vous permettra d’économiser au maximum. Bien que vous puissiez acheter la quantité de votre choix, si vous achetez une quantité différente, vos économies ne seront pas optimales.

Prenons quelques exemples qui expliquent pourquoi.

Dans l’exemple d’image suivant pour la recommandation sélectionnée, Azure recommande une quantité d’achat de 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Exemple présentant une recommandation d’achat de réservation" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Des informations supplémentaires sur la recommandation s’affichent quand vous sélectionnez **Afficher les détails**. L’image suivante montre des détails sur la recommandation. La quantité recommandée est calculée pour l’utilisation la plus élevée possible et est basée sur votre historique d’utilisation. Votre recommandation peut ne pas cibler une utilisation de 100 % si vous avez une utilisation incohérente. Dans l’exemple, remarquez que l’utilisation a fluctué dans le temps. Le coût de la réservation, les économies possibles et le pourcentage d’utilisation sont affichés.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Exemple présentant les détails d’une recommandation d’achat de réservation" :::

Le graphique et les valeurs estimées changent quand vous augmentez la quantité recommandée. En augmentant la quantité de réservation, vos économies seront réduites, car vous vous retrouverez avec une utilisation réduite de la réservation. En d’autres termes, vous payez pour des réservations qui ne sont pas entièrement utilisées.

Si vous réduisez la quantité de réservation, vos économies seront également réduites. Bien que vous ayez une utilisation accrue, il y aura probablement des périodes où vos réservations ne couvriront pas pleinement votre utilisation. Une utilisation au-delà de la quantité de réservation sera employée par des ressources de paiement à l’utilisation plus coûteuses. L’exemple d’image suivant illustre le point. Nous avons réduit manuellement la quantité de réservation à 4. L’utilisation de la réservation est augmentée, mais les économies globales sont réduites, car il y a des coûts de paiement à l’utilisation.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Exemple présentant les détails d’une recommandation d’achat de réservation modifiée" :::

Pour optimiser les économies avec les réservations, essayez d’acheter des réservations aussi près que possible de la recommandation.

## <a name="recommendations-in-azure-advisor"></a>Recommandations dans Azure Advisor

Les recommandations d’achat de réservation sont disponibles dans Azure Advisor. Gardez à l’esprit les points suivants :

- Advisor produit des recommandations dont l’étendue est limitée à un seul abonnement. Si vous voulez afficher les recommandations pour l’étendue de facturation dans son ensemble (compte de facturation ou profil de facturation), procédez comme suit :
  -  Sur le portail Azure, accédez à **Réservations** > **Ajouter**, puis sélectionnez le type pour lequel vous souhaitez afficher les recommandations.
- Les recommandations disponibles dans Advisor prennent en considération la tendance de votre utilisation sur les 30 derniers jours.
- La quantité et les économies des recommandations s’appliquent à une réservation de trois ans, le cas échéant. S’il n’y a pas réservation de trois ans en vente pour le service, la recommandation est calculée en utilisant le prix de réservation d’un an.
- Les calculs de recommandation tiennent compte des remises spéciales qui peuvent s’appliquer à vos tarifs d’utilisation à la demande.
- Si vous achetez une réservation à étendue partagée, les recommandations d’achat de réservation d’Advisor peuvent prendre jusqu’à cinq jours pour disparaître.

## <a name="other-expected-api-behavior"></a>Autre comportement attendu de l’API

- Si vous utilisez une période de recherche de 7 jours, vous risquez de ne pas obtenir de recommandations quand les machines virtuelles sont arrêtées pendant plus d’une journée.

## <a name="next-steps"></a>Étapes suivantes
- Obtenez des [recommandations sur les réservations utilisant les API REST](/rest/api/consumption/reservationrecommendations/list).
- Découvrez [comment la remise de réservation Azure est appliquée aux machines virtuelles](../manage/understand-vm-reservation-charges.md).
