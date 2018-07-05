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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063746"
---
Quand vous développez des fonctions localement, vous pouvez installer les extensions dont vous avez besoin en utilisant Azure Functions Core Tools à partir du terminal ou d’une invite de commandes. 

Après avoir mis à jour le fichier *function.json* de manière à inclure toutes les liaisons dont votre fonction a besoin, exécutez la commande `func extensions install` dans le dossier de projet. La commande lit le fichier *function.json* afin de savoir de quels packages vous avez besoin, puis les installe.

Si vous souhaitez installer une version particulière d’un package ou que vous souhaitez installer les packages avant de modifier le fichier *function.json*, utilisez la commande `func extensions install` avec le nom du package, comme indiqué dans l’exemple suivant :

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Remplacez `<target_version>` par une version spécifique du package, telle que `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org).
