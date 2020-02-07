---
title: Comprendre comment la remise de réservation est appliquée à Stockage sur disque Azure
description: Découvrez comment la remise des disques réservés Azure est appliquée à vos disques managés SSD Premium.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902131"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Comprendre comment votre remise de réservation est appliquée à Stockage sur disque Azure

Une fois que vous avez acheté une capacité réservée de disque Azure, la remise de réservation est automatiquement appliquée aux ressources du disque qui correspondent aux conditions de votre réservation. La remise de la réservation s’applique uniquement à la capacité du disque. Les instantanés du disque sont facturés selon les tarifs du paiement à l’utilisation.

Pour plus d’informations sur la réservation de disque Azure, consultez [Économies avec la réservation de disques Azure](../../virtual-machines/linux/disks-reserved-capacity.md).
Pour plus d’informations sur la tarification de la réservation de disque Azure, consultez [Tarification de disque Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

La remise de la réservation de disque Azure est une remise de type « capacité utilisée ou perdue » et s’applique aux ressources de disque managé sur une base horaire. Si vous n’avez pas de ressources de disque managé correspondant aux conditions de votre réservation pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Les heures non utilisées ne sont pas reportées.

Lorsque vous supprimez une ressource, la remise de réservation s’applique automatiquement à une autre ressource correspondante dans l’étendue spécifiée. Si aucune ressource correspondante n’est trouvée dans l’étendue spécifiée, les heures réservées sont perdues.

## <a name="discount-examples"></a>Exemples de remises

Les exemples suivants montrent comment la remise de la réservation de disque Azure s’applique en fonction de vos déploiements :

Supposons que vous avez acheté 100 disques P30 (~1 Tio par disque) de capacité réservée dans la région USA Ouest 2 pour un délai de 1 an. Supposons que le coût de cet exemple de réservation est de 140 100 $. Vous pouvez choisir de payer le montant total à l’avance ou de payer des versements mensuels fixes de 11 675 $ par mois pendant les douze prochains mois.
Pour ces exemples, supposons que vous vous êtes inscrit à un plan de paiement de réservation mensuel. Les scénarios suivants décrivent ce qui se passe si vous sous-utilisez, sur-utilisez ou hiérarchisez votre capacité réservée.

### <a name="underusing-your-capacity"></a>Sous-utilisation de votre capacité

Supposons que, pendant une heure donnée de la période de réservation, vous n’avez déployé que 99 disques P30 SSD Premium sur votre réservation de 100 disques P30. Le disque P30 restant n’est pas appliqué pendant cette heure et n’est pas reporté.

### <a name="overusing-your-capacity"></a>Sur-utilisation de votre capacité

Supposons que, pendant une heure donnée de la période de réservation, vous utilisez 101 disques P30 SSD Premium. La remise de la réservation s’applique uniquement à 100 disques P30 et le disque P30 restant est facturé au tarif de paiement à l’utilisation pour cette heure. Pour l’heure suivante, si votre utilisation passe à 100 disques P30, toute l’utilisation est couverte par la réservation.

### <a name="tiering-your-capacity"></a>Hiérarchisation de votre capacité

Supposons que, pendant une heure donnée de la période de réservation, vous souhaitez utiliser au total 200 disques P30 SSD Premium. Pendant les 30 premières minutes, vous n’en utilisez que 100. Pendant cette période, votre utilisation est entièrement couverte, car vous avez effectué une réservation pour 100 disques P30. Si vous interrompez ensuite l’utilisation des 100 premiers disques (votre utilisation est donc de zéro), puis que vous commencez à utiliser les 100 autres disques pendant les 30 minutes restantes, cette utilisation est également couverte dans le cadre de votre réservation.

![Description des exemples de sous-utilisation, de sur-utilisation et de hiérarchisation de la capacité](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Réduire les coûts avec la réservation de disques Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Réduire les coûts avec la réservation de disques Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)