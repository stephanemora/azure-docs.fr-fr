---
title: Fichier Include
description: Fichier Include
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2018
ms.locfileid: "31532496"
---
Une tâche qui s’exécute dans Azure Batch peut produire des données de sortie pendant son exécution. Le plus souvent, les données de sortie des tâches doivent être stockées pour être récupérées par d’autres tâches du travail et/ou par l’application cliente qui a exécuté le travail. Les tâches écrivent les données de sortie dans le système de fichiers d’un nœud de calcul Batch, mais toutes les données du nœud sont perdues lorsque le nœud est réinitialisé ou qu’il quitte le pool. En outre, les tâches sont parfois associées à une période de rétention de fichier, au terme de laquelle les fichiers créés par la tâche sont supprimés. Pour toutes ces raisons, il est important de conserver la sortie des tâches dont vous aurez besoin par la suite dans un magasin de données tel que [Stockage Azure](https://docs.microsoft.com/azure/storage/).

Pour connaître les options de compte de stockage de Batch, consultez [Aperçu des fonctionnalités d’Azure Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
