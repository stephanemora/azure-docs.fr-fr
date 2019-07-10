---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444634"
---
## <a name="create-an-azure-functions-project"></a>Créer votre projet Functions avec une fonction 

Le modèle de projet Azure Functions dans Visual Studio Code crée un projet qui peut être publié dans une application de fonction sur Azure. Une application de fonctions vous permet de regrouper les fonctions dans une unité logique pour la gestion, le déploiement et le partage des ressources.

1. Dans Visual Studio Code, appuyez sur F1 pour ouvrir la palette de commandes. Dans la palette de commandes, recherchez et sélectionnez `Azure Functions: Create new project...`.

1. Choisissez un emplacement de répertoire pour votre espace de travail de projet et optez pour **Sélectionner**.

    > [!NOTE]
    > Ces étapes ont été conçues pour être terminées en dehors d’un espace de travail. Dans ce cas, ne sélectionnez pas de dossier de projet qui fait partie d’un espace de travail.

1. Suivez les invites et fournissez les informations suivantes :

    | Prompt | Valeur | Description |
    | ------ | ----- | ----------- |
    | Sélectionnez un langage pour votre projet d’application de fonction | C# ou JavaScript | Cet article est valable pour C# et JavaScript. Pour Python, consultez [cet article Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions), et pour PowerShell, consultez [cet article PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Sélectionner un modèle pour la première fonction de votre projet | Déclencheur HTTP | Créez une fonction déclenchée via HTTP dans la nouvelle application de fonction. |
    | Fournir un nom de fonction | HttpTrigger | Appuyez sur Entrée pour utiliser le nom par défaut. |
    | Fournir un espace de noms | My.Functions | (C# uniquement) Les bibliothèques de classe C# doivent avoir un espace de noms.  |
    | Niveau d’autorisation | Fonction | Nécessite une [touche de fonction](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) pour appeler le point de terminaison HTTP de la fonction. |
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | Ajouter à l’espace de travail | Crée l’application de fonction dans l’espace de travail actuel. |

Visual Studio Code crée le projet d’application de fonction dans un nouvel espace de travail. Ce projet contient les fichiers de configuration [host.json](../articles/azure-functions/functions-host-json.md) et [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), ainsi que des fichiers de projet spécifiques au langage. 

Une nouvelle fonction déclenchée via HTTP est également créée dans le dossier HttpTrigger du projet d’application de fonction.