---
title: Optimiser les coûts d’Azure Synapse Analytics avec un plan de préachat
description: Découvrez comment réduire les coûts d’Azure Synapse Analytics en préachetant des unités de validation Azure Synapse (SCU) pour une année.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/08/2021
ms.author: banders
ms.openlocfilehash: 031dc79bfabf03bfadf0c7e01171cbb72a15008e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007374"
---
# <a name="optimize-azure-synapse-analytics-costs-with-a-pre-purchase-plan"></a>Optimiser les coûts d’Azure Synapse Analytics avec un plan de préachat

Vous pouvez réduire les coûts d’Azure Synapse Analytics en préachetant des unités de validation Azure Synapse (SCU) pour une année. Vous pouvez utiliser les unités SCU préachetées à tout moment durant la période d’achat. Contrairement aux machines virtuelles, les unités préachetées n’expirent pas sur une base horaire et vous pouvez les utiliser à tout moment durant la période d’achat.

Toute utilisation d’Azure Synapse Analytics est déduite automatiquement des unités SCU préachetées. Vous n’avez pas besoin de redéployer ou d’affecter un plan préacheté à vos espaces de travail Azure Synapse Analytics pour bénéficier des remises de préachat.

## <a name="determine-the-right-size-to-buy"></a>Déterminez la taille appropriée à acheter

Un préachat Synapse s’applique à l’ensemble des charges de travail et des niveaux de Synapse. Vous pouvez considérer le plan de préachat comme un pool d’unités de validation Synapse prépayées. L’utilisation est déduite du pool, quelle que soit la charge de travail ou le niveau. Les autres frais liés au calcul, au stockage et à la mise en réseau sont facturés séparément.

La remise de préachat Synapse s’applique à l’utilisation des produits suivants :

- Pool SQL dédié Azure Synapse Analytics
- VNET managé Azure Synapse Analytics
- Pipelines Azure Synapse Analytics
- Pool SQL serverless Azure Synapse Analytics
- Pool Apache Spark serverless Azure Synapse Analytics à mémoire optimisée
- Flux de données Azure Synapse Analytics de base
- Flux de données Azure Synapse Analytics standard

Pour plus d’informations sur les niveaux de SCU disponibles et les remises tarifaires, reportez-vous à l’expérience d’achat de réservations dans la section suivante.

## <a name="purchase-synapse-commit-units"></a>Acheter des unités de validation Synapse

L’achat de plans Synapse s’effectue dans le [portail Azure](https://portal.azure.com). Pour acquérir un plan de préachat, vous devez avoir le rôle de propriétaire pour au moins un abonnement d’entreprise.

- Vous devez avoir un rôle de propriétaire pour au moins un Contrat Entreprise (références de l’offre : MS-AZR-0017P or MS-AZR-0148P) ou Microsoft Customer Agreement (MCA) ou un abonnement individuel avec paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P).
- Pour les abonnements Contrat Entreprise (EA), l’option **Ajouter des instances réservées** doit être activée dans le [portail EA](https://ea.azure.com/). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.

### <a name="to-purchase"></a>Pour acheter :

1. Accédez au [portail Azure](https://portal.azure.com/?synapse=true#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand/autoOpenSpecPicker//productType/Reservation).
1. Si nécessaire, accédez à **Réservations** puis, en haut de la page, sélectionnez **+ Ajouter**.
1. Dans la page Acheter des réservations, sélectionnez **Plan de préachat Azure Synapse Analytics**.
1. Dans la page Sélectionner le produit que vous voulez acheter, sélectionnez un abonnement. Utilisez la liste **Abonnement** pour choisir l’abonnement à utiliser pour payer la capacité réservée. Les coûts initiaux de la capacité réservée sont facturés conformément au mode de paiement défini sur l’abonnement. Les frais sont déduits du solde du Paiement anticipé Azure (anciennement « Engagement financier »), le cas échéant, ou facturés comme un dépassement.
1. Sélectionnez une étendue. Utilisez la liste **Étendue** pour choisir une étendue d’abonnement :
    - **Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.
    - **Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.
    - **Étendue partagée** : applique la remise de réservation aux ressources correspondantes des abonnements éligibles inclus dans le contexte de facturation. Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription.
    - **Groupe d’administration** : applique la remise de réservation à la ressource correspondante dans la liste des abonnements qui font partie du groupe d’administration et de l’étendue de facturation.
1. Sélectionnez le nombre d’unités de validation Azure Synapse que vous souhaitez acheter, puis finalisez l’achat.  
    :::image type="content" source="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" alt-text="Capture d’écran montrant l’expérience Sélectionner le produit pour le plan de préachat Azure Synapse Analytics." lightbox="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" :::

## <a name="change-scope-and-ownership"></a>Modifiez l’étendue et la propriété

Vous pouvez apporter les modifications suivantes à une réservation après achat :

- Mettez à jour l’étendue de la réservation
- Contrôle d’accès en fonction du rôle Azure (Azure RBAC)

Vous ne pouvez pas fractionner ou fusionner un plan de préachat d’unités de validation Synapse. Pour plus d’informations sur la gestion des réservations, consultez [Gérer les réservations après l’achat](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Les annulations et les échanges ne sont pas pris en charge dans le cadre des plans de préachat Synapse. Tous les achats sont finaux.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
- [Gérer les réservations Azure](manage-reserved-vm-instance.md)
- [Comprendre la remise sur réservation Azure](understand-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)