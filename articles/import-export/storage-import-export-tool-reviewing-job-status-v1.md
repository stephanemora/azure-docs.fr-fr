---
title: Examiner les journaux de copie d’importations et d’exportations dans Azure Import/Export | Microsoft Docs
description: Découvrez comment examiner les journaux d’erreurs/de copie à partir d’importations et d’exportations pour la copie de données, ainsi que les problèmes de chargement.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03f4cf19922f808decfd84fe0538930dee807b5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709252"
---
# <a name="review-copy-logs-from-imports-and-exports-via-azure-importexport"></a>Examiner les journaux de copie d’importations et d’exportations via Azure Import/Export
Lorsque le service Microsoft Azure Import/Export traite les lecteurs associés à un travail d’importation ou d’exportation, il écrit les fichiers journaux de copie dans le compte de stockage utilisé pour importer ou exporter des objets blob. 

Le fichier journal contient l’état détaillé de chaque fichier importé ou exporté. 

Le service retourne l’URL de chaque fichier journal de copie lorsque vous interrogez l’état d’un travail terminé. Pour plus d’informations, consultez [Obtenir un travail](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Exemples d’URL

Voici des exemples d’URL de fichiers journaux de copie pour un travail d’importation avec deux lecteurs :  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 <!--See [Import/Export service Log File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-log) for the format of copy logs and the full list of status codes. ARCHIVED-->  

## <a name="next-steps"></a>Étapes suivantes

<!--* [Setting Up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md) ARCHIVED-->
 * [Préparation des disques durs pour un travail d’importation](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
<!--* [Repairing an import job](./storage-import-export-tool-repairing-an-import-job-v1.md)-->  
<!--* [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md)-->