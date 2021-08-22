---
title: Fichier Include
description: Fichier Include
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: f738a72c4316b51d13cd93194576549839d8127f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642019"
---
| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure lorsque vous utilisez la fonctionnalité copie intermédiaire. |
| `*.database.windows.net`      | 1433           | Nécessaire seulement quand vous copiez depuis ou vers Azure SQL Database ou Azure Synapse Analytics ; sinon, facultatif. Utilisez la fonctionnalité de copie intermédiaire pour copier des données vers SQL Database ou Azure Synapse Analytics sans ouvrir le port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Nécessaire lorsque vous copiez depuis ou vers Azure Data Lake Store, sinon, facultatif. |
