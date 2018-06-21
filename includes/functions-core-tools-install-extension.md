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
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726885"
---
Quand vous développez des fonctions localement, vous pouvez installer les extensions dont vous avez besoin en utilisant Azure Functions Core Tools à partir du terminal ou d’une invite de commandes. 

Après avoir mis à jour le fichier *function.json* de manière à inclure toutes les liaisons dont votre fonction a besoin, exécutez la commande `func extensions install` dans le dossier de projet. La commande lit le fichier *function.json* afin de savoir de quels packages vous avez besoin, puis les installe.

Si vous souhaitez installer une version particulière d’un package ou que vous souhaitez installer les packages avant de modifier le fichier *function.json*, utilisez la commande `func extensions install` avec le nom du package, comme indiqué dans l’exemple suivant :

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Remplacez `<target_version>` par une version spécifique du package. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org).
