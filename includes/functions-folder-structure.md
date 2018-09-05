---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809780"
---
Le code de toutes les fonctions d’une application de fonctions spécifique se trouve dans un dossier racine (`wwwroot`) qui contient un fichier de configuration d’hôte, ainsi qu’un ou plusieurs sous-dossiers. Chaque sous-dossier contient le code d’une fonction distincte, comme dans l’exemple suivant :

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

Le fichier host.json contient des configurations spécifiques du runtime et se trouve dans le dossier racine de l’application de fonction. Pour plus d’informations sur les paramètres disponibles, consultez les [informations de référence sur le fichier host.json](../articles/azure-functions/functions-host-json.md).

Chaque fonction a un dossier contenant un ou plusieurs fichiers de code, la configuration de function.json et d’autres dépendances. Pour un projet de bibliothèque de classes C#, le fichier de bibliothèque de classes (.dll) compilé est déployé dans le sous-dossier `bin`.

