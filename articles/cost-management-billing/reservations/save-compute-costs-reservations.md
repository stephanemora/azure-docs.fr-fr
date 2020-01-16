---
title: Qu’est-ce qu’une réservation Azure ?
description: Découvrez les réservations et tarifs Azure pour économiser sur vos machines virtuelles, les bases de données SQL, Azure Cosmos DB et les coûts d’autres ressources.
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 2e8c477aea7a11eefa606ab9cd084ca3ed9f400f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986781"
---
# <a name="what-are-azure-reservations"></a>Qu’est-ce qu’une réservation Azure ?

Les réservations Azure vous permettent d’économiser de l’argent en vous engageant sur un plan d’un ou trois ans de machine virtuelle, stockage Blob Azure ou Azure Data Lake Storage Gen2, de capacité de calcul SQL Database, de débit Azure Cosmos DB ou d’autres ressources Azure. L’engagement vous permet d’obtenir une remise sur les ressources que vous utilisez. Des réservations peuvent réduire sensiblement les coûts de ressources, jusqu’à hauteur de 72 % sur les prix de paiement à l’utilisation. Des réservations permettent de bénéficier d’une remise sur la facturation et n’ont aucune incidence sur l’état de runtime de vos ressources.

Vous pouvez payer une réservation à l’avance ou tous les mois. Une même réservation avec paiement initial et avec paiements mensuels a le même coût total : vous ne payez pas de frais supplémentaires si vous optez pour le paiement mensuel. Le paiement mensuel est disponible pour les réservations Azure, et non pour les produits tiers.

