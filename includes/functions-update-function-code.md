---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987969"
---
## <a name="update-the-function"></a>Mettre à jour la fonction

Par défaut, le modèle crée une fonction qui requiert une clé de fonction pour effectuer des requêtes. Pour simplifier le test de la fonction dans Azure, vous devez mettre à jour la fonction pour autoriser l’accès anonyme. La façon dont vous apportez cette modification dépend du langage du projet de fonctions.

### <a name="c"></a>C\#

Ouvrez le fichier de code MyHttpTrigger.cs qui correspond à votre nouvelle fonction et mettez à jour l’attribut **AuthorizationLevel** dans la définition de fonction sur la valeur `anonymous` et enregistrez vos modifications.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Ouvrez le fichier function.json pour votre nouvelle fonction, ouvrez-le dans un éditeur de texte, mettez à jour la propriété **authLevel** dans **bindings.httpTrigger** sur `anonymous`, puis enregistrez vos modifications.

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
