---
title: Matrice de prise en charge pour la sauvegarde des objets blob Azure
description: Fournit un récapitulatif des limitations et des paramètres de prise en charge lors de la sauvegarde des objets blob Azure.
ms.topic: conceptual
ms.date: 05/20/2021
ms.custom: references_regions
ms.openlocfilehash: 06e6d193ec929961dd4eeb192045a217aefa4ab2
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475098"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Matrice de prise en charge pour la sauvegarde des objets blob Azure

Cet article résume la disponibilité régionale, les scénarios pris en charge et les limitations de la sauvegarde opérationnelle des objets blob.

## <a name="supported-regions"></a>Régions prises en charge

La sauvegarde opérationnelle des blobs est actuellement disponible dans les régions suivantes : Australie Centre, Australie Centre 2, Australie Est, Australie Sud-Est, Brésil Sud, Brésil Sud-Est, Canada Centre, Canada Est, Inde Centre, États-Unis Centre, Asie de l'Est, États-Unis Est, États-Unis Est 2, France Centre, Allemagne Nord, Allemagne Centre-Ouest, Japon Est, Japon Ouest, Corée Centre, Corée Sud, États-Unis Centre Nord, Europe du Nord, Norvège Est, Norvège Ouest, Afrique du Sud Nord, États-Unis Centre-Est, Inde Sud, Suisse Nord, Suisse Ouest, EAU Centre, EAU Nord, Royaume-Uni Sud, Royaume-Uni Ouest, États-Unis Centre-Ouest, Europe Ouest, Inde Ouest, États-Unis Ouest, États-Unis Ouest 2.

## <a name="limitations"></a>Limites

La sauvegarde opérationnelle des objets blob utilise la restauration à un instant dans le passé des objets blob, le contrôle de version des objets blob, la suppression réversible des objets blob, le flux de modification pour les objets blob et le verrou de suppression pour fournir une solution de sauvegarde locale. Les limitations qui s’appliquent à ces fonctionnalités s’appliquent également à la sauvegarde opérationnelle.

**Scénarios pris en charge :** la sauvegarde opérationnelle prend en charge les objets blob de blocs dans les comptes de stockage standard à usage général v2 uniquement. Les comptes ADLS Gen2 ne sont donc pas pris en charge. En outre, les objets blob de pages, les objets blob d’ajout et les objets blob Premium dans votre compte de stockage ne sont pas restaurés et seuls les objets blob de blocs sont restaurés.

**Autres limitations :**

- Si vous avez supprimé un conteneur au cours de la période de conservation, ce conteneur ne sera pas restauré lors de l’opération de restauration à un instant dans le passé. Si vous tentez de restaurer une plage d’objets blob incluant des objets blob dont le conteneur a été supprimé, l’opération de récupération jusqu’à une date et heure échouera. Pour plus d’informations sur la protection des conteneurs contre la suppression, consultez [Suppression réversible pour les conteneurs (préversion)](../storage/blobs/soft-delete-container-overview.md).
- Si un objet blob a été déplacé entre les niveaux chaud et froid pendant la période comprise entre le moment présent et le point de restauration, l’objet blob est restauré à son niveau précédent. La restauration d’objets blob de blocs du niveau archive n’est pas prise en charge. Par exemple, si un objet blob a été déplacé du niveau d’accès chaud au niveau de stockage archive deux jours auparavant et qu’une opération de restauration est effectuée sur un point trois jours auparavant, l’objet blob n’est pas restauré vers le niveau d’accès chaud. Pour restaurer un objet blob archivé, commencez par le déplacer en dehors du niveau archive. Pour plus d’informations, consultez [Réalimenter les données d’objets blob à partir du niveau Archive](../storage/blobs/storage-blob-rehydration.md).
- Un bloc qui a été chargé via [Put Block](/rest/api/storageservices/put-block) ou [Put Block à partir d’une URL](/rest/api/storageservices/put-block-from-url), mais qui n’est pas validé via [Put Block List](/rest/api/storageservices/put-block-list), ne fait pas partie d’un objet blob et n’est donc pas restauré dans le cadre d’une opération de restauration.
- Un objet blob avec un bail actif ne peut pas être restauré. Si un objet blob avec un bail actif est inclus dans la plage d’objets blob à restaurer, l’opération de restauration échoue de façon automatique. Arrêtez tout bail actif avant de démarrer l’opération de restauration.
- Les instantanés ne sont pas créés ou supprimés dans le cadre d’une opération de restauration. Seul l’objet blob de base est restauré à son état précédent.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la sauvegarde opérationnelle des objets blob Azure](blob-backup-overview.md)
