---
title: Annulation et suppression d’un travail d’importation Azure Import/Export | Microsoft Docs
description: Découvrez comment annuler et supprimer des travaux pour le service Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483143"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Annulation et suppression de travaux du service Azure Import/Export

 Pour demander qu’un travail soit annulé avant d’être à l’état `Packaging`, appelez l’opération [Update Job Properties](/rest/api/storageimportexport/jobs) et définissez l’élément `CancelRequested` sur `true`. Le travail est annulé de manière optimale. Si des données sont en cours de transfert sur les disques, cette opération peut se poursuivre même après que l’annulation ait été demandée.

 Un travail annulé passe à l’état `Completed` et est conservé pendant 90 jours. Après quoi, il est supprimé.

 Pour supprimer un travail, appelez l’opération [Delete Job](/rest/api/storageimportexport/jobs) avant que le travail soit expédié (c’est-à-dire, pendant que le travail est à l’état `Creating`). Vous pouvez également supprimer un travail lorsqu’il se trouve dans l’état `Completed`. Une fois le travail supprimé, ses informations et son état ne sont plus accessibles via l’API REST ou le portail Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
