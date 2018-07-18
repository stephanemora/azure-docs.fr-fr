---
title: Présentation des instances réservées Azure | Microsoft Docs
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
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064341"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Présentation d’Azure Reserved VM Instances
[Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances) vous permet d’économiser de l’argent en payant à l’avance la capacité de calcul pendant un an ou trois ans, grâce à une remise sur les machines virtuelles que vous utilisez. Avec un engagement initial d’une durée de 1 ou 3 ans, les instances réservées Azure réduisent considérablement les coûts de vos machines virtuelles, jusqu’à 72 % par rapport au tarif du paiement à l’utilisation. Les instances réservées offrent une remise sur la facturation et n’ont aucune incidence sur l’état d’exécution de vos machines virtuelles.

Vous pouvez acheter une instance réservée dans le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez [Prépayer des machines virtuelles et réaliser des économies avec des instances réservées](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Pourquoi acheter une instance réservée ?
Si vous avez des machines virtuelles qui fonctionnent sur de longues périodes, l’achat d’une instance réservée est la solution la plus économique pour vous. Par exemple, si vous exécutez en permanence quatre instances d’une machine virtuelle Standard D2 dans la région Ouest des États-Unis, et que vous n’avez pas d’instance réservée, vous êtes facturé au tarif du paiement à l’utilisation. Si vous achetez une instance réservée pour ces quatre machines virtuelles, elles bénéficient immédiatement de cette remise. Elles ne sont plus facturées au tarif du paiement à l’utilisation. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Quels sont les frais couverts par une instance réservée ?
Une instance réservée couvre seulement les frais d’infrastructure de vos machines virtuelles Windows ou Linux. Elle ne couvre pas les frais supplémentaires de logiciel, de mise en réseau ou de stockage. Pour les machines virtuelles Windows, vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Qui peut acheter une instance réservée ?
Les clients Azure ayant souscrit à ces types d’abonnement peuvent acheter une instance réservée :
-   Offre d’abonnement de type contrat Entreprise (MS-AZR-0017P).
-   Offre d’abonnement de type [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Vous devez avoir le rôle « Propriétaire » sur l’abonnement pour acheter une instance réservée. Pour pouvoir acheter des instances réservées dans le cadre d’un Accord de Mise en Œuvre Entreprise, l’administrateur de l’entreprise doit autoriser ces achats dans le portail EA. Par défaut ce paramètre est activé.
-   Les partenaires fournisseurs de solutions cloud (CSP) peuvent utiliser le portail Azure ou [l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations) pour acheter des instances réservées.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Comment un achat d’instance réservée est-il facturé ?
L’achat d’une instance réservée est facturé selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût de l’instance réservée est déduit du solde de votre engagement monétaire. Si ce solde ne couvre pas le coût de l’instance réservée, le dépassement vous est facturé.
Si vous avez souscrit un abonnement avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Comment la remise liée à l’instance réservée achetée est-elle appliquée ?
La remise liée à l’instance réservée s’applique aux machines virtuelles qui correspondent aux attributs que vous sélectionnez quand vous achetez l’instance réservée. Ces attributs englobent l’étendue où les machines virtuelles correspondantes sont exécutées. Par exemple, si vous voulez une remise d’instance réservée pour les quatre machines virtuelles Standard D2 de la région Ouest des États-Unis, sélectionnez l’abonnement où ces machines virtuelles s’exécutent. Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue permet d’appliquer la remise d’instance réservée sur les différents abonnements. Vous pouvez la modifier après l’achat d’une instance réservée. Pour changer l’étendue, consultez [Gérer des instances réservées dans Azure](billing-manage-reserved-vm-instance.md).

La remise liée à l’instance réservée s’applique seulement aux machines virtuelles associées aux abonnements d’entreprise ou de paiement à l’utilisation. Les machines virtuelles qui relèvent d’autres types d’abonnement ne bénéficient pas de cette remise. En ce qui concerne les Accords de Mise en Œuvre Entreprise, les abonnements Enterprise Dev/Test ne peuvent pas prétendre aux remises liées aux instances réservées.

Pour mieux comprendre comment une instance réservée affecte la facturation des machines virtuelles, consultez [Comprendre comment la remise liée à une instance réservée est appliquée](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>Que se passe-t-il quand l’instance réservée expire ?
Au terme de l’instance réservée, la remise cesse d’être appliquée et l’infrastructure de machines virtuelles est de nouveau facturée au tarif du paiement à l’utilisation. Les instances réservées Azure ne sont pas renouvelées automatiquement. Pour continuer à bénéficier de la remise, vous devez acheter une nouvelle instance réservée. 

## <a name="sizes-and-regional-availability"></a>Tailles et disponibilités régionales
Les instances réservées sont disponibles pour la plupart des tailles de machine virtuelle, à quelques exceptions près :
- Machines virtuelles en préversion : aucune série ou taille de machine virtuelle en préversion ne peut faire l’objet d’un achat d’instance réservée.
- Clouds : les instances réservées ne sont pas disponibles à l’achat dans les régions Azure Gouvernement des États-Unis, Allemagne et Chine. 
- Quota insuffisant : une instance réservée qui est limitée à un seul abonnement doit avoir un quota de processeurs virtuels disponible dans l’abonnement pour la nouvelle instance réservée. Par exemple, si l’abonnement cible a un quota limite de 10 processeurs virtuels pour la série D, vous ne pouvez pas acheter d’instance réservée pour 11 instances Standard_D1. La vérification du quota pour les instances réservées inclut les machines virtuelles déjà déployées dans l’abonnement. Par exemple, si l’abonnement a un quota de 10 processeurs virtuels pour la série D et a deux instances Standard_D1 déployées, vous pouvez acheter une instance réservée pour 10 instances standard_D1 dans cet abonnement. 
- Restrictions de capacité : dans de rares cas, Azure limite l’achat de nouvelles instances réservées pour certaines tailles de machine virtuelle, en raison d’une faible capacité dans une région.

## <a name="next-steps"></a>Étapes suivantes
Réduisez le coût de vos machines virtuelles en achetant une [instance de réservée Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Pour plus d’informations sur les instances réservées, consultez les articles suivants :

- [Présentation des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer des instances réservées dans Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise liée à une instance réservée est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’utilisation de l’instance réservée sur votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation de l’instance réservée pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les instances réservées](billing-reserved-instance-windows-software-costs.md)
- [Instances réservées dans le programme CSP de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
