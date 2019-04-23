---
title: Prépayer les plans de logiciels - Azure réservations | Microsoft Docs
description: Découvrez comment vous pouvez prépayer les abonnements logiciels réaliser des économies sur les coûts associés au paiement à l’utilisation.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011093"
---
# <a name="prepay-for-azure-software-plans"></a>Prépayer les abonnements logiciels Azure

Lorsque vous prépayez votre utilisation du logiciel SUSE et RedHat dans Azure, vous pouvez économiser de l’argent sur vos coûts de paiement à l’utilisation. Les remises s’appliquent uniquement aux mesures SUSE et RedHat et non sur l’utilisation de la machine virtuelle. Vous pouvez acheter des réservations pour les machines virtuelles séparément pour réaliser des économies supplémentaires.

Vous pouvez acheter des plans de logiciel SUSE et RedHat dans le portail Azure. Pour acheter un abonnement logiciel :

- Vous devez disposer du rôle de propriétaire au moins une entreprise ou un abonnement paiement à l’utilisation.
- Pour les abonnements de l’entreprise, le **ajouter des Instances réservées** option doit être activée dans le [portail EA](https://ea.azure.com/). Si le paramètre est désactivé, vous devez être un administrateur EA pour l’abonnement.
- Pour le programme fournisseur de solutions Cloud (CSP), les agents d’administration ou des agents client peuvent acheter les plans de logiciels.

## <a name="buy-a-software-plan"></a>Acheter un abonnement logiciel

1. Connectez-vous au portail Azure et accédez à [réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Cliquez sur **ajouter** , puis sélectionnez le plan de logiciel que vous souhaitez acheter.
Renseignez les champs obligatoires. Toute machine virtuelle SUSE Linux ou une VM RedHat qui correspond aux attributs de ce que vous achetez Obtient la remise. Le nombre de déploiements qui peuvent bénéficier de la remise dépend de l’étendue et de la quantité sélectionnées.
3. Sélectionnez un abonnement. Il est utilisé pour payer le plan.
La méthode de paiement d’abonnement est facturée le coût de démarrage pour la réservation. Le type d’abonnement doit être un contrat entreprise (offrent des nombres : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P).
    - Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement.
    - Pour un abonnement de paiement à l’utilisation, les frais sont facturés à la méthode de paiement de carte de crédit ou facture de l’abonnement.
4. Sélectionnez une étendue. L’étendue peut couvrir un ou plusieurs abonnements (étendue partagée).
    - Abonnement unique : la remise de plan est appliqué à la mise en correspondance l’utilisation de l’abonnement.
    - Shared - la remise de plan est appliquée pour faire correspondre des instances dans aucun abonnement de votre contexte de facturation. Pour les entreprises, le contexte de facturation correspond à l’inscription et inclut tous les abonnements lors de l’inscription. Pour les clients de paiement à l’utilisation, le contexte de facturation est tous les abonnements de paiement à l’utilisation créés par l’administrateur de compte.
5. Sélectionnez un produit pour choisir la taille de machine virtuelle et le type d’image. La remise s’applique à la taille de machine virtuelle sélectionnée uniquement.
6. Sélectionnez un terme de 1 an ou 3 ans.
7. Choisissez une quantité, qui est le nombre d’instances de machine virtuelle prépayés qui peuvent bénéficier de la remise de facturation.
8. Ajouter le produit à leur panier, révision et d’achat.

La remise de réservation est automatiquement appliquée à la jauge de logiciel que vous effectuez un prépaiement pour. Les frais de calcul de machine virtuelle ne sont pas couverts par le plan. Vous pouvez acheter les réservations de machine virtuelle séparément.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Remise s’applique aux différentes tailles de SUSE VM

Comme les instances de machine virtuelle réservées, SUSE Linux offrent flexibilité de taille d’instance. Votre remise s’applique même lorsque vous déployez une machine virtuelle qui est une taille différente à partir du plan SUSE que vous avez acheté. Pour plus d’informations, consultez [comprendre comment la remise de plan de logiciel est appliquée](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Remise de plan de Red Hat

Plans sont disponibles uniquement pour les machines virtuelles de Red Hat Enterprise Linux. La remise ne s’applique pas aux machines virtuelles Red Hat Enterprise Linux SAP HANA ou des machines virtuelles SAP Business applications Red Hat Enterprise Linux.

Les remises de plan de Red Hat s’appliquent uniquement à la taille de machine virtuelle que vous sélectionnez à la date d’achat. Plans RHEL ne peut pas être remboursés ou échangés après l’achat.


## <a name="cancellation-and-exchanges-not-allowed"></a>Les annulations et les échanges sont interdits

Vous ne pouvez pas annuler ou d’échanger un plan SUSE ou RedHat que vous avez achetées. Pour être sûr d’acheter l’abonnement logiciel qui vous convient, étudiez votre utilisation. Pour identifier les produits à acheter, consultez [comprendre comment la remise de plan de logiciel est appliquée](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../../billing/billing-manage-reserved-vm-instance.md).

Pour en savoir plus, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../../billing/billing-save-compute-costs-reservations.md)
- [Gérer les réservations dans Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Comprendre l’application des remises de réservation SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../../billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../../billing/billing-understand-reserved-instance-usage-ea.md)
