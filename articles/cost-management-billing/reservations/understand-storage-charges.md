---
title: Comprendre comment la remise de réservation est appliquée à Stockage Azure | Microsoft Docs
description: Découvrez comment la remise de capacité réservée de Stockage Azure est appliquée à l’objet blob de blocs et aux ressources Azure Data Lake Storage Gen2.
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: 40fba61b173979fb9362de73a87d6192aac9941d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986677"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Comprendre comment la remise de réservation est appliquée à Stockage Azure

Une fois que vous avez acheté une capacité réservée de Stockage Azure, la remise de réservation est automatiquement appliquée à l’objet blob de blocs et aux ressources Azure Data Lake Storage Gen2 qui correspondent aux conditions de la réservation. La remise de réservation s’applique à la capacité de stockage uniquement. La bande passante et le taux de requête sont facturés au tarif du paiement à l’utilisation.

Pour plus d’informations sur la capacité réservée de Stockage Azure, consultez [Optimiser les coûts de stockage d’objets blob avec une capacité réservée](../../storage/blobs/storage-blob-reserved-capacity.md).

Pour plus d’informations sur la tarification de la réservation de stockage Azure, consultez [Tarification des objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/) et [Tarification Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

La remise de capacité réservée de Stockage Azure est appliquée à l’objet blob de blocs et aux ressources Azure Data Lake Storage Gen2 sur une base horaire.

La remise de capacité réservée de Stockage Azure repose sur le principe de « capacité utilisée ou perdue ». Si vous n’avez aucun objet blob de blocs ou ressource Azure Data Lake Storage Gen2 qui corresponde aux conditions de la réservation pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous supprimez une ressource, la remise de réservation s’applique automatiquement à une autre ressource correspondante dans l’étendue spécifiée. Si aucune ressource correspondante n’est trouvée dans l’étendue spécifiée, les heures réservées sont perdues.

## <a name="discount-examples"></a>Exemples de remises

Les exemples suivants montrent comment la remise de capacité réservée de Stockage Azure s’applique en fonction des déploiements.

Supposons que vous avez acheté 100 To de capacité réservée dans la région USA Ouest 2 pour un délai de 1 an. Votre réservation s’applique au stockage localement redondant (LRS) dans le niveau d’accès chaud.

Supposons que le coût de cet exemple de réservation est de 18 540 $. Vous pouvez choisir de payer le montant total à l’avance ou de payer des versements mensuels fixes de 1 545 $ par mois pendant les douze prochains mois.

Pour ces exemples, supposons que vous vous êtes inscrit à un plan de paiement de réservation mensuel. Les scénarios suivants décrivent ce qui se passe si vous sous- ou sur-utilisez votre capacité réservée.

### <a name="underusing-your-capacity"></a>Sous-utilisation de votre capacité

Supposons que, pendant une heure donnée de la période de réservation, vous n’avez utilisé que 80 To de votre capacité réservée de 100 To. Les 20 To restants ne sont pas appliqués pendant cette heure et ne sont pas reportés.

### <a name="overusing-your-capacity"></a>Sur-utilisation de votre capacité

Supposons que, pendant une heure donnée de la période de réservation, vous avez utilisé 101 To de capacité de stockage. La remise de réservation s’applique à 100 To de vos données, et le 1 To restant est facturé au tarif de paiement à l’utilisation pour cette heure. Si, pendant l’heure suivante, votre utilisation passe à 100 To, toute l’utilisation est couverte par la réservation.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Optimiser les coûts de stockage d’objets blob avec une capacité réservée](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
