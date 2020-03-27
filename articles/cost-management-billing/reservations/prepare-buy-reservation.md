---
title: Préparer l’achat d’une réservation Azure
description: Découvrez les points importants à prendre en compte avant l’achat d’une réservation Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235651"
---
# <a name="prepare-to-buy-a-reservation"></a>Préparer l’achat d’une réservation

Les réservations Azure vous permettent d’économiser en vous engageant sur des plans d’un ou trois ans pour de nombreuses ressources Azure. Avant de vous engager dans l’achat d’une réservation, veillez à consulter les sections suivantes, qui vous permettront de préparer votre achat.

## <a name="who-can-buy-a-reservation"></a>Qui peut acheter une réservation ?

Pour acheter un plan, vous devez avoir un rôle de propriétaire dans un abonnement Entreprise (MS-AZR-0017P ou MS-AZR-0148P), Paiement à l’utilisation (MS-AZR-0003P ou MS-AZR-0023P) ou Contrat Client Microsoft. Les fournisseurs de solutions cloud peuvent utiliser le portail Azure ou l’ [Espace partenaires](/partner-center/azure-reservations) pour acheter des réservations Azure.

Les clients Contrat Entreprise (EA) peuvent limiter les achats aux administrateurs EA en désactivant l'option **Ajouter des instances réservées** dans le portail EA. Les administrateurs EA doivent être propriétaires d'au moins un abonnement EA pour acheter une réservation. Cette option est particulièrement utile pour les entreprises souhaitant qu'une équipe centralisée achète des réservations pour différents centres de coûts. Au terme de l'achat, les équipes centralisées peuvent ajouter des propriétaires de centres de coûts aux réservations. Les propriétaires peuvent ensuite étendre la réservation à leurs abonnements. L’équipe centralisée n'est pas tenue de disposer d'un accès de propriétaire d'abonnement là où la réservation est achetée.

Une remise de réservation s’applique uniquement aux ressources associées aux abonnements qui ont été achetés dans le cadre de plans Entreprise, CSP, Contrat Client Microsoft et individuels avec paiement à l’utilisation.

## <a name="scope-reservations"></a>Réservations d’étendue

Vous pouvez étendre une réservation à un groupe de ressources ou à un groupe d’abonnement. La définition de l’étendue d’une réservation sélectionne où les économies de la réservation s’appliquent. Quand vous limitez l’étendue de la réservation à un groupe de ressources, les remises sur réservation s’appliquent uniquement au groupe de ressources et non à l’ensemble de l’abonnement.

### <a name="reservation-scoping-options"></a>Options d’étendue de réservation

Vous disposez de trois options pour définir l’étendue d’une réservation, selon vos besoins :

- **Étendue de groupe de ressources unique** : applique la remise sur réservation aux ressources correspondantes incluses dans le groupe de ressources sélectionné uniquement.
- **Étendue d’abonnement unique** : applique la remise sur réservation aux ressources correspondantes incluses dans l’abonnement sélectionné.
- **Étendue partagée** : applique la remise sur réservation aux ressources correspondantes des abonnements éligibles inclus dans le contexte de facturation. Pour les clients Contrat Entreprise, le contexte de facturation correspond à l’inscription. Pour les clients avec un Contrat Client Microsoft, l’étendue de facturation correspond au profil de facturation. Pour les abonnements individuels utilisant les tarifs du paiement à l’utilisation, l’étendue de facturation correspond à tous les abonnements éligibles créés par l’administrateur de compte.

Lors de l’application des remises de réservation sur votre utilisation, Azure traite la réservation dans l’ordre suivant :

1. Réservations limitées à un groupe de ressources
2. Réservations à étendue unique
3. Réservations à étendue partagée

Un seul groupe de ressources peut obtenir des remises de réservation de plusieurs réservations, en fonction de la façon dont vous étendez vos réservations.

Vous pouvez modifier l’étendue après l’achat d’une réservation. Pour ce faire, accédez à la réservation, cliquez sur **Configuration** et redéfinissez la réservation. La modification de l’étendue de la réservation n’est pas une transaction commerciale. Le terme de votre réservation n’est pas modifié. Pour plus d’informations sur la mise à jour de l’étendue, consultez [Mettre à jour la portée après avoir acheté une réservation](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Exemple illustrant la modification d’une étendue de réservation](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Acheter des réservations

Vous pouvez acheter des réservations à partir du portail Azure, d’API, de PowerShell et de l’interface CLI. Quand vous êtes prêt à acheter une réservation, lisez les articles s’appliquant à votre situation :

- [App Service](prepay-app-service-isolated-stamp.md)
- [Cache Azure pour Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Explorateur de données](../../data-explorer/pricing-reserved-capacity.md)
- [Stockage sur disque](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Hôte dédié](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Abonnements logiciels](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Stockage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Base de données SQL](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Machines virtuelles](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les réservations pour les ressources Azure](manage-reserved-vm-instance.md)
