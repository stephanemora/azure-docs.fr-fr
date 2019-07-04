---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176837"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

La commande suivante démarre l’application de fonction. L’application s’exécute à l’aide du même runtime Azure Functions que celui qui se trouve dans Azure.

```bash
func host start --build
```

L’option `--build` est requise pour compiler les projets C#. Vous n’avez pas besoin d’utiliser cette option dans un projet JavaScript.

Lorsque l’hôte Functions démarre, il écrit une sortie similaire à la suivante, tronquée pour une meilleure lisibilité :

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copiez l’URL de votre fonction `HttpTrigger` à partir de la sortie du runtime et collez-la dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. Ce qui suit montre la réponse dans le navigateur à la requête GET retournée par la fonction locale :

![Tester localement dans le navigateur](./media/functions-run-function-test-local/functions-test-local-browser.png)

Maintenant que vous avez exécuté votre fonction localement, vous pouvez créer l’application de fonction et d’autres ressources nécessaires dans Azure.