Vous pouvez acheter une réservation sur le [portail Azure](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Pourquoi acheter une réservation ?

Si vous avez des machines virtuelles, des données de stockage Blob, Azure Cosmos DB ou des bases de données SQL qui utilisent une capacité ou un débit important ou qui fonctionnent sur de longues périodes, l’achat d’une réservation est l’option la plus économique. Par exemple, si vous exécutez en permanence quatre instances d’un service sans réservation, vous êtes facturé au tarif du paiement à l’utilisation. Lorsque vous achetez une réservation pour ces ressources, vous bénéficiez immédiatement de la remise sur celle-ci réservation. Les ressources ne sont plus facturées au tarif du paiement à l’utilisation.

## <a name="charges-covered-by-reservation"></a>Frais couverts par la réservation

Plans de service :

- **Instance de machine virtuelle réservée** : une réservation couvre uniquement les coûts de calcul de machine virtuelle. Une réservation ne couvre pas les frais de logiciels, de mise en réseau ou de stockage.
- **Capacité réservée de stockage Azure** : une réservation couvre la capacité de stockage pour les comptes de stockage standard pour le stockage d’objets blob ou le stockage Azure Data Lake Gen2. La réservation ne couvre pas les taux de bande passante ou de transaction.
- **La capacité réservée Azure Cosmos DB** : une réservation couvre le débit provisionné pour vos ressources. Elle ne couvre pas les frais de stockage et de réseau.
- **vCore réservé de SQL Database** : seuls les coûts de calcul sont inclus dans une réservation. La licence est facturée séparément.
- **SQL Data Warehouse** : Une réservation couvre l’utilisation de cDWU. Elle ne couvre pas les frais de stockage ou de mise en réseau associés à l’utilisation de SQL Data Warehouse.
- **Frais de timbre App service** : Une réservation couvre l’utilisation des timbres. Elle ne s’applique pas aux workers, si bien que toutes les autres ressources associées aux timbres sont facturées séparément.
- Azure Database pour MySQL
- Azure Database pour PostgreSQL
- Azure Database for MariaDB
- Explorateur de données Azure
- Disques managés SSD Premium


Pour les machines virtuelles Windows et SQL Database, vous pouvez couvrir les coûts de licence avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Qui peut acheter une réservation ?

Pour acheter un plan, vous devez avoir un rôle de propriétaire dans un abonnement Entreprise (MS-AZR-0017P ou MS-AZR-0148P), Paiement à l’utilisation (MS-AZR-0003P ou MS-AZR-0023P) ou Contrat Client Microsoft. Les fournisseurs de solutions cloud peuvent utiliser le portail Azure ou l’ [Espace partenaires](/partner-center/azure-reservations) pour acheter des réservations Azure.

Les clients Contrat Entreprise (EA) peuvent limiter les achats aux administrateurs EA en désactivant l'option **Ajouter des instances réservées** dans le portail EA. Les administrateurs EA doivent être propriétaires d'au moins un abonnement EA pour acheter une réservation. Cette option est particulièrement utile pour les entreprises souhaitant qu'une équipe centralisée achète des réservations pour différents centres de coûts. Au terme de l'achat, les équipes centralisées peuvent ajouter des propriétaires de centres de coûts aux réservations. Les propriétaires peuvent ensuite étendre la réservation à leurs abonnements. L’équipe centralisée n'est pas tenue de disposer d'un accès de propriétaire d'abonnement là où la réservation est achetée.

Une remise de réservation s’applique uniquement aux ressources associées aux abonnements qui ont été achetés dans le cadre de plans Entreprise, CSP, Contrat Client Microsoft et individuels avec paiement à l’utilisation.

## <a name="scope-reservations"></a>Réservations d’étendue

Vous pouvez étendre une réservation à un groupe de ressources ou à un groupe d’abonnement. La définition de l’étendue d’une réservation sélectionne où les économies de la réservation s’appliquent. Lorsque vous étendez la réservation d’un groupe de ressources, les remises de réservation s’appliquent uniquement au groupe de ressources, pas à l’ensemble de l’abonnement.

### <a name="reservation-scoping-options"></a>Options d’étendue de réservation

Avec l’étendue de réservation des groupes de ressources, vous disposez de trois options pour étendre une réservation, selon vos besoins :

- **Étendue de groupe de ressources unique** : applique la remise de réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.
- **Étendue d’abonnement unique** : applique la remise de réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.
- **Étendue partagée** : applique la remise de réservation aux ressources correspondantes dans les abonnements éligibles inclus dans le contexte de facturation. Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription. Pour les clients avec un Contrat Client Microsoft, l’étendue de facturation correspond au profil de facturation. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.

Lors de l’application des remises de réservation sur votre utilisation, Azure traite la réservation dans l’ordre suivant :

1. Réservations limitées à un groupe de ressources
2. Réservations à étendue unique
3. Réservations à étendue partagée

Un seul groupe de ressources peut obtenir des remises de réservation de plusieurs réservations, en fonction de la façon dont vous étendez vos réservations.

### <a name="scope-a-reservation-to-a-resource-group"></a>Étendue d’une réservation à un groupe de ressources

Vous pouvez étendre la réservation à un groupe de ressources lorsque vous achetez la réservation ou vous définissez l’étendue après l’achat. Vous devez être propriétaire d’un abonnement pour étendre la réservation à un groupe de ressources.

Pour définir l’étendue, accédez à la page [Acheter une réservation](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) sur le portail Azure. Sélectionnez le type de réservation que vous voulez acheter. Dans le formulaire de sélection **Sélectionnez le produit que vous voulez acheter**, remplacez la valeur Étendue par Groupe de ressources unique. Sélectionnez ensuite un groupe de ressources.

![Exemple illustrant la sélection de l’achat de réservation de machine virtuelle](./media/save-compute-costs-reservations/select-product-to-purchase.png)

Les recommandations d’achat pour le groupe de ressources dans la réservation de la machine virtuelle sont présentées. Ces recommandations sont calculées à partir de l’analyse de votre utilisation sur les 30 derniers jours. Une recommandation d’achat est proposée si le coût d’exécution des ressources avec les instances réservées est moins chère que le coût d’exécution des ressources avec le paiement à l’utilisation. Pour plus d’informations sur les recommandations d’achat de réservation, consultez [Obtenir des recommandations d’achat d’instances réservées en fonction de vos habitudes d’utilisation](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour ce faire, accédez à la réservation, cliquez sur **Configuration** et redéfinissez la réservation. La modification de l’étendue de la réservation n’est pas une transaction commerciale. Le terme de votre réservation n’est pas modifié. Pour plus d’informations sur la mise à jour de l’étendue, consultez [Mettre à jour la portée après avoir acheté une réservation](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemple illustrant la modification d’une étendue de réservation](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Surveiller et optimiser l’utilisation de la réservation

Vous pouvez surveiller l’utilisation de la réservation de plusieurs façons : via le portail Azure, via l’API ou via les données d’utilisation. Pour voir toutes les réservations auxquelles vous avez accès, accédez à **Réservations** dans le portail Azure. La grille de réservations affiche le dernier pourcentage d’utilisation enregistré pour la réservation. Cliquez sur la réservation pour voir l’utilisation à long terme de la réservation.

Vous pouvez également obtenir les informations d’utilisation de la réservation avec les [API](reservation-apis.md#see-reservation-usage) et vos [données d’utilisation](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) si vous êtes client Contrat Entreprise ou Contrat Client Microsoft.

Si vous remarquez que l’utilisation de la réservation de votre groupe de ressources est faible, vous pouvez mettre à jour l’étendue de réservation vers un abonnement unique ou le partager dans le contexte de facturation. Vous pouvez également fractionner la réservation et appliquer les réservations qui en résultent à différents groupes de ressources.

### <a name="other-considerations"></a>Autres considérations

Si vous n’avez pas les ressources correspondantes dans un groupe de ressources, la réservation sera sous-utilisée. La réservation ne s’applique pas automatiquement à un autre groupe de ressources ou à un autre abonnement où il existe une faible utilisation.

L’étendue de réservation ne se met automatiquement à jour si vous déplacez le groupe de ressources d’un abonnement à un autre. L’étendue n’est pas mise à jour si vous supprimez le groupe de ressources. Vous devrez [redéfinir la portée de la réservation](manage-reserved-vm-instance.md#change-the-reservation-scope). Sinon, la réservation sera sous-utilisée.

## <a name="discounted-subscription-and-offer-types"></a>Types d'abonnements et d'offres remisés

Les remises de réservation s’appliquent aux types d'abonnements et d'offres éligibles suivants.

- Contrat Entreprise (références de l’offre : MS-AZR-0017P ou MS-AZR-0148P)
- Abonnements avec Contrat Client Microsoft.
- Forfaits individuels avec paiement à l’utilisation (références de l’offre : MS-AZR-0003P ou MS-AZR-0023P)
- Abonnements CSP

Les ressources opérant dans le cadre d’un abonnement relevant d’autres types d’offres ne bénéficient d’aucune remise sur réservation.

## <a name="how-is-a-reservation-billed"></a>Comment une réservation est-elle facturée ?

La réservation est facturée selon le mode de paiement associé à l’abonnement. Le coût de la réservation est déduit de votre solde d’engagement financier, le cas échéant. Lorsque ce solde ne couvre pas le coût des réservations, le dépassement vous est facturé. Si vous avez souscrit un abonnement à partir d’un forfait individuel avec paiement à l’utilisation, la carte de crédit associée à votre compte est facturée immédiatement pour les achats en paiement initial. Les paiements mensuels apparaissent sur votre facture et votre carte de crédit est facturée chaque mois. Lorsque vous réglez sur facture, les frais sont portés sur votre prochaine facture.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

La remise sur réservation s’applique à l’utilisation de ressource correspondant aux attributs que vous sélectionnez lorsque vous achetez la réservation. Les attributs englobent l’étendue dans laquelle les machines virtuelles, les bases de données SQL, Azure Cosmos DB ou d’autres ressources correspondantes s’exécutent. Par exemple, si vous voulez une remise sur réservation pour quatre machines virtuelles Standard D2 de la région USA Ouest, sélectionnez l’abonnement dans le cadre duquel ces machines virtuelles s’exécutent.

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

Par exemple, vous pourrez plus tard créer une ressource et disposer d'une réservation correspondante sous-utilisée. La remise de réservation s’applique automatiquement à la nouvelle ressource correspondante.

Si elles s’exécutent dans différents abonnements au sein de votre compte/inscription, sélectionnez l’étendue alors définie comme partagée. Cette étendue partagée permet d’appliquer la remise de réservation à différents abonnements. Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour plus d’informations, voir [Gérer les réservations Azure](manage-reserved-vm-instance.md).

Une remise de réservation s’applique uniquement aux ressources associées aux types d’abonnements Entreprise, Contrat Client Microsoft, CSP, ou avec paiement à l’utilisation. Les ressources opérant dans le cadre d’un abonnement relevant d’autres types d’offres ne bénéficient d’aucune remise sur réservation.

## <a name="when-the-reservation-term-expires"></a>Expiration du terme de la réservation

À l’issue de la période de réservation, la remise sur facturation cesse de s’appliquer et les ressources sont facturées au tarif du paiement à l’utilisation. Par défaut, les réservations ne sont pas configurées pour se renouveler automatiquement. Vous pouvez choisir d’activer le renouvellement automatique d’une réservation en sélectionnant l’option dans les paramètres de renouvellement. En cas de renouvellement automatique, une réservation de remplacement est achetée à l’expiration de la réservation existante. Par défaut, la réservation de remplacement a les mêmes attributs que la réservation arrivant à expiration. Vous pouvez éventuellement modifier la périodicité de facturation, le terme ou la quantité dans les paramètres de renouvellement. Tout utilisateur disposant d’un accès propriétaire sur la réservation et de l’abonnement utilisé pour la facturation peut configurer le renouvellement.  

## <a name="discount-applies-to-different-sizes"></a>La remise s'applique à différentes tailles

Quand vous achetez une réservation, la remise peut être appliquée à d’autres instances avec des attributs qui se trouvent dans le même groupe de tailles. Cette fonctionnalité est appelée flexibilité de la taille d'instance. La flexibilité de la couverture de remise varie selon le type de réservation et les attributs que vous choisissez quand vous achetez la réservation.

Plans de service :

- Instances de machines virtuelles réservées : Lorsque vous achetez la réservation et sélectionnez **Optimisé pour flexibilité de la taille d’instance**, la couverture de remise dépend de la taille de machine virtuelle que vous sélectionnez. La réservation peut s’appliquer aux tailles des machines virtuelles dans le même groupe de gammes de tailles. Pour plus d’informations, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacité réservée de stockage Azure : Vous pouvez acheter une capacité réservée pour les comptes de stockage Azure standard, en unités de 100 Tio ou 1 PiB par mois. La capacité réservée de stockage Azure est disponible dans toutes les régions pour n’importe quel niveau d’accès (chaud, froid ou archive) et pour toute option de réplication (LRS, GRS ou ZRS).
- Capacité réservée SQL Database : la couverture de remise dépend du niveau de performance que vous choisissez. Pour plus d’informations, consultez [Comprendre comment une remise de réservation Azure est appliquée aux bases de données SQL](understand-reservation-charges.md).
- Capacité réservée Azure Cosmos DB : la couverture de remise dépend du débit provisionné. Pour plus d’informations, consultez [Comprendre comment une remise de réservation Azure Cosmos DB est appliquée](understand-cosmosdb-reservation-charges.md).

## <a name="reservation-notifications"></a>Notifications de réservation

Selon la façon dont vous payez votre abonnement Azure, nous envoyons des notifications de réservation par e-mail aux utilisateurs suivants dans votre organisation. Les notifications sont envoyées pour différents événements, notamment :

- Purchase
- Expiration de la réservation à venir
- Expiry
- Renouvellement
- Annulation
- Étendue modifiée

Pour les clients avec des abonnements EA :
- Une notification d’achat est envoyée à l’acheteur et aux contacts de notification EA.
- Les autres notifications de cycle de vie des réservations sont envoyées uniquement aux contacts de notification EA.
- Les utilisateurs ajoutés à une réservation à l’aide de l’autorisation RBAC (IAM) ne reçoivent pas de notifications par e-mail.

Pour les clients qui ont des abonnements individuels :
- l’acheteur reçoit une notification d’achat.
- Au moment de l’achat, le propriétaire du compte de facturation de l’abonnement reçoit une notification d’achat.
- Le propriétaire du compte reçoit toutes les autres notifications.


## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations Azure, consultez les articles suivants :
    - [Gérer les réservations Azure](manage-reserved-vm-instance.md)
    - [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
    - [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
    - [Coûts des logiciels Windows non inclus dans les réservations](reserved-instance-windows-software-costs.md)
    - [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](/partner-center/azure-reservations)

- En savoir plus sur les réservations de plans de service :
    - [Machines virtuelles avec instances de machines virtuelles réservées Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Ressources Azure Cosmos DB avec capacité réservée Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Ressources de calcul SQL Database avec capacité réservée Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md) En savoir plus sur les réservations pour les offres logicielles :
    - [Offres logicielles Red Hat dans Réservations Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Offres logicielles SUSE dans Réservations Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
