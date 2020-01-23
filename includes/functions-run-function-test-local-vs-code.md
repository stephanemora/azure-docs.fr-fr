---
title: Fichier Include
description: Fichier Include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ebcbe7e8af7e01a9b7e75282be4e4f4ceb80b806
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279378"
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
