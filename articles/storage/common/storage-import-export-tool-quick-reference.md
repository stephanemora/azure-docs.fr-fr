---
title: Aide-mémoire sur les commandes de travail d’importation pour l’outil Azure Import/Export | Microsoft Docs
description: Référence de l’outil Azure Import/Export pour les commandes de travail d’importation fréquemment utilisées.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453584"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Aide-mémoire sur les commandes fréquemment utilisées pour les travaux d’importation

Cet article fournit un aide-mémoire pour certaines commandes fréquemment utilisées. Pour une utilisation détaillée, consultez [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Première session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Deuxième session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Annuler la dernière session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Reprise de la dernière session interrompue

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Ajouter des disques à la dernière session

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Étapes suivantes

* [Exemple de workflow pour préparer des disques durs à un travail d’importation](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
