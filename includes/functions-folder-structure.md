---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731184"
---
Le code de toutes les fonctions d’une application de fonctions spécifique se trouve dans un dossier de projet racine qui contient un fichier de configuration d’hôte, ainsi qu’un ou plusieurs sous-dossiers. Chaque sous-dossier contient le code d’une fonction distincte, comme dans la représentation suivante :

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Dans la version 2.x du runtime Functions, toutes les fonctions dans l’application de fonction doivent partager le même rôle de travail de langage.  

Le fichier [host.json](../articles/azure-functions/functions-host-json.md), qui contient des configurations spécifiques du runtime, se trouve dans le dossier racine de l’application de fonction. Un dossier `bin` contient les packages et autres fichiers de bibliothèque requis par l’application de fonction. Consultez la configuration requise spécifique au langage d’un projet d’application de fonction :

- [Bibliothèque de classes C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [Script C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

