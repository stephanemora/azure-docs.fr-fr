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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594526"
---
Le code de toutes les fonctions d’une application de fonctions spécifique se trouve dans un dossier de projet racine qui contient un fichier de configuration d’hôte, ainsi qu’un ou plusieurs sous-dossiers. Chaque sous-dossier contient le code d’une fonction distincte. La structure de dossiers est présentée dans la représentation suivante :

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

Dans la version 2.x du runtime Functions, toutes les fonctions dans la Function App doivent partager la même pile de langage.  

Le fichier [host.json](../articles/azure-functions/functions-host-json.md) contient des configurations spécifiques du runtime et se trouve dans le dossier racine de la Function App. Un dossier *bin* contient des packages et autres fichiers de bibliothèque requis par la Function App. Consultez la configuration requise spécifique au langage d’un projet d’application de fonction :

* [Bibliothèque de classes C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



