---
title: Fichier include
description: Fichier Include
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389492"
---
| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure lorsque vous utilisez la fonctionnalité copie intermédiaire. |
| `*.database.windows.net`      | 1433           | Nécessaire seulement quand vous copiez depuis ou vers Azure SQL Database ou Azure Synapse Analytics ; sinon, facultatif. Utilisez la fonctionnalité de copie intermédiaire pour copier des données vers SQL Database ou Azure Synapse Analytics sans ouvrir le port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Nécessaire lorsque vous copiez depuis ou vers Azure Data Lake Store, sinon, facultatif. |
