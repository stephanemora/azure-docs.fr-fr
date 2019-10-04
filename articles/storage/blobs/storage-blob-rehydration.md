---
title: Réalimenter les données d’objets blob à partir du niveau Archive
description: Réalimentez vos objets blob à partir du stockage d’archive pour pouvoir accéder aux données.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68957506"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Réalimenter les données d’objets blob à partir du niveau Archive

Lorsqu’un objet blob se trouve dans le niveau d’accès Archive, il est considéré comme étant hors connexion et ne peut être lu ni modifié. Les métadonnées de l’objet blob restent en ligne et disponible, ce qui vous permet de répertorier l’objet blob et ses propriétés. La lecture et la modification des données d’objets blob sont uniquement disponibles avec des niveaux en ligne tels que chaud ou froid. Il existe deux options pour récupérer et accéder aux données stockées dans le niveau d’accès Archive.

1. [Réalimenter un objet blob archivé dans un niveau en ligne](#rehydrate-an-archived-blob-to-an-online-tier) - Réalimenter un objet blob archivé dans un niveau chaud ou froid en modifiant son niveau à l'aide de l’opération [Définir le niveau du blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier).
2. [Copier un objet blob archivé dans un niveau en ligne](#copy-an-archived-blob-to-an-online-tier) - Créer une copie d’un objet blob archivé à l’aide de l’opération [Copier le blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob). Spécifiez un autre nom d’objet blob et un niveau de destination chaud ou froid.

 Pour plus d’informations sur les niveaux, consultez [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Réalimenter un objet blob archivé dans un niveau en ligne

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copier un objet blob archivé dans un niveau en ligne

Si vous ne souhaitez pas réalimenter un objet blob, vous pouvez choisir d’effectuer une opération [Copier le blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob). Votre objet blob d’origine reste inchangé dans le niveau archive pendant que vous travaillez sur le nouvel objet blob dans le niveau chaud ou froid. Vous pouvez définir la propriété optionnelle *x-ms-rehydrate-priority* sur Standard ou Haut (préversion) lorsque vous utilisez le processus de copie.

Les objets blob du niveau archive peuvent uniquement être copiés vers des niveaux de destination en ligne. La copie d’un objet blob du niveau archive vers un autre objet blob du même niveau n’est pas prise en charge.

La copie d’un objet blob depuis le niveau Archive prend du temps. En arrière-plan, l’opération **Copier le blob** réalimente temporairement votre objet blob source du niveau Archive pour créer un objet blob en ligne dans le niveau de destination. Ce nouvel objet blob est indisponible tant que la réalimentation temporaire depuis le niveau Archive n’est pas terminée et que les données sont écrites dans le nouvel objet blob.

## <a name="pricing-and-billing"></a>Tarification et facturation

La réalimentation d’objets blob hors du niveau archive vers les niveaux chaud ou froid est facturée comme une opération de lecture et de récupération des données. L’utilisation de la haute priorité (préversion) a des coûts d’exploitation et de récupération des données plus élevés par rapport à la priorité standard. La réalimentation à haute priorité apparaît sous la forme d’un élément de ligne distinct sur votre facture. Si une demande de priorité élevée pour retourner un objet blob archive de quelques gigaoctets prend plus de 5 heures, le taux de récupération haute priorité n’est pas facturé. Toutefois, les tarifs de récupération standard s’appliquent toujours.

La copie d’objets blob depuis le niveau archive vers les niveaux chaud ou froid est facturée comme une opération de lecture et de récupération des données. Une opération d’écriture est facturée pour la création de la nouvelle copie. Aucun frais de suppression précoce n’est appliqué lorsque vous copiez vers un objet blob en ligne car l’objet blob source reste inchangé dans le niveau archive. Des frais de priorité élevée s’appliquent.

Les objets blob dans le niveau Archive doivent être stockés pendant un minimum de 180 jours. La suppression ou la réactivation d’objets blob archivés avant 180 jours entraînent des frais de suppression précoce.

> [!NOTE]
> Pour plus d’informations sur la tarification de la réalimentation des données et des objets blob de blocs, consultez [Présentation de la tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les niveaux de Stockage Blob](storage-blob-storage-tiers.md)
* [Vérifier la tarification du niveau chaud, froid et archive dans les comptes de stockage d’objets blob et GPv2 par région](https://azure.microsoft.com/pricing/details/storage/)
* [Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)
* [Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)
