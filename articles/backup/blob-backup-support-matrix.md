---
title: Matrice de prise en charge pour la sauvegarde des objets blob Azure
description: Fournit un récapitulatif des limitations et des paramètres de prise en charge lors de la sauvegarde des objets blob Azure.
ms.topic: conceptual
ms.date: 10/07/2021
ms.custom: references_regions
ms.openlocfilehash: d0b524ba2865aa72f6fce88e326fffde435f71c3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714156"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Matrice de prise en charge pour la sauvegarde des objets blob Azure

Cet article résume la disponibilité régionale, les scénarios pris en charge et les limitations de la sauvegarde opérationnelle des objets blob.

## <a name="supported-regions"></a>Régions prises en charge

La sauvegarde opérationnelle des blobs est disponible dans toutes les régions du cloud public, à l’exception des régions France Sud et Afrique du Sud Ouest. Elle est également disponible dans les régions de cloud souverain, c’est-à-dire toutes les régions Azure Government et les régions de la Chine (sauf la région Chine Est).

## <a name="limitations"></a>Limites

La sauvegarde opérationnelle des objets blob utilise la restauration à un instant dans le passé des objets blob, le contrôle de version des objets blob, la suppression réversible des objets blob, le flux de modification pour les objets blob et le verrou de suppression pour fournir une solution de sauvegarde locale. Les limitations qui s’appliquent à ces fonctionnalités s’appliquent également à la sauvegarde opérationnelle.

**Scénarios pris en charge :** la sauvegarde opérationnelle prend en charge les objets blob de blocs dans les comptes de stockage standard à usage général v2 uniquement. Les comptes de stockage avec un espace de noms hiérarchique activé (c’est-à-dire, les comptes ADLS Gen2) ne sont pas pris en charge.   <br><br>   En outre, les objets blob de pages, les objets blob d’ajout et les objets blob Premium dans votre compte de stockage ne sont pas restaurés et seuls les objets blob de blocs sont restaurés.

**Autres limitations :**

- Si vous avez supprimé un conteneur au cours de la période de conservation, ce conteneur ne sera pas restauré lors de l’opération de restauration à un instant dans le passé. Si vous tentez de restaurer une plage d’objets blob incluant des objets blob dont le conteneur a été supprimé, l’opération de récupération jusqu’à une date et heure échouera. Pour plus d’informations sur la protection des conteneurs contre la suppression, consultez [Suppression réversible pour les conteneurs (préversion)](../storage/blobs/soft-delete-container-overview.md).
- Si un objet blob a été déplacé entre les niveaux chaud et froid pendant la période comprise entre le moment présent et le point de restauration, l’objet blob est restauré à son niveau précédent. La restauration d’objets blob de blocs du niveau archive n’est pas prise en charge. Par exemple, si un objet blob a été déplacé du niveau d’accès chaud au niveau de stockage archive deux jours auparavant et qu’une opération de restauration est effectuée sur un point trois jours auparavant, l’objet blob n’est pas restauré vers le niveau d’accès chaud. Pour restaurer un objet blob archivé, commencez par le déplacer en dehors du niveau archive. Pour plus d’informations, consultez [Réalimenter les données d’objets blob à partir du niveau Archive](../storage/blobs/archive-rehydrate-overview.md).
- Un bloc qui a été chargé via [Put Block](/rest/api/storageservices/put-block) ou [Put Block à partir d’une URL](/rest/api/storageservices/put-block-from-url), mais qui n’est pas validé via [Put Block List](/rest/api/storageservices/put-block-list), ne fait pas partie d’un objet blob et n’est donc pas restauré dans le cadre d’une opération de restauration.
- Un objet blob avec un bail actif ne peut pas être restauré. Si un objet blob avec un bail actif est inclus dans la plage d’objets blob à restaurer, l’opération de restauration échoue de façon automatique. Arrêtez tout bail actif avant de démarrer l’opération de restauration.
- Les instantanés ne sont pas créés ou supprimés dans le cadre d’une opération de restauration. Seul l’objet blob de base est restauré à son état précédent.
- Si les objets blob restaurés incluent des [objets blob immuables](../storage/blobs/immutable-storage-overview.md#about-immutable-storage-for-blobs), ces derniers ne sont pas restaurés à l’état au point de récupération sélectionné. Toutefois, les autres objets blob pour lesquels l’immuabilité n’est pas activée seront restaurés comme prévu au point de récupération sélectionné.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la sauvegarde opérationnelle des objets blob Azure](blob-backup-overview.md)