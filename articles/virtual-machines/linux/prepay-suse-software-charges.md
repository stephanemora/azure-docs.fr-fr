---
title: Acheter des abonnements logiciels SUSE Linux - Azure Reservations | Microsoft Docs
description: Découvrez comment prépayer votre utilisation de SUSE et faire des économies par rapport au paiement à l’utilisation.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 44d23cf38eb1dca9ade94c5a8fd0ae495300be44
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582188"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Prépayer des abonnements logiciels SUSE dans Azure Reservations

Prépayez votre utilisation de SUSE et faites des économies par rapport au paiement à l’utilisation. Les remises s’appliquent uniquement aux compteurs SUSE et non à l’utilisation de la machine virtuelle. Vous pouvez acheter des réservations de machines virtuelles séparément pour réaliser encore plus d’économies.

Vous pouvez acheter des abonnements logiciels SUSE dans le portail Azure. Pour acheter un abonnement logiciel :

- Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
- Pour les abonnements Entreprise, les achats de réservation doivent être activés dans le [portal EA](https://ea.azure.com).
- Pour le programme CSP, seuls les administrateurs et les commerciaux peuvent acheter des abonnements logiciels SUSE.

## <a name="buy-a-suse-software-plan"></a>Acheter un abonnement logiciel SUSE

1. Accédez à [Réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) dans le portail Azure.
1. Sélectionnez **Ajouter**, puis sélectionnez SUSE Linux.
1. Renseignez les champs obligatoires. Toutes les machines virtuelles SUSE Linux qui correspondent aux attributs de ce que vous achetez peuvent bénéficier de la remise. Le nombre de déploiements qui peuvent bénéficier de la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |:------------|:--------------|
    |NOM        |Nom de cet achat|
    |Abonnement|Abonnement utilisé pour payer cet abonnement logiciel. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être un contrat Entreprise (numéro de l’offre : MS-AZR-0017P) ou Paiement à l’utilisation (numéro de l’offre : MS-AZR-0003P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|
    |Étendue       |L’étendue peut couvrir un ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise sur l’abonnement logiciel est appliquée à l’utilisation de SUSE Linux dans cet abonnement. </li><li>Partagé : la remise sur l’abonnement logiciel est appliquée à l’utilisation de SUSE Linux de tout abonnement compris dans votre contexte de facturation. Pour les clients Entreprise, l’étendue partagée correspond à l’inscription et inclut tous les abonnements (à l’exception des abonnements de développement/test) au sein de l’inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Abonnement logiciel     |Sélectionnez l’abonnement SUSE Linux. Pour savoir quel abonnement acheter, consultez [Comprendre l’application des remises aux réservations de logiciels SUSE Linux Enterprise](../../billing/billing-understand-suse-reservation-charges.md).|
    |Taille de la machine virtuelle     |Les tarifs SUSE Linux dépendent du nombre de processeurs virtuels de la machine virtuelle. Sélectionnez l’option qui correspond au nombre de processeurs virtuels de vos machines virtuelles SUSE Linux.|
    |Terme        |Une année ou trois ans.|
    |Quantité    |Nombre de machines virtuelles pour lesquelles vous achetez cet abonnement logiciel SUSE Linux. La quantité correspond au nombre d’instances SUSE Linux en cours d’exécution pouvant bénéficier de la remise.|
1. Sélectionnez **Achat**.
1. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

La remise de réservation est automatiquement appliquée aux machines virtuelles SUSE en cours d’exécution qui correspondent à la réservation. La remise s’applique uniquement au compteur SUSE. Les frais de calcul de la machine virtuelle ne sont pas couverts par cet abonnement logiciel.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>La remise s’applique aux différentes tailles de machine virtuelle avec une flexibilité de taille d’instance

Comme pour les instances réservées de machine virtuelle, les abonnements logiciels SUSE Linux offrent une certaine flexibilité au niveau de la taille des instances. Cela signifie que votre remise est appliquée, même lorsque vous déployez une machine virtuelle dont la taille est différente de celle de l’abonnement logiciel SUSE que vous avez acheté. Pour plus d’informations, consultez [Comprendre l’application des remises aux réservations de logiciels SUSE Linux Enterprise](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="cancellation-and-exchanges-not-allowed"></a>Les annulations et les échanges sont interdits

Vous ne pouvez pas annuler ni échanger un abonnement logiciel SUSE que vous avez acheté. Pour être sûr d’acheter l’abonnement logiciel qui vous convient, étudiez votre utilisation. Pour savoir quel abonnement acheter, consultez [Comprendre l’application des remises aux réservations de logiciels SUSE Linux Enterprise](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../../billing/billing-manage-reserved-vm-instance.md).

Pour en savoir plus, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../../billing/billing-save-compute-costs-reservations.md)
- [Gérer les réservations dans Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Comprendre l’application des remises de réservation SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../../billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).