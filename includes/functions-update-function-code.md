---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737069"
---
## <a name="update-the-function"></a>Mettre à jour la fonction

Par défaut, le modèle crée une fonction qui requiert une clé de fonction pour effectuer des requêtes. Pour simplifier le test de la fonction dans Azure, vous devez mettre à jour la fonction pour autoriser l’accès anonyme. La façon dont vous apportez cette modification dépend du langage du projet de fonctions.

### <a name="c"></a>C\#

Ouvrez le fichier de code MyHttpTrigger.cs qui correspond à votre nouvelle fonction et mettez à jour l’attribut **AuthorizationLevel** dans la définition de fonction sur la valeur `Anonymous` et enregistrez vos modifications.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Ouvrez le fichier function.json pour votre nouvelle fonction dans un éditeur de texte, mettez à jour la propriété **authLevel** dans **bindings** sur `anonymous`, puis enregistrez vos changements.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Vous pouvez maintenant appeler la fonction dans Azure sans avoir à fournir la clé de fonction. La clé de fonction n’est jamais requise lors de l’exécution locale.
