---
title: Guide de suppression réversible Explorateur Stockage Azure | Microsoft Docs
description: Suppression réversible Explorateur Stockage Azure
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 0d097e769bfea8ff9c65921e8f0d851372187a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564901"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Guide de suppression réversible Explorateur Stockage Azure

La suppression réversible permet d’atténuer l’impact de la suppression accidentelle de données critiques. Ce guide vous aidera à comprendre comment tirer parti de cette fonctionnalité dans Explorateur Stockage. Avant de continuer, il est recommandé d’en savoir plus sur la [suppression réversible des blobs](../blobs/soft-delete-blob-overview.md).

## <a name="configuring-delete-retention-policy"></a>Configuration de la stratégie de rétention des suppressions

Vous pouvez configurer la stratégie de rétention des suppressions pour chaque compte de stockage dans Explorateur Stockage. Ouvrez le menu contextuel de n’importe quel nœud « Conteneurs de blobs » sous le compte de stockage et choisissez **Configurer la stratégie de suppression réversible…**

Une nouvelle stratégie peut prendre jusqu’à 30 secondes pour que sa configuration prenne effet. Supprimer des données sans attendre que la nouvelle stratégie prenne effet peut entraîner un comportement inattendu. Explorateur Stockage attend 30 secondes avant de signaler une stratégie correctement configurée dans le journal d’activité.

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>Suppression réversible avec espace de noms hiérarchique

La fonctionnalité de suppression réversible présente des différences fondamentales entre les conteneurs de blobs avec ou sans espace de noms hiérarchique.

Les conteneurs de blobs avec espace de noms hiérarchique ont des répertoires réels. Ces répertoires peuvent également être supprimés de manière réversible. Lorsqu’un répertoire réel est supprimé de manière réversible, tous les blobs ou répertoires actifs qu’il contient deviennent inaccessibles. Ces blobs et répertoires seront récupérés lorsque le répertoire est restauré et supprimés lorsque le répertoire expire. Les blobs ou les répertoires qu’il contient et qui ont déjà été supprimés de manière réversible seront conservés tels quels.

Les conteneurs de blobs sans espace de noms hiérarchique ne permettent pas la coexistence de blobs supprimés de manière réversible et de blobs actifs portant le même nom. En chargeant un blob portant le même nom qu’un blob supprimé de manière réversible, le blob supprimé de manière réversible deviendra un instantané du nouveau blob. Dans un conteneur de blobs avec espace de noms hiérarchique, la même opération fera coexister le blob supprimé de manière réversible avec le nouveau. Les conteneurs de blobs avec espace de noms hiérarchique permettent également la coexistence de plusieurs blobs supprimés de manière réversible portant le même nom.

Chaque blob ou répertoire supprimé de manière réversible dans un conteneur de blobs avec espace de noms hiérarchique possède une propriété `DeletionID`. Cette propriété permet de distinguer les blobs ou les répertoires portant le même nom. Les blobs supprimés de manière réversible dans des conteneurs de blobs sans espace de noms hiérarchique n’ont pas de propriété `DeletionID`.

Les conteneurs de blobs sans espace de noms hiérarchique prennent également en charge le « contrôle de version des blobs ». Si le contrôle de version des blobs est activé, le comportement de certaines opérations, telles que la suppression, change. Pour plus d’informations, consultez [Guide du contrôle de version des blobs d’Explorateur Stockage Azure](./storage-explorer-blob-versioning.md).

## <a name="view-soft-deleted-blobs"></a>Afficher les blobs supprimés de manière réversible

Dans Explorateur Blob, les blobs supprimés de manière réversible sont affichés dans certains contextes d’affichage.

Pour les conteneurs de blobs sans espace de noms hiérarchique, choisissez le contexte d’affichage « Blobs actifs et blobs supprimés de manière réversible » ou « Tous les blobs et blobs sans la version actuelle » pour afficher les blobs supprimés de manière réversible.

Pour les conteneurs de blobs avec espace de noms hiérarchique, choisissez le contexte d’affichage « Blobs actifs et supprimés de manière réversible » ou « Supprimés uniquement » pour afficher les blobs et les répertoires supprimés de manière réversible.

## <a name="delete-blobs"></a>Suppression d’objets blob

Lorsque vous supprimez des blobs ou des répertoires, Explorateur Stockage vérifie la stratégie actuelle de rétention des suppressions du compte de stockage. Une boîte de dialogue de confirmation vous informe alors de ce qui se passe si vous poursuivez l’opération de suppression. Si la suppression réversible est désactivée, vous pouvez l’activer à partir de la boîte de dialogue de confirmation en sélectionnant le bouton **Activer la suppression réversible**.

> [!WARNING]
> Explorateur Stockage peut ne pas voir une nouvelle stratégie de rétention des suppressions si vous venez de l’enregistrer. Il est vivement recommandé d’attendre au moins 30 secondes que la nouvelle stratégie prenne effet avant de supprimer des données.

## <a name="undelete-blobs"></a>Restaurer des blobs

Explorateur Stockage pouvez restaurer des blobs supprimés de manière réversible et par lots.

Pour restaurer des blobs, sélectionnez les blobs supprimés de manière réversible que vous souhaitez restaurer et utilisez l’option **Restaurer → Restaurer la sélection** dans la barre d’outils ou le menu contextuel.

Vous pouvez également restaurer des blobs de manière récursive dans un répertoire. Si un répertoire actif est inclus dans la sélection, Explorateur Stockage y restaurera tous les blobs ou répertoires supprimés de manière réversible.

Dans les conteneurs de blobs avec espace de noms hiérarchique, restaurer un blob échouera si un blob actif portant le même nom existe déjà.

> [!NOTE]
> Les instantanés supprimés de manière réversible ne peuvent être restaurés qu’en restaurant le blob de base. Il n’existe aucun moyen de restaurer des instantanés individuels.

## <a name="undelete-blobs-by-date-range"></a>Restaurer des blobs par plage de dates

Explorateur Stockage vous permet également de restaurer des blobs en fonction de leur date de suppression.

Pour restaurer des blocs par plage de dates, sélectionnez les blobs supprimés de manière réversible que vous souhaitez restaurer et utilisez l’option **Restaurer → Restaurer par date…** dans la barre d’outils ou le menu contextuel.

L’option **Restaurer par date…** fonctionne exactement de la même manière que l’option **Restaurer la sélection**, sauf qu’elle filtrera les blobs ou les répertoires dont la date de suppression se situe en dehors de la plage que vous avez spécifiée.

## <a name="see-also"></a>Voir aussi

- [Guide du contrôle de version des blobs d’Explorateur Stockage Azure](./storage-explorer-blob-versioning.md)
- [Suppression réversible pour les objets blob](../blobs/soft-delete-blob-overview.md)
