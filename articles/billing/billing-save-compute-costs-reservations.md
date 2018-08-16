---
title: Que sont les réservations Azure ? | Microsoft Docs
description: Découvrez les réservations et la tarification Azure pour économiser sur vos machines virtuelles, les bases de données SQL et d’autres coûts de ressources.
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626302"
---
# <a name="what-are-azure-reservations"></a>Que sont les réservations Azure ?

Les réservations Azure vous permettent d’économiser de l’argent en prépayant un ou trois ans de capacité de calcul de machine virtuelle ou de base de données SQL Database. Le prépaiement vous permet d’obtenir une remise sur les ressources que vous utilisez. Avec un engagement initial d’une durée de 1 ou 3 ans, les réservations Azure réduisent considérablement (jusqu’à 72 % par rapport au tarif du paiement à l’utilisation) les coûts de calcul de vos machines virtuelles ou de votre base de données SQL. Les réservations permettent de bénéficier d’une remise sur la facturation et n’ont aucune incidence sur l’état de runtime de vos machines virtuelles ou bases de données SQL.

Vous pouvez acheter une réservation sur le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez les rubriques suivantes :

- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Pourquoi acheter une réservation ?

Si vous avez des machines virtuelles ou des bases de données SQL qui fonctionnent sur de longues périodes, l’achat d’une réservation est l’option la plus économique. Par exemple, si vous exécutez en permanence quatre instances d’un service sans réservation, vous êtes facturé au tarif du paiement à l’utilisation. Si vous achetez une réservation pour ces ressources, vous bénéficiez immédiatement de la remise sur celle-ci réservation. Les ressources ne sont plus facturées au tarif du paiement à l’utilisation.

## <a name="what-charges-does-a-reservation-cover"></a>Quels frais une réservation couvre-t-elle ?

- Instance de machine virtuelle réservée : une réservation couvre uniquement les coûts de calcul de machine virtuelle. Une réservation ne couvre pas les frais de logiciels, de mise en réseau ou de stockage.
- vCore réservé de SQL Database : seuls les coûts de calcul sont inclus dans une réservation. La licence est facturée séparément.

Pour les machines virtuelles Windows et SQL Database, vous pouvez couvrir les coûts de licence avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Qui peut acheter une réservation ?

Peuvent acheter réservation les clients Azure ayant souscrit à les types d’abonnements suivants :

- Offre d’abonnement de type contrat Entreprise (MS-AZR-0017P).
- Offre d’abonnement de type [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Pour pouvoir acheter une réservation, vous devez avoir le rôle « Propriétaire » sur l’abonnement. Pour l’achat de réservations dans le cadre d’une inscription d’entreprise, l’administrateur de l’entreprise doit activer les achats de réservations sur le portail de l’Accord Entreprise. Par défaut ce paramètre est activé.
- Les partenaires fournisseurs de solutions cloud (CSP) peuvent utiliser le portail Azure ou l’[Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations) pour acheter des réservations Azure. 

Une remise sur réservation Azure s’applique uniquement aux machines virtuelles ou aux bases de données SQL associées aux types d’abonnements CSP, Entreprise ou avec paiement à l’utilisation.

## <a name="how-is-a-reservation-billed"></a>Comment une réservation est-elle facturée ?

La réservation est facturée selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût des réservations est déduit de votre solde d’engagement. Si ce solde ne couvre pas le coût des réservations, le dépassement vous est facturé. Si vous avez souscrit un abonnement avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-is-the-reservation-discount-applied"></a>Comment la remise sur réservation est-elle appliquée ?

La remise sur réservation s’applique à l’utilisation de ressource correspondant aux attributs que vous sélectionnez lorsque vous achetez la réservation. Ces attributs englobent l’étendue dans laquelle les machines virtuelles, les bases de données SQL ou d’autres ressources correspondantes s’exécuteront. Par exemple, si vous voulez une remise sur réservation pour quatre machines virtuelles Standard D2 de la région USA Ouest, sélectionnez l’abonnement dans le cadre duquel ces machines virtuelles s’exécutent. Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue partagée permet d’appliquer la remise de réservation à différents abonnements. Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour plus d’informations, voir [Gérer les réservations dans Azure](billing-manage-reserved-vm-instance.md).

La remise sur réservation Azure s’applique uniquement aux machines virtuelles ou aux bases de données SQL associées aux types d’abonnements Entreprise ou avec paiement à l’utilisation. Les machines virtuelles ou bases de données SQL opérant dans le cadre d’abonnements relevant d’autres types d’offres ne bénéficient pas de remises sur réservation. En ce qui concerne les inscriptions d’entreprise, les abonnements de Dev/Test ne peuvent pas bénéficier des avantages de réservation.

Pour mieux comprendre la manière dont les réservations affectent la facturation de votre machine virtuelle ou base de données SQL, voir [Comprendre comment la remise sur réservation est appliquée](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reservation-term-expires"></a>Que se passe-t-il lorsque le terme de la réservation expire ?

À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et la machine virtuelle, la base de données SQL ou les autres ressources sont facturées au tarif du paiement à l’utilisation. Les réservations Azure ne sont pas renouvelées automatiquement. Pour continuer de bénéficier de la remise sur réservation, vous devez acheter une nouvelle réservation pour des services susceptibles d’en bénéficier.

## <a name="next-steps"></a>Étapes suivantes

Commencez à économiser sur vos machines virtuelles en achetant une [Instance de machine virtuelle réservée](../virtual-machines/windows/prepay-reserved-vm-instances.md) ou une [capacité réservée SQL Database](../sql-database/sql-database-reserved-capacity.md).

Pour plus d’informations sur les réservations, voir les articles suivants :

- [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre comment la remise sur réservation est appliquée](billing-understand-vm-reservation-charges.md)
- [Comprendre l’usage d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’usage d’une réservation pour votre inscription d’entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
- [Réservations Azure dans le cadre du programme Fournisseur de solutions Microsoft Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez d’autres questions, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
