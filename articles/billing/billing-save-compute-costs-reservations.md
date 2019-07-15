---
title: Qu’est-ce qu’une réservation Azure ?
description: Découvrez les réservations et tarifs Azure pour économiser sur vos machines virtuelles, les bases de données SQL, Azure Cosmos DB et les coûts d’autres ressources.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7ffb575d7f962232604a4ad6930b804d2619b488
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490595"
---
# <a name="what-are-azure-reservations"></a>Qu’est-ce qu’une réservation Azure ?

Les réservations Azure vous permettent d’économiser de l’argent en prépayant un ou trois ans de machine virtuelle, de capacité de calcul SQL Database, de débit Azure Cosmos DB ou d’autres ressources Azure. Le prépaiement vous permet d’obtenir une remise sur les ressources que vous utilisez. Les réservations peuvent réduire sensiblement les coûts de machine virtuelle, de calcul SQL Database, d’Azure Cosmos DB ou d’autres ressources, jusqu’à hauteur de 72 % sur les tarifs des paiements à l’utilisation. Des réservations permettent de bénéficier d’une remise sur la facturation et n’ont aucune incidence sur l’état de runtime de vos ressources.

Vous pouvez acheter une réservation sur le [portail Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Pourquoi acheter une réservation ?

Si vous avez des machines virtuelles, Azure Cosmos DB ou des bases de données SQL qui fonctionnent sur de longues périodes, l’achat d’une réservation est l’option la plus économique. Par exemple, si vous exécutez en permanence quatre instances d’un service sans réservation, vous êtes facturé au tarif du paiement à l’utilisation. Si vous achetez une réservation pour ces ressources, vous bénéficiez immédiatement de la remise sur celle-ci réservation. Les ressources ne sont plus facturées au tarif du paiement à l’utilisation.

## <a name="charges-covered-by-reservation"></a>Frais couverts par la réservation

Plans de service :

- **Instance de machine virtuelle réservée** : une réservation couvre uniquement les coûts de calcul de machine virtuelle. Une réservation ne couvre pas les frais de logiciels, de mise en réseau ou de stockage.
- **La capacité réservée Azure Cosmos DB** : une réservation couvre le débit provisionné pour vos ressources. Elle ne couvre pas les frais de stockage et de réseau.
- **vCore réservé de SQL Database** : seuls les coûts de calcul sont inclus dans une réservation. La licence est facturée séparément.

Pour les machines virtuelles Windows et SQL Database, vous pouvez couvrir les coûts de licence avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Qui peut acheter une réservation ?

Pour acheter un plan, vous devez disposer d’un rôle de propriétaire dans un abonnement Entreprise (MS-AZR-0017P ou MS-AZR-0148P) ou Paiement à l'utilisation (MS-AZR-003P or MS-AZR-0023P). Les fournisseurs de solutions cloud peuvent utiliser le portail Azure ou l’ [Espace partenaires](/partner-center/azure-reservations) pour acheter des réservations Azure.

Les clients EA peuvent limiter les achats aux administrateurs EA en désactivant l’option **Ajouter des instances réservées** dans EA Portal. Les administrateurs EA doivent être propriétaires d'au moins un abonnement EA pour acheter une réservation. Cette option est particulièrement utile pour les entreprises souhaitant qu'une équipe centralisée achète des réservations pour différents centres de coûts. Au terme de l'achat, les équipes centralisées peuvent ajouter des propriétaires de centres de coûts aux réservations. Les propriétaires peuvent ensuite étendre la réservation à leurs abonnements. L’équipe centralisée n'est pas tenue de disposer d'un accès de propriétaire d'abonnement là où la réservation est achetée.

Une remise de réservation s’applique uniquement aux ressources associées aux abonnements achetés via des forfaits d’entreprise, de fournisseurs de services cloud et individuels avec paiement à l’utilisation.

## <a name="reservation-scope"></a>Étendue de la réservation

Une étendue de réservation détermine les ressources auxquelles la remise de réservation s’applique. Une étendue de réservation peut présenter les valeurs suivantes :

**Étendue partagée** : la remise de réservation est appliquée aux ressources correspondantes dans les abonnements éligibles du contexte de facturation.

- Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription. Pour les clients disposant de forfaits individuels Paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.

**Abonnement unique** : la remise de réservation est appliquée aux ressources correspondantes incluses dans l’abonnement sélectionné.

Vous pouvez [mettre à jour l'étendue après avoir acheté une réservation](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

## <a name="discounted-subscription-and-offer-types"></a>Types d'abonnements et d'offres remisés

Les remises de réservation s’appliquent aux types d'abonnements et d'offres éligibles suivants.

- Contrat Entreprise (références de l’offre : MS-AZR-0017P ou MS-AZR-0148P)
- Forfaits individuels avec paiement à l’utilisation (références de l’offre : MS-AZR-0003P ou MS-AZR-0023P)
- Abonnements CSP

Les ressources opérant dans le cadre d’un abonnement relevant d’autres types d’offres ne bénéficient d’aucune remise sur réservation.

## <a name="how-is-a-reservation-billed"></a>Comment une réservation est-elle facturée ?

La réservation est facturée selon le mode de paiement associé à l’abonnement. Si vous avez souscrit un abonnement Entreprise, le coût des réservations est déduit de votre solde d’engagement. Si ce solde ne couvre pas le coût des réservations, le dépassement vous est facturé. Si vous avez souscrit un abonnement à partir d’un forfait individuel avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement. Si vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

La remise sur réservation s’applique à l’utilisation de ressource correspondant aux attributs que vous sélectionnez lorsque vous achetez la réservation. Ces attributs englobent l’étendue dans laquelle les machines virtuelles, les bases de données SQL, Azure Cosmos DB ou d’autres ressources correspondantes s’exécutent. Par exemple, si vous voulez une remise sur réservation pour quatre machines virtuelles Standard D2 de la région USA Ouest, sélectionnez l’abonnement dans le cadre duquel ces machines virtuelles s’exécutent.

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

Par exemple, vous pourrez plus tard créer une ressource et disposer d'une réservation correspondante sous-utilisée. Dans cet exemple, la remise de réservation s’applique automatiquement à la nouvelle ressource correspondante.

Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue partagée permet d’appliquer la remise de réservation à différents abonnements. Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour plus d’informations, voir [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md).

Une remise sur réservation s’applique uniquement aux ressources associées aux types d’abonnements CSP, Entreprise ou avec paiement à l’utilisation. Les ressources opérant dans le cadre d’un abonnement relevant d’autres types d’offres ne bénéficient d’aucune remise sur réservation.

## <a name="when-the-reservation-term-expires"></a>Expiration du terme de la réservation

À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et la machine virtuelle, la base de données SQL, Azure Cosmos DB ou tout autre ressource est facturée au tarif du paiement à l’utilisation. Les réservations Azure ne sont pas renouvelées automatiquement. Pour continuer de bénéficier de la remise sur facturation, vous devez acheter une nouvelle réservation de services et logiciels éligibles.

## <a name="discount-applies-to-different-sizes"></a>La remise s'applique à différentes tailles

Quand vous achetez une réservation, la remise peut être appliquée à d’autres instances avec des attributs qui se trouvent dans le même groupe de tailles. Cette fonctionnalité est appelée flexibilité de la taille d'instance. La flexibilité de la couverture de remise varie selon le type de réservation et les attributs que vous choisissez quand vous achetez la réservation.

Plans de service :

- Instances de machines virtuelles réservées : lorsque vous achetez la réservation et sélectionnez **Optimisé pour** : **flexibilité de la taille d’instance**, la couverture de remise dépend de la taille de machine virtuelle que vous sélectionnez. La réservation peut s’appliquer aux tailles des machines virtuelles dans le même groupe de gammes de tailles. Pour plus d’informations, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacité réservée SQL Database : la couverture de remise dépend du niveau de performance que vous choisissez. Pour plus d’informations, consultez [Comprendre comment une remise de réservation Azure est appliquée aux bases de données SQL](billing-understand-reservation-charges.md).
- Capacité réservée Azure Cosmos DB : la couverture de remise dépend du débit provisionné. Pour plus d’informations, consultez [Comprendre comment une remise de réservation Azure Cosmos DB est appliquée](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
    - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
    - [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](/partner-center/azure-reservations)

- En savoir plus sur les réservations de plans de service :
    - [Machines virtuelles avec instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Ressources Azure Cosmos DB avec capacité réservée Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Ressources de calcul SQL Database avec capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md) En savoir plus sur les réservations pour les offres logicielles :
    - [Offres logicielles Red Hat dans Réservations Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Offres logicielles SUSE dans Réservations Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
