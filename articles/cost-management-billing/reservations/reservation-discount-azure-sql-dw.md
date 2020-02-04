---
title: Description de l’application de remises de réservation à Azure SQL Data Warehouse | Microsoft Docs
description: Découvrez dans quelle mesure l’application de remises de réservation à Azure SQL Data Warehouse peut vous aider à réaliser des économies.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 24a673dc7949d5ce05aa1a701cc8b939a766fd99
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76313924"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Description de l’application de remises de réservation à Azure SQL Data Warehouse

Une fois que vous avez acheté une capacité réservée Azure SQL Data Warehouse, la remise de réservation est automatiquement appliquée aux entrepôts de données qui se trouvent dans la région concernée. La remise de réservation s’applique à l’utilisation émise par le compteur de cDWU SQL Data Warehouse. Les opérations liées au stockage et au réseau sont facturées au tarif de paiement à l’utilisation.

## <a name="reservation-discount-application"></a>Application de la remise de réservation

La remise de capacité réservée SQL Data Warehouse est appliquée aux entrepôts de données en cours d’exécution sur une base horaire. Si vous n’avez pas d’entrepôt déployé pendant une heure, la capacité réservée est perdue pour cette heure. Elle n’est pas reconduite.

Après l’achat, la réservation que vous achetez est mise en correspondance avec l’utilisation de SQL Data Warehouse émise par les entrepôts en cours d’exécution à n’importe quel point dans le temps. Si vous arrêtez certains entrepôts, les remises de réservation s’appliquent automatiquement à tous les autres entrepôts correspondants.

Pour les entrepôts qui ne s’exécutent pas pendant une heure complète, la réservation est automatiquement appliquée aux autres instances correspondantes dans cette heure.

## <a name="discount-examples"></a>Exemples de remises

Les exemples suivants montrent comment la remise de capacité réservée SQL Data Warehouse s’applique en fonction des déploiements.

- **Exemple 1** : vous achetez 5 unités de capacité réservée de 100 cDWU. Vous exécutez une instance SQL Data Warehouse DW1500c pendant une heure. Dans ce cas, l’utilisation émise correspond à 15 unités de 100 cDWU. La remise de réservation s’applique aux 5 unités que vous avez utilisées. Vous êtes facturé au tarif de paiement à l’utilisation pour les 10 unités restantes de 100 cDWU que vous avez utilisées. En d'autres termes, une couverture partielle est possible pour les réservations multiples.

- **Exemple 2 :** vous achetez 5 unités de capacité réservée de 100 cDWU. Vous exécutez deux instances SQL Data Warehouse DW100c pendant une heure. Dans ce cas, deux événements d’utilisation sont émis pour 1 unité de 100 cDWU. Les deux événements d’utilisation bénéficient de remises de capacité réservée. Les 3 unités restantes de capacité réservée de 100 cDWU sont perdues et ne sont pas reconduites. En d'autres termes, une seule réservation peut être mise en correspondance avec plusieurs instances de SQL Data Warehouse.

- **Exemple 3** : vous achetez 1 unité de capacité réservée de 100 cDWU. Vous exécutez deux instances SQL Data Warehouse DW100c. Chacune s’exécute pendant 30 minutes. Dans ce cas, les deux événements d’utilisation bénéficient de remises de capacité réservée. Aucune utilisation n’est facturée au tarif de paiement à l’utilisation.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

- Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
- [Afficher les transactions de réservation](view-reservations.md)
- [Obtenir des transactions de réservation et utilisation par le biais d’une API](reservation-apis.md)
- [Gérer les réservations](manage-reserved-vm-instance.md)
