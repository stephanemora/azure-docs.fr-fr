---
title: Fichier Include
description: Fichier Include
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559297"
---
| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter aux services de déplacement des données dans Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. |
| `download.microsoft.com`    | 443            | Exigé par le runtime d’intégration auto-hébergé pour télécharger les mises à jour. Si vous avez désactivé la mise à jour automatique, vous pouvez ignorer la configuration de ce domaine. |
| `*.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure lorsque vous utilisez la fonctionnalité [copie intermédiaire](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy). |
| `*.database.windows.net`      | 1433           | Nécessaire lorsque vous copiez depuis ou vers Azure SQL Database ou Azure SQL Data Warehouse, sinon, facultatif. Utilisez la fonction de copie intermédiaire pour copier des données vers SQL Database ou SQL Data Warehouse sans ouvrir le port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Nécessaire lorsque vous copiez depuis ou vers Azure Data Lake Store, sinon, facultatif. |
