---
title: Remise de réservation appliquée aux flux de données Azure Data Factory | Microsoft Docs
description: Découvrez comment une remise de réservation est appliquée aux flux de données ADF en cours d’exécution. La remise est appliquée à ces flux de données sur une base horaire.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 6796006bbac8987370bddab979356d5123fab204
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531571"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Comment une remise de réservation est-elle appliquée aux flux de données Azure Data Factory ?

Une fois que vous avez acheté la capacité de réserve pour flux de données ADF, la remise de réservation est appliquée automatiquement aux flux de données à l’aide d’un runtime d’intégration Azure qui correspond au type de calcul et au nombre de cœurs de la réservation.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Ainsi, si vous ne disposez pas des ressources d’intégration Azure correspondantes utilisées pendant une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous arrêtez d’utiliser le runtime d’intégration pour les flux de données, la remise de réservation s’applique automatiquement à une autre ressource correspondante dans l’étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

## <a name="discount-applied-to-adf-data-flows"></a>Remise appliquée aux flux de données ADF

La remise de la capacité de réserve pour flux de données ADF s’applique à l’exécution des runtimes d’intégration sur une base horaire. La réservation que vous achetez est associée au type de calcul utilisé par les runtimes d’intégration pour vos flux de données. Pour les flux de données qui ne s’exécutent pas pendant une heure entière, la réservation est automatiquement appliquée aux autres flux de données qui correspondent aux attributs de la réservation. La remise peut aussi s’appliquer à des flux de données qui s’exécutent simultanément. Si aucun flux de données s’exécutant pendant une heure entière ne correspond aux attributs de la réservation, vous ne bénéficiez pas pleinement de la remise de réservation pour cette heure.

Les exemples suivants montrent comment la remise de capacité de réserve pour flux de données ADF s’applique en fonction du nombre de cœurs achetés et du moment où ils s’exécutent.

- Scénario 1 : Vous achetez une réservation de flux de données ADF pour 1 heure avec 80 cœurs de calcul à mémoire optimisée en entrant 80 comme quantité pour le type de calcul à mémoire optimisée. Vous exécutez un flux de données avec un runtime d’intégration Azure défini sur 144 cœurs à mémoire optimisée pendant une heure. Vous êtes facturé selon la méthode du paiement à l’utilisation pour 64 cœurs d’utilisation du flux de données pendant une heure. Vous bénéficiez de la remise de réservation pour une heure de 80 cœurs d’utilisation à mémoire optimisée.
- Scénario 2 : Vous achetez une réservation de flux de données ADF pour 1 heure avec 32 cœurs de calcul à usage général en entrant 32 comme quantité pour le type de calcul à usage général. Vous déboguez vos flux de données pendant 1 heure à l’aide des 32 cœurs du runtime d’intégration Azure de calcul à usage général. Vous bénéficiez de la remise de réservation pour cette heure entière d’utilisation.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports de facturation d’utilisation, consultez [Comprendre l’utilisation des réservations Azure](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez l’article suivant :

- [Qu’est-ce qu’une réservation Azure ?](../cost-management-billing/reservations/save-compute-costs-reservations.md)