---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99809561"
---
## <a name="run-the-function-in-azure"></a>Exécuter la fonction dans Azure

1. Dans la zone **Azure : Fonctions** de la barre latérale, développez votre abonnement, votre nouvelle application de fonction et **Fonctions**. Cliquez avec le bouton droit (Windows) ou faites <kbd>Ctrl -</kbd> clic (macOS) sur la fonction `HttpExample`, puis choisissez **Exécuter la fonction maintenant...** .

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Exécuter la fonction maintenant dans Azure à partir de Visual Studio Code":::

1. Dans **Entrer le corps de la demande** figure la valeur `{ "name": "Azure" }` pour le corps du message de la demande. Appuyez sur Entrée pour envoyer ce message de demande à votre fonction.  

1. Quand la fonction s’exécute dans Azure et retourne une réponse, une notification est générée dans Visual Studio Code.
