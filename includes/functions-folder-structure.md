---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4460d19de1859a8a3c51d91d418b948b5d3532a6
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666716"
---
Le code de toutes les fonctions d’une application de fonctions spécifique se trouve dans un dossier de projet racine qui contient un fichier de configuration d’hôte, ainsi qu’un ou plusieurs sous-dossiers. Chaque sous-dossier contient le code d’une fonction distincte. La structure de dossiers est présentée dans la représentation suivante :

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Dans la version 2.x du runtime Functions, toutes les fonctions dans la Function App doivent partager la même pile de langage.  

Le fichier [host.json](../articles/azure-functions/functions-host-json.md) contient des configurations spécifiques du runtime et se trouve dans le dossier racine de la Function App. Un dossier *bin* contient des packages et autres fichiers de bibliothèque requis par la Function App. Consultez la configuration requise spécifique au langage d’un projet d’application de fonction :

* [Bibliothèque de classes C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
