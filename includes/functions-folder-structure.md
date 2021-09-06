---
title: Fichier Include
description: inclure fichier
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/17/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cc661942dc1cf110a07df383149b03c8a4ea7409
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321021"
---
Le code de toutes les fonctions d’une application de fonctions spécifique se trouve dans un dossier de projet racine qui contient un fichier de configuration d’hôte. Le fichier [host.json](../articles/azure-functions/functions-host-json.md) contient des configurations spécifiques du runtime et se trouve dans le dossier racine de la Function App. Un dossier *bin* contient des packages et autres fichiers de bibliothèque requis par la Function App. Les structures de dossiers spécifiques requises par l’application de fonction dépendent du langage :

* [C# compilé (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)

Dans les versions 2.x et ultérieures du runtime Functions, toutes les fonctions dans l’application de fonction doivent partager la même pile de langage. 
