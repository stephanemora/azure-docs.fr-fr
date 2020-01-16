---
title: Utilisation de l’outil Azure Import/Export - v1 | Microsoft Docs
description: Découvrez comment utiliser l’outil d’importation/exportation pour préparer les disques durs pour un travail d’importation, réparer un travail d’importation ou un travail d’exportation.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 1/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: aaceee65ae91a22dc658d185a874e2040b1e0f04
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975664"
---
# <a name="using-the-azure-importexport-tool-classic-deployment-model"></a>Utilisation de l’outil Azure Import/Export (modèle de déploiement classique)

L’outil Azure Import/Export (WAImportExport.exe) sert à créer et gérer des travaux pour le service Azure Import/Export, ce qui vous permet de transférer de grandes quantités de données vers ou à partir du Stockage Blob Azure.

Cette documentation vaut pour le modèle de déploiement classique de l’outil Azure Import/Export. Pour plus d’informations sur l’utilisation de la version la plus récente de l’outil, consultez [Utilisation de l’outil Azure Import/Export](../storage-import-export-tool-how-to.md).

Les articles suivants vous montrent comment :

- Installer et configurer l’outil Import/Export.
- Préparer vos disques durs pour un travail dans lequel vous importez des données à partir de vos disques vers le Stockage Blob Azure.
- Vérifier l’état d’un travail avec les fichiers journaux de copie.
- Réparer un travail d’importation.
- Réparer un travail d’exportation.
- Résoudre les problèmes de l’outil Azure Import/Export, au cas où vous rencontrez un problème lors d’un processus.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration de l’outil WAImportExport](../storage-import-export-tool-how-to.md)
