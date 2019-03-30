---
title: Acheter des plans de Red Hat Enterprise Linux - Azure réservations | Microsoft Docs
description: Découvrez comment vous pouvez prépayer les votre utilisation de Red Hat et faites des économies sur les coûts associés au paiement à l’utilisation.
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
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653016"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Prépayer les plans de Red Hat Enterprise Linux à partir des réservations d’Azure

Prépayer les votre utilisation de Red Hat et réalisez des économies sur les coûts associés au paiement à l’utilisation. Les remises s’appliquent uniquement aux compteurs de Red Hat et non sur l’utilisation de la machine virtuelle. Vous pouvez acheter des réservations de machines virtuelles séparément pour réaliser encore plus d’économies.

Vous pouvez acheter des abonnements de logiciels de Red Hat dans le portail Azure. Pour acheter un abonnement logiciel :

- Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.
- Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
- Pour le programme fournisseur de solutions Cloud (CSP), les agents d’administration ou des agents client peuvent acheter les plans de Red Hat.

## <a name="buy-a-red-hat-software-plan"></a>Acheter un plan de logiciels de Red Hat

1. Accédez à [Réservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) dans le portail Azure.
1. Sélectionnez **ajouter** et sélectionnez Red Hat Linux.
1. Renseignez les champs obligatoires. N’importe quel Linux machine virtuelle Red Hat qui correspond aux attributs de ce que vous achetez Obtient la remise. Le nombre de déploiements qui peuvent bénéficier de la remise dépend de l’étendue et de la quantité sélectionnées.

    | Champ      | Description|
    |:------------|:--------------|
    |Nom        |Nom de cet achat|
    |Abonnement|Abonnement utilisé pour payer cet abonnement logiciel. Les coûts initiaux de la réservation sont facturés au mode de paiement défini sur l’abonnement. Le type d’abonnement doit être Accord Entreprise (numéros de l’offre : MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l’utilisation (numéros de l’offre : MS-AZR-0003P ou MS-AZR-0023P). Pour un abonnement Entreprise, les frais sont déduits du solde d’engagement monétaire de l’inscription ou facturés comme un dépassement. Pour un abonnement Paiement à l’utilisation, les frais sont facturés sur le mode de paiement par carte de crédit ou par facture défini sur l’abonnement.|
    |Étendue       |L’étendue peut couvrir un ou plusieurs abonnements (étendue partagée). Si vous sélectionnez : <ul><li>Abonnement unique : la remise de plan est appliqué à l’utilisation de Red Hat Linux dans cet abonnement. </li><li>Shared - la remise de plan est appliquée à l’utilisation de Red Hat Linux dans n’importe quel abonnement au sein de votre contexte de facturation. Pour les clients Entreprise, l'étendue partagée correspond à l'inscription et inclut tous les abonnements compris dans l'inscription. Pour les clients Paiement à l’utilisation, l’étendue partagée correspond à tous les abonnements Paiement à l’utilisation créés par l’administrateur de compte.</li></ul>|
    |Planification     |Sélectionnez le plan de Red Hat Linux. Pour vous aider à identifier les éléments à acheter, consultez comprendre comment la remise de réservation de logiciels de Red Hat Linux Enterprise est appliquée.|
    |Taille de la machine virtuelle     |Red Hat Linux tarification varie selon le nombre de processeurs virtuels sur la machine virtuelle. Sélectionnez l’option qui représente le nombre de processeurs virtuels sur vos machines virtuelles Red Hat Linux.|
    |Terme        |Une année ou trois ans.|
    |Quantité    |Le nombre de machines virtuelles que vous achetez pour ce plan de Red Hat Linux. La quantité est le nombre d’instances Red Hat Linux qui peuvent bénéficier de la remise de facturation en cours d’exécution.|
1. Sélectionnez **Achat**.
1. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

La remise de réservation est automatiquement appliquée pour les machines virtuelles Red Hat en cours d’exécution qui correspond à la réservation. La remise s’applique uniquement à la jauge de Red Hat. Les frais de calcul de la machine virtuelle ne sont pas couverts par cet abonnement logiciel.

## <a name="discount-applies-to-different-vm-sizes"></a>Remise s’applique aux différentes tailles de machine virtuelle

Comme les Instances de machine virtuelle réservées, Red Hat Linux offrent flexibilité de taille d’instance. Cela signifie que votre remise s’applique même lorsque vous déployez une machine virtuelle qui est une taille différente à partir du plan de Red Hat que vous avez acheté. Pour plus d’informations, consultez comprendre comment la remise de réservation de logiciels de Red Hat Linux Enterprise est appliquée.

## <a name="cancellation-and-exchanges-not-allowed"></a>Les annulations et les échanges sont interdits

Vous ne pouvez pas annuler ou d’échanger un plan de Red Hat que vous avez achetées. Pour être sûr d’acheter l’abonnement logiciel qui vous convient, étudiez votre utilisation. Pour vous aider à identifier les éléments à acheter, consultez comprendre comment la remise de réservation de logiciels de Red Hat Linux Enterprise est appliquée.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment gérer une réservation, consultez [Gérer les réservations Azure](../../billing/billing-manage-reserved-vm-instance.md).

Pour en savoir plus, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](../../billing/billing-save-compute-costs-reservations.md)
- [Gérer les réservations dans Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Comprendre comment la remise de réservation de Red Hat est appliquée
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](../../billing/billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.