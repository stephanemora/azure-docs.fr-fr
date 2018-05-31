---
title: Présentation d’Azure Reserved VM Instances - Facturation dans Azure | Microsoft Docs
description: Découvrez Azure Reserved VM Instances et la tarification des machines virtuelles pour réduire les coûts de vos machines virtuelles.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 93be4bb037af400599b88bb71f34143ee65a5deb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301700"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Présentation d’Azure Reserved VM Instances
[Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances) vous permet d’économiser de l’argent en payant à l’avance la capacité de calcul pendant un an ou trois ans, grâce à une remise sur les machines virtuelles que vous utilisez. Avec cet engagement initial d’une durée de 1 an ou de 3 ans, Azure Reserved VM Instances réduit considérablement les coûts de vos machines virtuelles, jusqu’à 72 % par rapport au tarif du paiement à l’utilisation. Les instances réservées offrent une remise sur facturation et n’ont aucune incidence sur l’état d’exécution de vos machines virtuelles.

Vous pouvez acheter une instance réservée (RI) sur le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Pourquoi acheter une instance virtuelle réservée ?
Si vous avez des machines virtuelles qui fonctionnent sur de longues périodes, l’achat d’une instance réservée est la solution la plus avantageuse pour vous. Par exemple, si vous exécutez en permanence quatre instances d’une machine virtuelle Standard D2 dans la région Ouest des États-Unis, et que vous n’avez pas d’instance réservée, vous êtes facturé au tarif du paiement à l’utilisation. Si vous achetez une instance réservée pour ces quatre machines virtuelles, elles bénéficient immédiatement de cette remise. Elles ne sont plus facturées au tarif du paiement à l’utilisation. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Quels sont les frais couverts par une instance réservée ?
Une instance réservée ne couvre que les frais d’infrastructure de vos machines virtuelles Windows ou Linux. Elle ne couvre pas les frais supplémentaires de logiciel, de mise en réseau ou de stockage. Pour les machines virtuelles Windows, vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Qui peut acheter une instance réservée ?
Les clients Azure ayant souscrit à ces types d’abonnement peuvent acheter une instance réservée :
-   Offre d’abonnement de type contrat Entreprise (MS-AZR-0017P).
-   Offre d’abonnement de type [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Vous devez avoir le rôle « Propriétaire » de l’abonnement pour acheter une instance réservée. Pour pouvoir acheter des instances réservées dans le cadre d’une inscription professionnelle, l’administrateur de l’entreprise doit autoriser ces achats dans le portail EA. Par défaut ce paramètre est activé.
-   Les partenaires fournisseurs de solutions cloud (CSP) peuvent utiliser le portail Azure ou l’[Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations) pour acheter des instances réservées.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Comment un achat d’instance réservée est-il facturé ?
L’achat d’une instance réservée est facturé selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût de l’instance réservée est déduit du solde de votre engagement monétaire. Si ce solde ne couvre pas le coût de l’instance réservée, le dépassement vous est facturé.
Si vous avez souscrit un abonnement avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Comment la remise d’instance réservée achetée est-elle appliquée ?
La remise d’instance réservée s’applique aux machines virtuelles qui correspondent aux attributs que vous sélectionnez lorsque vous achetez l’instance réservée. Ces attributs englobent l’étendue où les machines virtuelles correspondantes sont exécutées. Par exemple, si vous souhaitez une remise d’instance réservée pour les quatre machines virtuelles Standard D2 de la région Ouest des États-Unis, sélectionnez l’abonnement dans lequel ces machines virtuelles s’exécutent. Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette répartition permet d’appliquer la remise d’instance réservée à différents abonnements. Vous pouvez la modifier après l’achat d’une instance réservée. Pour modifier la répartition, consultez la documentation sur l’administration des instances réservées.

La remise d’instance réservée ne s’applique qu’aux machines virtuelles associées aux abonnements d’entreprise ou de paiement à l’utilisation. Les machines virtuelles qui relèvent d’autres types d’abonnement ne bénéficient pas de cette remise. En ce qui concerne les inscriptions pro, les abonnements Enterprise Dev/Test ne peuvent pas prétendre aux remises d’instances réservées.

Pour mieux comprendre comment l’instance réservée affecte la facturation des machines virtuelles, consultez [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Que se passe-t-il lorsque l’instance réservée arrive à terme ?
Au terme de l’instance réservée, la remise cesse d’être appliquée et l’infrastructure de machines virtuelles est de nouveau facturée au tarif du paiement à l’utilisation. Les instances réservées ne sont pas renouvelables automatiquement. Pour continuer à bénéficier de la remise, vous devez acheter une autre instance réservée. 

## <a name="sizes-and-regional-availability"></a>Tailles et disponibilités régionales
Les instances réservées sont disponibles pour la plupart des tailles de machine virtuelle, à quelques exceptions près :
- Machines virtuelles en préversion : aucune série ou taille de machine virtuelle existant en préversion ne peut faire l’objet d’un achat d’instance réservée.
- Clouds : les instances réservées ne sont pas disponibles à l’achat dans les régions Azure - Gouvernement des États-Unis, Allemagne et Chine. 
- Quota insuffisant : une instance réservée qui ne s’étend qu’à un abonnement doit avoir le quota de processeurs virtuels disponible dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a un quota limite de 10 processeurs virtuels pour la série D, vous ne pouvez pas acheter d’instance réservée pour 11 instances Standard_D1. La vérification du quota pour les instances réservées inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, si l’abonnement a un quota de 10 processeurs virtuels pour la série D et a deux instances Standard_D1 déployées, vous pouvez acheter une instance réservée pour 10 instances standard_D1 dans cet abonnement. 
- Restrictions de capacité : dans de rares cas, Azure limite l’achat de nouvelles instances réservées pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région.

## <a name="next-steps"></a>Étapes suivantes
Réduisez le coût de vos machines virtuelles en achetant une [instance de réservée Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Pour en savoir plus sur les instances réservées, consultez les articles suivants :

- [Prépayer les machines virtuelles avec des instances de machines virtuelles réservées](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Administrer les instances de machine virtuelle réservées Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise de l’offre d’instance de machine virtuelle réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’offre d’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’offre d’instance réservée pour l’inscription de votre entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)
- [Instances réservées dans le programme CSP de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
