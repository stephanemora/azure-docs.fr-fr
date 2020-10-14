---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84732467"
---
1. Dans Visual Studio Code, sélectionnez F1 pour ouvrir la palette de commandes. Dans la palette de commandes, recherchez et sélectionnez **Azure Functions: Deploy to function app**.

1. Si vous n’êtes pas connecté, vous êtes invité à le faire avec **Se connecter à Azure**. Une fois la connexion établie à partir du navigateur, revenez à Visual Studio Code. Si vous avez plusieurs abonnements, **sélectionnez un abonnement** qui contient votre application de fonction.

1. Sélectionnez votre application de fonction existante dans Azure. Dans la fenêtre d’avertissement sur le remplacement de tous les fichiers dans l’application de fonction, sélectionnez **Déployer** pour accepter l’avertissement et continuer.

Le projet est régénéré, repackagé et chargé vers Azure. Le projet existant est remplacé par le nouveau package, et l’application de fonction redémarre.