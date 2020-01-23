---
title: Fichier Include
description: Fichier Include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 343321b43d219401f0b494ab3f6a54074fbc36f3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279316"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

La commande suivante démarre l’application de fonction. L’application s’exécute à l’aide du même runtime Azure Functions que celui qui se trouve dans Azure. La commande de démarrage varie selon le langage de votre projet.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

Lorsque l’hôte Functions démarre, il écrit une sortie similaire à la suivante, que nous avons tronquée pour une meilleure lisibilité :

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
