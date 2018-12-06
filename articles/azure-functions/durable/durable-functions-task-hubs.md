---
title: Hubs de tâches dans Fonctions durables - Azure
description: Découvrez ce que représente un hub de tâches dans l’extension Fonctions durables pour Azure Functions. Découvrez comment configurer des hubs de tâches.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 7a6346e594c5a7cc4cf02f3ea658aac4977e641a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637454"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tâches dans Fonctions durables (Azure Functions)

Un *hub de tâches* dans [Fonctions durables](durable-functions-overview.md) est un conteneur logique réservé aux ressources Stockage Azure qui sont utilisées pour les orchestrations. Les fonctions d’orchestrateur et d’activité peuvent interagir entre elles uniquement si elles appartiennent au même hub de tâches.

Chaque application de fonction dispose d’un hub de tâches distinct. Si plusieurs applications de fonction partagent un compte de stockage, ce compte de stockage contient plusieurs hub de tâches. Le diagramme suivant illustre un hub de tâches par application de fonction dans les comptes de stockage partagé et dédié.

![Schéma représentant les comptes de stockage partagé et dédié.](./media/durable-functions-task-hubs/task-hubs-storage.png)

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

### <a name="hostjson-functions-v1"></a>host.json (Functions v1)
```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```
### <a name="hostjson-functions-v2"></a>host.json (Functions v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "MyTaskHub"
    }
  }
}
```
Les hubs de tâches peuvent également être configurés à l’aide de paramètres d’application, comme indiqué dans l’exemple de fichier suivant *host.json* :

### <a name="hostjson-functions-v1"></a>host.json (Functions v1)
```json
{
  "durableTask": {
    "HubName": "%MyTaskHub%"
  }
}
```
### <a name="hostjson-functions-v2"></a>host.json (Functions v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "%MyTaskHub%"
    }
  }
}
```
Le nom du hub de tâches est défini sur la valeur du paramètre d’application `MyTaskHub`. Le fichier `local.settings.json` suivant montre comment définir le paramètre `MyTaskHub` sur `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" :  "samplehubname" 
  }
}
```

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom par défaut est **DurableFunctionsHub**.

> [!NOTE]
> Le nom permet de différencier les hubs de tâches les uns des autres lorsqu’ils sont plusieurs dans un compte de stockage partagé. Si plusieurs applications de fonction partagent un compte de stockage partagé, vous devez configurer des noms différents pour chaque hub de tâches dans les fichiers *host.json*.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment gérer les versions](durable-functions-versioning.md)
