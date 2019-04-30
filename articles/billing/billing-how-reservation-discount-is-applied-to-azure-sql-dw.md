---
title: Comment les remises de réservation s’appliquent à Azure SQL Data Warehouse | Microsoft Docs
description: Découvrez comment les remises de réservation s’appliquent à Azure SQL Data Warehouse pour aider à réaliser des économies de vous.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918363"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Comment les remises de réservation s’appliquent à Azure SQL Data Warehouse

Une fois que vous achetez la capacité d’Azure SQL Data Warehouse est réservé, la remise de réservation est automatiquement appliquée aux entrepôts de données qui existent dans cette région. La remise de réservation s’applique à l’utilisation émise par le compteur de cDWU SQL Data Warehouse. Mise en réseau et stockage sont facturés au tarif du paiement.

## <a name="reservation-discount-application"></a>Application de réservation de remise

La remise de réservation de capacité de SQL Data Warehouse est appliquée aux entrepôts en cours d’exécution sur une base horaire. Si vous n’avez pas un entrepôt déployé pendant une heure, la capacité réservée est perdue pour cette heure. Il ne s’étend pas.

Après l’achat, la réservation qui vous achetez est mis en correspondance avec l’utilisation de SQL Data Warehouse émise en exécutant des entrepôts à n’importe quel point dans le temps. Si vous arrêtez certains entrepôts, puis réservation remises automatiquement s’appliquent à tous les entrepôts correspondants.

Pour les entrepôts qui n’exécutent pas pour une heure complète, la réservation est automatiquement appliquée à d’autres instances correspondants dans cette heure.

## <a name="discount-examples"></a>Exemples de remises

Les exemples suivants montrent comment la remise de réservation de capacité SQL Data Warehouse s’applique, selon les déploiements.

- **Exemple 1**: Vous achetez 5 unités de capacité de cDWU réservé 100. Vous exécutez une instance DW1500c SQL Data Warehouse pour une heure. Dans ce cas, l’utilisation est émise pour 15 unités de 100 utilisation cDWU. La remise de réservation s’applique aux 5 unités que vous avez utilisé. Vous êtes facturé à l’aide du tarif du paiement pour les 10 unités restantes de l’utilisation de cDWU 100 que vous avez utilisé.

- **Exemple 2 :** Vous achetez 5 unités de capacité de cDWU réservé 100. Vous exécutez deux instances DW100c SQL Data Warehouse pour une heure. Dans ce cas, deux événements d’utilisation sont émis pour 1 unité de 100 utilisation cDWU. Les deux événements d’utilisation bénéficiez de remises de capacité réservée. Les 3 unités restantes de la capacité de cDWU réservé 100 supplémentaires seront gaspillées et n’étend pas pour une utilisation ultérieure.

- **Exemple 3**: Vous achetez 1 unité de capacité de cDWU réservé 100. Vous exécutez deux instances DW100c SQL Data Warehouse. Chacun s’exécute pendant 30 minutes. Dans ce cas, les deux événements d’utilisation bénéficiez de remises de capacité réservée. Aucune utilisation n’est facturée aux tarifs de paiement à l’utilisation.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

- Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Afficher les transactions de réservation](billing-view-reservations.md)
- [Obtenir des opérations de réservation et d’utilisation via des API](billing-reservation-apis.md)
- [Gérer les réservations](billing-manage-reserved-vm-instance.md)
