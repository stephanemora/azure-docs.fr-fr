---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
Quand vous développez des fonctions localement, vous pouvez installer les extensions dont vous avez besoin en utilisant Azure Functions Core Tools à partir du terminal ou d’une invite de commandes. La commande `func extensions install` suivante installe l’extension de liaison Azure Cosmos DB :

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Remplacez `<taget_version>` par une version spécifique du package. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org).
