---
title: Remise de réservation appliquée aux bases de données Azure SQL Database | Microsoft Docs
description: Découvrez comment une remise de réservation est appliquée aux bases de données Azure SQL Database en cours d'exécution.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f0b093950b7f8fbbfbb0ca8ceb48cb0aa6341e10
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200280"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Comme une remise de réservation est-elle appliquée aux bases de données Azure SQL Database ?

Quand vous achetez une capacité réservée Azure SQL Database, la remise de réservation est automatiquement appliquée aux bases de données SQL qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre les coûts de calcul de votre base de données SQL. Les frais de logiciel, de stockage et de réseau vous sont facturés aux tarifs normaux. Vous pouvez couvrir les coûts de licence pour les bases de données SQL avec [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Pour Azure Reserved Virtual Machine Instances, consultez [Comprendre la remise Azure Reserved VM Instances](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Ainsi, si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

## <a name="discount-applied-to-sql-databases"></a>Remise appliquée aux bases de données SQL

 La remise de capacité réservée SQL Database est appliquée aux bases de données SQL en cours d’exécution sur une base horaire. La réservation que vous achetez est associée au calcul utilisé par les bases de données SQL en cours d’exécution. Pour les bases de données SQL qui ne s’exécutent pas pendant une heure entière, la réservation est automatiquement appliquée aux autres bases de données SQL qui correspondent aux attributs de la réservation. La remise peut être appliquée à des bases de données SQL qui s’exécutent simultanément. Si aucune base de données SQL s’exécutant pendant une heure entière ne correspond aux attributs de la réservation, vous ne bénéficiez pas pleinement de la remise de réservation pour cette heure.

Les exemples suivants montrent comment la remise de capacité réservée SQL Database s’applique en fonction du nombre de cœurs achetés et du moment où ils s’exécutent.

- Scénario 1 : Vous achetez une capacité réservée de la base de données SQL pour une base de données SQL à 8 cœurs. Vous exécutez une base de données SQL à 16 cœurs qui correspond au reste des attributs de la réservation. Vous êtes facturé selon la méthode du paiement à l’utilisation pour les calculs utilisés par la base de données SQL à 8 cœurs. Vous obtenez la remise de réservation pour les calculs utilisés par la base de données SQL à 8 cœurs pendant une heure.

Dans le reste de ces exemples, nous supposons que la capacité réservée SQL Database que vous achetez est pour une base de données SQL à 16 cœurs et que le reste des attributs de réservation correspond aux bases de données SQL en cours d’exécution.

- Scénario 2 : Vous exécutez deux bases de données SQL à 8 cœurs pendant une heure. La remise de réservation pour 16 cœurs est appliquée aux calculs utilisés par les deux bases de données SQL à 8 cœurs.
- Scénario 3 : Vous exécutez une base de données SQL à 16 cœurs de 13 h 00 à 13 h 30. Vous exécutez une autre base de données SQL à 16 cœurs de 13 h 30 à 14 h 00. Les deux sont couvertes par la remise de réservation.
- Scénario 4 : Vous exécutez une base de données SQL à 16 cœurs de 13 h 00 à 13 h 45. Vous exécutez une autre base de données SQL à 16 cœurs de 13 h 30 à 14 h 00. Le prix du chevauchement de 15 minutes vous est facturé selon la méthode du paiement à l’utilisation. La remise de réservation s’applique aux calculs utilisés pendant la période restante.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports de facturation d’utilisation, consultez [Comprendre l’utilisation des réservations Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Gérer les réservations Azure](manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservations pour les abonnements CSP](/partner-center/azure-reservations)
