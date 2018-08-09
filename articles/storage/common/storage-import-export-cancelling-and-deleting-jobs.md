---
title: Annulation et suppression d’un travail d’importation Azure Import/Export | Microsoft Docs
description: Découvrez comment annuler et supprimer des travaux pour le service Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: e11cf974a5f33020c889844dd34f51612e13036e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521011"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Annulation et suppression de travaux du service Azure Import/Export

 Pour demander qu’un travail soit annulé avant d’être à l’état `Packaging`, appelez l’opération [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) et définissez l’élément `CancelRequested` sur `true`. Le travail est annulé de manière optimale. Si des données sont en cours de transfert sur les disques, cette opération peut se poursuivre même après que l’annulation ait été demandée.

 Un travail annulé passe à l’état `Completed` et est conservé pendant 90 jours. Après quoi, il est supprimé.

 Pour supprimer un travail, appelez l’opération [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) avant que le travail soit expédié (c’est-à-dire, pendant que le travail est à l’état `Creating`). Vous pouvez également supprimer un travail lorsqu’il se trouve dans l’état `Completed`. Une fois le travail supprimé, ses informations et son état ne sont plus accessibles via l’API REST ou le portail Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
