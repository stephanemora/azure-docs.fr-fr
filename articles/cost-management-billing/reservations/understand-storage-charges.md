---
title: Comprendre comment les remises de réservation sont appliquées aux services de stockage Azure | Microsoft Docs
description: Découvrez comment les remises de capacité de réserve sont appliquées aux ressources de stockage Blob Azure, Azure Files et Azure Data Lake Storage Gen2.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024028"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Comprendre comment les remises de réservation sont appliquées aux services de stockage Azure 
Les services de stockage Azure vous permettent de réaliser des économies sur les coûts de stockage en réservant une capacité. Le stockage Blob Azure, Azure Files et Azure Data Lake Storage Gen 2 prennent en charge les instances de réserve. Une fois que vous avez acheté une capacité de réserve, la remise de réservation est automatiquement appliquée aux ressources de stockage qui correspondent aux conditions de la réservation. La remise de réservation s’applique à la capacité de stockage uniquement. La bande passante et le taux de requête sont facturés au tarif du paiement à l’utilisation.

Pour plus d’informations sur la capacité de réserve du Stockage Blob Azure et d’Azure Data Lake Storage Gen 2, consultez [Optimiser les coûts de stockage d’objets blob avec une capacité de réserve](../../storage/blobs/storage-blob-reserved-capacity.md). Pour plus d’informations sur la capacité de réserve d’Azure Files, consultez [Optimiser les coûts d’Azure Files avec la capacité de réserve](../../storage/files/files-reserve-capacity.md).

Pour obtenir des informations sur les prix des réservations de stockage Blob Azure, consultez [Tarification des objets blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/) et [Tarification Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Pour obtenir des informations sur les prix des réservations de stockage Azure Files, consultez [Tarification Azure Files](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?
La remise de capacité de réserve est appliquée aux ressources de stockage prises en charge sur une base horaire.

La remise de capacité de réserve repose sur le principe de « remise utilisée ou perdue ». Si vous n’avez aucun objet blob de blocs, aucun partage de fichiers Azure ni aucune ressource Azure Data Lake Storage Gen2 qui corresponde aux conditions de la réservation pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous supprimez une ressource, la remise de réservation s’applique automatiquement à une autre ressource correspondante dans l’étendue spécifiée. Si aucune ressource correspondante n’est trouvée dans l’étendue spécifiée, les heures réservées sont perdues.

## <a name="discount-examples"></a>Exemples de remises
Les exemples suivants montrent comment la remise de capacité de réserve s’applique en fonction des déploiements.

Supposons que vous ayez acheté 100 Tio de capacité de réserve dans la région USA Ouest 2 pour une durée de 1 an. Votre réservation s’applique au stockage d’objets blob LRS (stockage localement redondant) au niveau d’accès chaud.

Supposons que le coût de cet exemple de réservation est de 18 540 $. Vous pouvez choisir de payer le montant total à l’avance ou de payer des versements mensuels fixes de 1 545 $ par mois pendant les douze prochains mois.

Pour ces exemples, supposons que vous vous êtes inscrit à un plan de paiement de réservation mensuel. Les scénarios suivants décrivent ce qui se passe si vous sous- ou sur-utilisez votre capacité réservée.

### <a name="underusing-your-capacity"></a>Sous-utilisation de votre capacité
Supposons que, pendant une heure donnée de la période de réservation, vous n’avez utilisé que 80 Tio de votre capacité de réserve de 100 Tio. Les 20 Tio restants ne sont pas appliqués pendant cette heure et ne sont pas reportés.

### <a name="overusing-your-capacity"></a>Sur-utilisation de votre capacité
Supposons que, pendant une heure donnée de la période de réservation, vous avez utilisé 101 Tio de capacité de stockage. La remise de réservation s’applique à 100 Tio de vos données, et le 1 Tio restant est facturé au tarif de paiement à l’utilisation pour cette heure. Si, pendant l’heure suivante, votre utilisation passe à 100 Tio, toute l’utilisation est couverte par la réservation.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter
Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- [Optimiser les coûts de stockage d’objets blob avec une capacité réservée](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Optimiser les coûts d’Azure Files avec la capacité de réserve](../../storage/files/files-reserve-capacity.md)
- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
