---
title: Qu’est-ce qu’une réservation Azure ? | Microsoft Docs
description: Découvrez les réservations et tarifs Azure pour économiser sur vos machines virtuelles, les bases de données SQL, Azure Cosmos DB et les coûts d’autres ressources.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 1349a05e1dd235c7b375335ae2c9fed16170a61f
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649388"
---
# <a name="what-are-azure-reservations"></a>Qu’est-ce qu’une réservation Azure ?

Les réservations Azure vous permettent d’économiser de l’argent en prépayant un ou trois ans de machine virtuelle, de capacité de calcul SQL Database, de débit Azure Cosmos DB ou d’autres ressources Azure. Le prépaiement vous permet d’obtenir une remise sur les ressources que vous utilisez. Les réservations peuvent réduire sensiblement les coûts de machine virtuelle, de calcul SQL Database, d’Azure Cosmos DB ou d’autres ressources, jusqu’à hauteur de 72 % sur les tarifs des paiements à l’utilisation. Des réservations permettent de bénéficier d’une remise sur la facturation et n’ont aucune incidence sur l’état de runtime de vos ressources.

Vous pouvez acheter une réservation sur le [portail Azure](https://aka.ms/reservations). Pour plus d’informations, consultez les articles suivants :

Plans de service :
- [Machines virtuelles avec des Instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Capacité réservée de ressources Cosmos DB Azure avec Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Capacité réservée de ressources de calcul de base de données SQL avec la base de données SQL Azure](../sql-database/sql-database-reserved-capacity.md)

Abonnements logiciels :
- [Plans de logiciels Red Hat à partir des réservations d’Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Plans de logiciel SUSE à partir des réservations d’Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

## <a name="why-buy-a-reservation"></a>Pourquoi acheter une réservation ?

Si vous avez des machines virtuelles, Azure Cosmos DB ou les bases de données SQL qui s’exécutent pendant de longues périodes, achat d’une réservation vous donne l’option la plus rentable. Par exemple, lorsque vous exécutez en permanence quatre instances d’un service sans réservation, vous êtes facturé au tarif de paiement à l’utilisation. Si vous achetez une réservation pour ces ressources, vous obtenez immédiatement la remise de réservation. Les ressources ne sont plus facturées au tarif du paiement à l’utilisation.

## <a name="charges-covered-by-reservation"></a>Frais couverts par la réservation

Plans de service :

- Instance de machine virtuelle réservée : une réservation couvre uniquement les coûts de calcul de machine virtuelle. Une réservation ne couvre pas les frais de logiciels, de mise en réseau ou de stockage.
- Capacité réservée Azure Cosmos DB : Une réservation couvre le débit approvisionné pour vos ressources. Elle ne couvre pas les frais de stockage et de réseau.
- vCore réservé de SQL Database : seuls les coûts de calcul sont inclus dans une réservation. La licence est facturée séparément.
- Capacité réservée Azure Cosmos DB : une réservation couvre le débit provisionné pour vos ressources, mais pas les frais de stockage et de réseau.

Pour les machines virtuelles Windows et SQL Database, vous pouvez couvrir les coûts de licence avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Qui peut acheter une réservation ?

Pour acheter un plan, vous devez disposer d’un rôle de propriétaire d’abonnement dans une entreprise (MS-AZR - 0017p ou MS-AZR - 0148p) ou d’un abonnement de paiement à l’utilisation (MS-AZR - 003p ou MS-AZR - 0023P). Fournisseurs de solutions cloud peuvent utiliser le portail Azure ou [partenaires](/partner-center/azure-reservations) d’acheter des réservations d’Azure.

Les clients EA peuvent limiter les achats aux administrateurs de l’EA en désactivant le **ajouter des Instances réservées** option dans le portail EA. Les administrateurs EA doivent être un propriétaire d’abonnement au moins un abonnement EA pour acheter une réservation. L’option est utile pour les entreprises qui veulent une équipe centralisée pour acheter des réservations pour les centres de coûts différentes. Après l’achat, équipes centralisés peuvent ajouter des propriétaires de centre de coût pour les réservations. Propriétaires peuvent ensuite définir l’étendue la réservation à leurs abonnements. L’équipe centrale n’a pas besoin d’avoir accès à l’abonnement propriétaire où la réservation est achetée.

Une remise sur réservation s’applique uniquement aux ressources associées aux types d’abonnements CSP, Entreprise ou avec paiement à l’utilisation.

## <a name="how-is-a-reservation-billed"></a>Comment une réservation est-elle facturée ?

La réservation est facturée selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût des réservations est déduit de votre solde d’engagement. Si ce solde ne couvre pas le coût des réservations, le dépassement vous est facturé. Si vous avez souscrit un abonnement avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est appliquée

La remise sur réservation s’applique à l’utilisation de ressource correspondant aux attributs que vous sélectionnez lorsque vous achetez la réservation. Ces attributs englobent l’étendue dans laquelle les machines virtuelles, les bases de données SQL, Azure Cosmos DB ou d’autres ressources correspondantes s’exécutent. Par exemple, si vous voulez une remise sur réservation pour quatre machines virtuelles Standard D2 de la région USA Ouest, sélectionnez l’abonnement dans le cadre duquel ces machines virtuelles s’exécutent. Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue partagée permet d’appliquer la remise de réservation à différents abonnements. Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour plus d’informations, voir [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md).

Une remise sur réservation s’applique uniquement aux ressources associées aux types d’abonnements CSP, Entreprise ou avec paiement à l’utilisation. Les ressources opérant dans le cadre d’un abonnement relevant d’autres types d’offres ne bénéficient d’aucune remise sur réservation.

Pour mieux comprendre comment les réservations affecte votre facturation, consultez les articles suivants :

Plans de service :

- [Comprendre la remise sur Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md)
- [Comprendre la remise sur réservation Azure](billing-understand-vm-reservation-charges.md)
- [Comprendre la remise sur réservation Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)

Abonnements logiciels :

- [Comprendre la remise de réservation Azure et d’utilisation pour Red Hat](billing-understand-rhel-reservation-charges.md)
- [Comprendre la remise sur réservation Azure et son usage pour SUSE](billing-understand-suse-reservation-charges.md)

## <a name="when-the-reservation-term-expires"></a>Lorsque le terme de la réservation expire

À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et la machine virtuelle, la base de données SQL, Azure Cosmos DB ou tout autre ressource est facturée au tarif du paiement à l’utilisation. Les réservations Azure ne sont pas renouvelées automatiquement. Pour continuer de bénéficier de la remise sur facturation, vous devez acheter une nouvelle réservation de services et logiciels éligibles.

## <a name="discount-applies-to-different-sizes"></a>Remise s’applique aux différentes tailles

Quand vous achetez une réservation, la remise peut être appliquée à d’autres instances avec des attributs qui se trouvent dans le même groupe de tailles. Cette fonctionnalité est appelée à la flexibilité de taille d’instance. La flexibilité de la couverture de remise varie selon le type de réservation et les attributs que vous choisissez quand vous achetez la réservation.

Plans de service :

- Instances de machines virtuelles réservées : Lorsque vous achetez la réservation et sélectionnez **optimisé pour**: **flexibilité de la taille d’instance**, la couverture de remise varie selon la taille de machine virtuelle que vous sélectionnez. La réservation peut s’appliquer aux tailles des machines virtuelles dans le même groupe de gammes de tailles. Pour plus d’informations, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
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
