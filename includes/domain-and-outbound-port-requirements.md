---
title: Fichier Include
description: Fichier Include
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596110"
---
| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter aux services de déplacement des données dans Azure Data Factory. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ou `*.frontend.clouddatahub.net` | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. <br>Pour les nouvelles fabriques de données créées, recherchez le nom de domaine complet à partir de votre clé de runtime d’intégration auto-hébergé dont le format est {fabrique_de_données}.{région}.datafactory.azure.net. Pour une ancienne fabrique de données, si vous ne voyez pas le nom de domaine complet dans votre clé d’intégration auto-hébergée, utilisez *.frontend.clouddatahub.net à la place. |
| `download.microsoft.com`    | 443            | Exigé par le runtime d’intégration auto-hébergé pour télécharger les mises à jour. Si vous avez désactivé la mise à jour automatique, vous pouvez ignorer la configuration de ce domaine. |
| `*.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure lorsque vous utilisez la fonctionnalité [copie intermédiaire](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Nécessaire seulement quand vous copiez depuis ou vers Azure SQL Database ou Azure Synapse Analytics ; sinon, facultatif. Utilisez la fonction de copie intermédiaire pour copier des données vers SQL Database ou Synapse Analytics sans ouvrir le port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Nécessaire lorsque vous copiez depuis ou vers Azure Data Lake Store, sinon, facultatif. |
