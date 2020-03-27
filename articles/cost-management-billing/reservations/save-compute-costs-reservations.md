---
title: Qu’est-ce qu’une réservation Azure ?
description: Découvrez les réservations et tarifs Azure pour économiser sur vos machines virtuelles, les bases de données SQL, Azure Cosmos DB et les coûts d’autres ressources.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235635"
---
# <a name="what-are-azure-reservations"></a>Qu’est-ce qu’une réservation Azure ?

Les réservations Azure vous permettent d’économiser en vous engageant sur des plans d’un ou trois ans pour plusieurs produits. L’engagement vous permet d’obtenir une remise sur les ressources que vous utilisez. Des réservations peuvent réduire sensiblement les coûts de ressources, jusqu’à hauteur de 72 % sur les prix de paiement à l’utilisation. Des réservations permettent de bénéficier d’une remise sur la facturation et n’ont aucune incidence sur l’état de runtime de vos ressources. Quand vous achetez une réservation, la remise s’applique automatiquement aux ressources correspondantes.

Vous pouvez payer une réservation à l’avance ou tous les mois. Une même réservation avec paiement initial et avec paiements mensuels a le même coût total : vous ne payez pas de frais supplémentaires si vous optez pour le paiement mensuel. Le paiement mensuel est disponible pour les réservations Azure, et non pour les produits tiers.

Vous pouvez acheter une réservation sur le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Pourquoi acheter une réservation ?

Si vous utilisez les ressources d’une façon systématique adaptée aux réservations, l’achat d’une réservation vous offre l’opportunité de réduire vos coûts. Par exemple, si vous exécutez en permanence des instances d’un service sans réservation, vous êtes facturé au tarif du paiement à l’utilisation. Quand vous achetez une réservation, vous bénéficiez immédiatement de la remise sur réservation. Les ressources ne sont plus facturées au tarif du paiement à l’utilisation.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

Après l’achat de la réservation, la remise sur réservation s’applique automatiquement à l’utilisation de ressources correspondant aux attributs que vous avez sélectionnés au moment de l’achat. Les attributs incluent la référence SKU, les régions (le cas échéant) et l’étendue. L’étendue de réservation détermine où les économies de la réservation s’appliquent.

Pour plus d’informations sur la façon dont la remise est appliquée, consultez [Application de la remise sur les instances réservées](reservation-discount-application.md).

Pour plus d’informations sur le fonctionnement de l’étendue de réservation, consultez [Réservations d’étendue](prepare-buy-reservation.md#scope-reservations).


## <a name="flexibility-with-azure-reservations"></a>Flexibilité des réservations Azure

Les réservations Azure offrent la flexibilité nécessaire pour répondre à vos besoins en constante évolution. Vous pouvez échanger une réservation pour une autre réservation du même type. Vous pouvez également obtenir le remboursement d’une réservation (jusqu’à 50 000 USD sur une période glissante de 12 mois) si vous n’en avez plus besoin. La limite maximale du remboursement s’applique à toutes les réservations dans le cadre de votre contrat avec Microsoft.

Pour plus d’informations, consultez [Échanges et remboursements en libre-service pour les réservations Azure](exchange-and-refund-azure-reservations.md).


## <a name="charges-covered-by-reservation"></a>Frais couverts par la réservation

- **Instance de machine virtuelle réservée** : une réservation couvre uniquement les coûts de calcul de machine virtuelle. Une réservation ne couvre pas les frais de logiciels, de réseau, de stockage ou liés à Windows.
- **Capacité réservée de stockage Azure** : une réservation couvre la capacité de stockage pour les comptes de stockage standard pour le stockage d’objets blob ou le stockage Azure Data Lake Gen2. La réservation ne couvre pas les taux de bande passante ou de transaction.
- **La capacité réservée Azure Cosmos DB** : une réservation couvre le débit provisionné pour vos ressources. Elle ne couvre pas les frais de stockage et de réseau.
- **vCore réservé de SQL Database** : seuls les coûts de calcul sont inclus dans une réservation. La licence SQL est facturée séparément.
- **SQL Data Warehouse** : Une réservation couvre l’utilisation de cDWU. Elle ne couvre pas les frais de stockage ou de mise en réseau associés à l’utilisation de SQL Data Warehouse.
- **Azure Databricks** : une réservation couvre uniquement l’utilisation de DBU. Les autres frais, comme le calcul, le stockage et le réseau, sont facturés séparément.
- **Frais de timbre App service** : Une réservation couvre l’utilisation des timbres. Elle ne s’applique pas aux workers, si bien que toutes les autres ressources associées aux timbres sont facturées séparément.
- **Azure Database pour MySQL** : seuls les coûts de calcul sont inclus dans une réservation. Une réservation ne couvre pas les frais de logiciels, de réseau ou de stockage qui sont associés au serveur de base de données MySQL.
- **Azure Database pour PostgreSQL** : seuls les coûts de calcul sont inclus dans une réservation. Une réservation ne couvre pas les frais de logiciels, de réseau ou de stockage qui sont associés aux serveurs de base de données PostgreSQL.
- **Azure Database pour MariaDB** : seuls les coûts de calcul sont inclus dans une réservation. Une réservation ne couvre pas les frais de logiciels, de réseau ou de stockage qui sont associés au serveur de base de données MariaDB.
- **Azure Data Explorer** : une réservation couvre les frais de majoration. Une réservation ne couvre pas les frais de calcul, de stockage ou de réseau associés à l’utilisation des clusters.
- **Azure Cache pour Redis** : seuls les coûts de calcul sont inclus dans une réservation. Une réservation ne couvre pas les frais de stockage ou de réseau associés aux instances de cache Redis.
- **Azure Dedicated Host** : seuls les coûts de calcul sont inclus avec l’hôte dédié.
- **Réservations de Stockage sur disque Azure** : une réservation couvre uniquement les disques SSD Premium de taille P30 ou supérieure. Elle ne couvre pas les autres types de disque ou les tailles inférieures à P30.

Abonnements logiciels :

- **SUSE Linux** : une réservation couvre les coûts de l’abonnement logiciel. Les remises s’appliquent uniquement aux compteurs SUSE et non à l’utilisation de la machine virtuelle.
- **Offres Red Hat** : une réservation couvre les coûts de l’abonnement logiciel. Les remises s’appliquent uniquement aux compteurs RedHat et non à l’utilisation de la machine virtuelle.
- **Azure VMware Solution by CloudSimple** : une réservation couvre les nœuds VMWare CloudSimple. Des coûts logiciels supplémentaires sont toujours facturés.
- **Azure Red Hat OpenShift** : une réservation s’applique aux coûts OpenShift, et non aux coûts d’infrastructure Azure.

Pour les machines virtuelles Windows et SQL Database, la remise sur réservation ne s’applique pas aux coûts logiciels. Vous pouvez couvrir les coûts de licence avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).


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
