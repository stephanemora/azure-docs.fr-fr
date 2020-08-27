---
title: Comprendre la remise sur les instances réservées aux hôtes dédiés Azure
description: Découvrez comment la remise sur les instances de machine virtuelle réservées Azure est appliquée aux hôtes dédiés Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: f9080d13c4358a03b3f13ffcf346af42e49c47c6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689454"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Comment la remise de réservation Azure est appliquée aux hôtes dédiés Azure

Quand vous achetez une instance d’hôte dédiée réservée Azure, la remise de réservation est automatiquement appliquée aux hôtes dédiés qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre les coûts de calcul de vos hôtes dédiés.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Ainsi, si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Quand vous supprimez un hôte dédié, la remise de réservation s’applique automatiquement à une autre ressource correspondante dans l’étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Remise de réservation pour les hôtes dédiés

L’instance des hôtes dédiés réservés Azure vous offre une remise sur le coût de l’infrastructure de calcul utilisée avec vos hôtes dédiés. La remise s’applique à vos hôtes dédiés, qu’ils soient, ou non, utilisés par des machines virtuelles. La réservation ne couvre pas les coûts supplémentaires comme la gestion des licences, l’utilisation du réseau ou la consommation du stockage par la machine virtuelle déployée sur l’hôte dédié.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide,  [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Utilisation d’hôtes dédiés Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Tarifs des hôtes dédiés](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gérer les réservations pour Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](https://docs.microsoft.com/partner-center/azure-reservations)

