---
title: Qu’est-ce qu’une réservation Azure ? | Microsoft Docs
description: Découvrez les réservations et tarifs Azure pour économiser sur vos machines virtuelles, les bases de données SQL, Azure Cosmos DB et les coûts d’autres ressources.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2019
ms.author: banders
ms.openlocfilehash: a4ad3574849d3c80f671abc93e463b06454289e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904165"
---
# <a name="what-are-azure-reservations"></a>Qu’est-ce qu’une réservation Azure ?

Les réservations Azure vous permettent d’économiser de l’argent en prépayant un ou trois ans de machine virtuelle, de capacité de calcul SQL Database, de débit Azure Cosmos DB ou d’autres ressources Azure. Le prépaiement vous permet d’obtenir une remise sur les ressources que vous utilisez. Les réservations peuvent réduire sensiblement les coûts de machine virtuelle, de calcul SQL Database, d’Azure Cosmos DB ou d’autres ressources, jusqu’à hauteur de 72 % sur les tarifs des paiements à l’utilisation. Des réservations permettent de bénéficier d’une remise sur la facturation et n’ont aucune incidence sur l’état de runtime de vos ressources.

Vous pouvez acheter une réservation sur le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez les rubriques suivantes :

- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Prépayer des ressources Azure Cosmos DB avec une capacité réservée Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Pourquoi acheter une réservation ?

Si vous avez des machines virtuelles, Azure Cosmos DB ou des bases de données SQL qui fonctionnent sur de longues périodes, l’achat d’une réservation est l’option la plus économique. Par exemple, si vous exécutez en permanence quatre instances d’un service sans réservation, vous êtes facturé au tarif du paiement à l’utilisation. Si vous achetez une réservation pour ces ressources, vous bénéficiez immédiatement de la remise sur celle-ci réservation. Les ressources ne sont plus facturées au tarif du paiement à l’utilisation.

## <a name="what-charges-does-a-reservation-cover"></a>Quels frais une réservation couvre-t-elle ?

- Instance de machine virtuelle réservée : une réservation couvre uniquement les coûts de calcul de machine virtuelle. Une réservation ne couvre pas les frais de logiciels, de mise en réseau ou de stockage.
- vCore réservé de SQL Database : seuls les coûts de calcul sont inclus dans une réservation. La licence est facturée séparément.
- Capacité réservée Azure Cosmos DB : une réservation couvre le débit provisionné pour vos ressources, mais pas les frais de stockage et de réseau.

Pour les machines virtuelles Windows et SQL Database, vous pouvez couvrir les coûts de licence avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Qui peut acheter une réservation ?

Peuvent acheter une réservation les clients Azure ayant souscrit les types d’abonnements suivants :

- Offre d’abonnement de type contrat Entreprise (MS-AZR-0017P ou MS-AZR-0148P).
- Offre d’abonnement de type paiement à l’utilisation (MS-AZR-003P ou MS-AZR-0023P).
- Les partenaires fournisseurs de solutions cloud (CSP) peuvent utiliser le portail Azure ou l’[Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations) pour acheter des réservations Azure.

Une remise sur réservation s’applique uniquement aux ressources associées aux types d’abonnements CSP, Entreprise ou avec paiement à l’utilisation.

 Pour acheter un abonnement logiciel :

- Vous devez avoir un rôle de propriétaire pour au moins un abonnement Entreprise ou Paiement à l’utilisation.

## <a name="how-is-a-reservation-billed"></a>Comment une réservation est-elle facturée ?

La réservation est facturée selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût des réservations est déduit de votre solde d’engagement. Si ce solde ne couvre pas le coût des réservations, le dépassement vous est facturé. Si vous avez souscrit un abonnement avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-is-the-reservation-discount-applied"></a>Comment la remise sur réservation est-elle appliquée ?

La remise sur réservation s’applique à l’utilisation de ressource correspondant aux attributs que vous sélectionnez lorsque vous achetez la réservation. Ces attributs englobent l’étendue dans laquelle les machines virtuelles, les bases de données SQL, Azure Cosmos DB ou d’autres ressources correspondantes s’exécutent. Par exemple, si vous voulez une remise sur réservation pour quatre machines virtuelles Standard D2 de la région USA Ouest, sélectionnez l’abonnement dans le cadre duquel ces machines virtuelles s’exécutent. Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue partagée permet d’appliquer la remise de réservation à différents abonnements. Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour plus d’informations, voir [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md).

Une remise sur réservation s’applique uniquement aux ressources associées aux types d’abonnements CSP, Entreprise ou avec paiement à l’utilisation. Les ressources opérant dans le cadre d’un abonnement relevant d’autres types d’offres ne bénéficient d’aucune remise sur réservation.

Pour mieux comprendre l’incidence des réservations sur la facturation, voir les rubriques suivantes :

- [Comprendre la remise sur Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md)
- [Comprendre la remise sur réservation Azure](billing-understand-vm-reservation-charges.md)
- [Comprendre la remise sur réservation Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)
- [Comprendre la remise sur réservation Azure et son usage pour SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>Que se passe-t-il lorsque le terme de la réservation expire ?

À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et la machine virtuelle, la base de données SQL, Azure Cosmos DB ou tout autre ressource est facturée au tarif du paiement à l’utilisation. Les réservations Azure ne sont pas renouvelées automatiquement. Pour continuer de bénéficier de la remise sur facturation, vous devez acheter une nouvelle réservation de services et logiciels éligibles.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>La remise s’applique aux différentes tailles avec une flexibilité de taille d’instance

Quand vous achetez une réservation, la remise peut être appliquée à d’autres instances avec des attributs qui se trouvent dans le même groupe de tailles. La flexibilité de la couverture de remise varie selon le type de réservation et les attributs que vous choisissez quand vous achetez la réservation.

- Instances de machines virtuelles réservées : quand vous achetez la réservation, si vous sélectionnez **Optimisé pour** : **flexibilité de la taille d’instance**, la couverture de remise dépend de la taille de machine virtuelle que vous sélectionnez. La réservation peut s’appliquer aux tailles des machines virtuelles dans le même groupe de gammes de tailles. Pour plus d’informations, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Abonnement logiciel SUSE Linux Enterprise : la couverture de remise dépend des processeurs virtuels des machines virtuelles où vous exécutez le logiciel SUSE. Pour plus d’informations, consultez [Comprendre comment la remise des abonnements logiciels SUSE Linux Enterprise est appliquée](billing-understand-suse-reservation-charges.md).
- Capacité réservée SQL Database : la couverture de remise dépend du niveau de performance que vous choisissez. Pour plus d’informations, consultez [Comprendre comment une remise de réservation Azure est appliquée aux bases de données SQL](billing-understand-reservation-charges.md).
- Capacité réservée Azure Cosmos DB : la couverture de remise dépend du débit provisionné. Pour plus d’informations, consultez [Comprendre comment une remise de réservation Azure Cosmos DB est appliquée](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Commencez à économiser sur vos machines virtuelles en achetant une [instance de machine virtuelle réservée](../virtual-machines/windows/prepay-reserved-vm-instances.md), une [capacité réservée SQL Database](../sql-database/sql-database-reserved-capacity.md) ou une [capacité réservée Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md).
- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
    - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
    - [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)
