---
title: Hubs de tâches dans Fonctions durables - Azure
description: Découvrez ce que représente un hub de tâches dans l’extension Fonctions durables pour Azure Functions. Découvrez comment configurer des hubs de tâches.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tâches dans Fonctions durables (Azure Functions)

Un *hub de tâches* dans [Fonctions durables](durable-functions-overview.md) est un conteneur logique réservé aux ressources Stockage Azure qui sont utilisées pour les orchestrations. Les fonctions d’orchestrateur et d’activité peuvent interagir entre elles uniquement si elles appartiennent au même hub de tâches.

Chaque application de fonction dispose d’un hub de tâches distinct. Si plusieurs applications de fonction partagent un compte de stockage, ce compte de stockage contient plusieurs hub de tâches. Le diagramme suivant illustre un hub de tâches par application de fonction dans les comptes de stockage partagé et dédié.

![Schéma représentant les comptes de stockage partagé et dédié.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Ressources Azure Storage

Un hub de tâches se compose des ressources de stockage suivantes : 

* Une ou plusieurs files d’attente de contrôle.
* Une file d’attente des éléments de travail.
* Une table d’historique.
* Une table d’instances.
* Un conteneur de stockage comprenant un ou plusieurs objets blob de bail.

Toutes ces ressources sont automatiquement créées dans le compte Stockage Azure par défaut lorsque des fonctions d’orchestrateur ou d’activité sont exécutées ou planifiées pour l’être. L’article [Performances et mise à l’échelle](durable-functions-perf-and-scale.md) explique comment ces ressources sont utilisées.

## <a name="task-hub-names"></a>Noms de hub de tâches

Les hubs de tâches sont identifiés à l’aide d’un nom qui est déclaré dans le fichier *host.json*, comme indiqué dans l’exemple suivant :

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom par défaut est **DurableFunctionsHub**.

> [!NOTE]
> Le nom permet de différencier les hubs de tâches les uns des autres lorsqu’ils sont plusieurs dans un compte de stockage partagé. Si plusieurs applications de fonction partagent un compte de stockage partagé, vous devez configurer des noms différents pour chaque hub de tâches dans les fichiers *host.json*.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment gérer les versions](durable-functions-versioning.md)
