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
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851349"
---
# <a name="how-reservation-recommendations-are-created"></a>Mode de création des recommandations de réservation

Les recommandations relatives à l’achat d’une instance réservée Azure sont générées par [l’API Recommandation de réservation](/rest/api/consumption/reservationrecommendations) d’Azure Consumption. Les recommandations de l’API sont également utilisées par [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). Advisor affiche les recommandations dans le portail Azure.

Si vous avez des machines virtuelles en cours d’exécution dans Azure, vous pouvez bénéficier d’une tarification à prix réduit pour les instances réservées et prépayer vos machines virtuelles. L’API de recommandation Microsoft Consumption évalue votre utilisation sur 7, 30 et 60 jours et recommande des configurations optimales pour les instances réservées. Elle calcule le coût que vous payeriez si vous n’aviez pas d’instances réservées par rapport au coût que vous allez payer avec les instances réservées, afin de maximiser vos économies.

Azure Advisor affiche les recommandations basées sur une période de 30 jours.

Par souci de simplicité, l’exemple suivant montre les calculs effectués dans le cas d’une recommandation sur sept jours. La même méthode est appliquée lors du calcul de recommandations sur 30 ou 60 jours.

## <a name="calculation-method-example"></a>Exemple de méthode de calcul

Supposons que votre utilisation horaire d’une machine virtuelle Windows pour une référence SKU et une région spécifiques varie sur une période de sept jours (168 heures). L’utilisation minimale est de 65 unités et l’utilisation maximale est de 127 unités durant les sept jours. Dans cet exemple, la 79ème heure a utilisé 80 machines virtuelles et vous avez acheté 75 instances réservées.

Si vous achetez 75 instances réservées, vous payez les coûts suivants pour la 79ème heure :

- 75 instances réservées. Le coût est prépayé lorsque vous achetez des instances réservées.
- Les instances réservées couvrent le coût matériel lié à l’exécution des machines virtuelles. Vous payez donc 75 heures au tarif logiciel uniquement.
- L’utilisation pour la 79ème heure est de 80. Vous payez donc cinq heures au tarif du compteur combiné Windows et matériel. Le tarif combiné est basé sur votre tarif Accord Entreprise (EA) ou votre tarif de paiement à l’utilisation.

Si vous achetez 75 instances réservées, vous pouvez calculer le coût total en ajoutant les coûts horaires précédents. Vous pouvez également calculer votre coût actuel à l’aide de votre taux. La différence entre les deux montants représente les économies réalisées pour sept jours dans cet exemple.

L’API effectue les calculs pour chaque point d’utilisation spécifique. Ensuite, elle retourne la quantité recommandée où les économies sont les plus importantes. Dans l’exemple suivant, le graphe indique que le pic des économies est réalisé lorsque la quantité est égale à 68. Les économies se réduisant par la suite, l’API recommande d’utiliser une quantité égale à 68.

![Diagramme montrant les pics des économies](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Autre comportement attendu de l’API

- L’API indique les économies possibles avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) pour Windows lorsque l’avantage est utilisé. Si l’avantage n’est pas utilisé, les recommandations de l’API sont basées sur le coût de base de Windows. Si vous y avez accès, envisagez d’utiliser Azure Hybrid Benefit pour maximiser vos économies.
- Si vous utilisez une période de consultation de sept jours, vous risquez de ne pas recevoir de recommandations lorsque les machines virtuelles sont arrêtées pendant plus d’une journée.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez [comment la remise de réservation Azure est appliquée aux machines virtuelles](../manage/understand-vm-reservation-charges.md).
