---
title: Fichier include
description: Fichier include
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83797163"
---
Une tâche qui s’exécute dans Azure Batch peut produire des données de sortie pendant son exécution. Le plus souvent, les données de sortie des tâches doivent être stockées pour être récupérées par d’autres tâches du travail et/ou par l’application cliente qui a exécuté le travail. Les tâches écrivent les données de sortie dans le système de fichiers d’un nœud de calcul Batch, mais toutes les données du nœud sont perdues lorsque le nœud est réinitialisé ou qu’il quitte le pool. En outre, les tâches sont parfois associées à une période de rétention de fichier, au terme de laquelle les fichiers créés par la tâche sont supprimés. Pour toutes ces raisons, il est important de conserver la sortie des tâches dont vous aurez besoin par la suite dans un magasin de données tel que [Stockage Azure](https://docs.microsoft.com/azure/storage/).

Pour plus d’options de compte de stockage dans Batch, consultez [Comptes de Stockage Azure et Batch](../articles/batch/accounts.md#azure-storage-accounts).
