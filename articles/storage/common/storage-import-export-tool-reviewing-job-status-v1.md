---
title: Vérification de l’état des travaux Azure Import/Export - v1 | Microsoft Docs
description: Découvrez comment utiliser les fichiers journaux créés lors de l’exécution du travail d’importation ou d’exportation pour connaître l’état du travail Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 5ab0dd7f0e0ed90c205c37ddbb7ea3a9fca74ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512260"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Vérification de l’état des travaux Azure Import/Export avec les copies des fichiers journaux
Lorsque le service Microsoft Azure Import/Export traite des lecteurs associés à un travail d’importation ou d’exportation, il écrit les fichiers journaux de copie dans le compte de stockage dans ou à partir duquel vous importez ou exportez des objets blob. Le fichier journal contient l’état détaillé de chaque fichier importé ou exporté. L’URL de chaque fichier journal de copie est renvoyée lorsque vous interrogez l’état d’un travail effectué ; consultez la page [Get Job](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) pour plus d’informations.  

## <a name="example-urls"></a>Exemples d’URL

Voici des exemples d’URL de fichiers journaux de copie pour un travail d’importation avec deux lecteurs :  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Consultez la page [Format des fichiers journaux d’activité du Service Import-Export](../storage-import-export-file-format-log.md) pour connaître le format des journaux d’activité de copie et la liste complète des codes d’état.  

## <a name="next-steps"></a>Étapes suivantes

 * [Configuration de l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Réparation d’un travail d’importation](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Réparation d’un travail d’exportation](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Résolution des problèmes associés à l’outil Azure Import-Export](storage-import-export-tool-troubleshooting-v1.md)
