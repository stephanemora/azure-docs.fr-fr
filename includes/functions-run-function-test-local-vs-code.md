---
title: Fichier Include
description: Fichier Include
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d47caaf9dbca14364771e0d8ba1e7f6ce16ef2d6
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174612"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Azure Functions Core Tools vous permet d’exécuter un projet Azure Functions sur votre ordinateur de développement local.

1. Pour tester votre fonction, définissez un point d’arrêt dans le code de fonction et appuyez sur F5 pour démarrer le projet d’application de fonction. La sortie de Core Tools est affichée dans le panneau **Terminal**.

1. Dans le panneau **Terminal**, copiez le point de terminaison de l’URL de votre fonction déclenchée via HTTP. 

    ![Sortie Azure locale](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. L’exécution est suspendue lorsque le point d’arrêt est atteint.

1. Lorsque vous continuez l’exécution, la capture d’écran suivante du navigateur montre la réponse à la requête GET :

    ![Réponse de la fonction localhost dans le navigateur](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Pour arrêter le débogage, appuyez sur MAJ + F5.