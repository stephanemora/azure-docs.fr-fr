---
title: Fichier include
description: Fichier Include
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 44cd654bf9a4dddf32ef2bee44575622b3aa2563
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108760277"
---
| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Requis par le runtime d’intégration auto-hébergé pour la création interactive. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. <br>Pour les nouvelles fabriques de données créées, recherchez le nom de domaine complet à partir de votre clé de runtime d’intégration auto-hébergé dont le format est {fabrique_de_données}.{région}.datafactory.azure.net. Pour une ancienne fabrique de données, si vous ne voyez pas le nom de domaine complet dans votre clé d’intégration auto-hébergée, utilisez *.frontend.clouddatahub.net à la place. |
| `download.microsoft.com`    | 443            | Exigé par le runtime d’intégration auto-hébergé pour télécharger les mises à jour. Si vous avez désactivé la mise à jour automatique, vous pouvez ignorer la configuration de ce domaine. |
| `*.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure lorsque vous utilisez la fonctionnalité [copie intermédiaire](../copy-activity-performance.md#staged-copy). |
| `*.database.windows.net`      | 1433           | Nécessaire seulement quand vous copiez depuis ou vers Azure SQL Database ou Azure Synapse Analytics ; sinon, facultatif. Utilisez la fonctionnalité de copie intermédiaire pour copier des données vers SQL Database ou Azure Synapse Analytics sans ouvrir le port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Nécessaire lorsque vous copiez depuis ou vers Azure Data Lake Store, sinon, facultatif. |
