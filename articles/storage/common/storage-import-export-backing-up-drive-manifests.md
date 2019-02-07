---
title: Sauvegarde de manifestes de lecteur Azure Import/Export | Microsoft Docs
description: Découvrez comment sauvegarder automatiquement vos manifestes de lecteur pour le service Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694344"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Sauvegarde de manifestes de lecteur pour les travaux Azure Import/Export

Les manifestes de lecteur peuvent être automatiquement sauvegardés vers des objets blob en définissant la propriété `BackupDriveManifest` sur `true` dans les opérations [Put Job](/rest/api/storageimportexport/jobs) ou [Update Job Properties](/rest/api/storageimportexport/jobs) de l’API REST. Par défaut, les manifestes de lecteur ne sont pas sauvegardés. Les sauvegardes de manifestes de lecteur sont stockées en tant qu’objets blob de blocs dans un conteneur au sein du compte de stockage associé au travail. Par défaut, le nom du conteneur est `waimportexport`, mais vous pouvez spécifier un autre nom dans la propriété `DiagnosticsPath` lors de l’appel des opérations `Put Job` ou `Update Job Properties`. Les objets blob de manifeste sauvegardés sont nommés selon le format suivant : `waies/jobname_driveid_timestamp_manifest.xml`.

 Vous pouvez récupérer l’URI des manifestes de lecteur sauvegardés pour un travail en appelant l’opération [Get Job](/rest/api/storageimportexport/jobs). L’URI de l’objet blob est retourné dans la propriété `ManifestUri` pour chaque lecteur.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
