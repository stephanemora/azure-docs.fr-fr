---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493466"
---
## <a name="run-the-function-in-azure"></a>Exécuter la fonction dans Azure

1. Dans la zone **Azure : Functions** de la barre latérale, développez **Projet local** > **Fonctions**. Cliquez avec le bouton droit (Windows) ou faites <kbd>Ctrl -</kbd> clic (macOS) sur la fonction `HttpExample`, puis choisissez **Exécuter la fonction maintenant...** .

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Exécuter la fonction maintenant dans Azure à partir de Visual Studio Code":::

1. Dans **Entrer le corps de la demande** figure la valeur `{ "name": "Azure" }` pour le corps du message de la demande. Appuyez sur Entrée pour envoyer ce message de demande à votre fonction.  

1. Quand la fonction s’exécute dans Azure et retourne une réponse, une notification est générée dans Visual Studio Code.
