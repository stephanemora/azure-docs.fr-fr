---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/18/2021
ms.author: glenga
ms.openlocfilehash: ba804f75309b8cc05af4cf927a3eef6c1774d31a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110459455"
---
Le modèle de projet Azure Functions dans Visual Studio crée un projet de bibliothèque de classe C# que vous pouvez publier dans une application de fonction dans Azure. Vous pouvez utiliser une application de fonction pour regrouper des fonctions en une unité logique afin de faciliter la gestion, le déploiement, la mise à l’échelle et le partage des ressources.

1. Dans le menu de Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans **Créer un projet**, entrez *functions* dans la zone de recherche, choisissez le modèle **Azure Functions**, puis sélectionnez  **Suivant**.

1. Dans **Configurer votre nouveau projet**, entrez un **Nom de projet**, puis sélectionnez **Créer**. Le nom d’application de la fonction doit être valide en tant qu’espace de noms C#, afin de ne pas utiliser des traits d’union, des traits de soulignement ou d’autres caractères non alphanumériques.

1. Pour les paramètres **Créer une application Azure Functions**, utilisez les valeurs du tableau suivant :

    | Paramètre      | Valeur  | Description                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version de .NET** | **.NET Core 3 (LTS)** | Cette valeur crée un projet de fonction qui utilise la version 3.x du runtime Azure Functions, qui prend en charge .NET Core 3.x. La version 1.x d’Azure Functions prend en charge .NET Framework. Pour plus d’informations, consultez [Vue d’ensemble des versions du runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Modèle de fonction** | **Déclencheur HTTP** | Cette valeur crée une fonction déclenchée par une requête HTTP. |
    | **Compte de stockage (AzureWebJobsStorage)**  | **Émulateur de stockage** | Étant donné qu’une application de fonction dans Azure nécessite un compte de stockage, il en est attribué ou créé un quand vous publiez votre projet sur Azure. Un déclencheur HTTP n’utilise pas de chaîne de connexion de compte Stockage Azure ; tous les autres types de déclencheurs nécessitent une chaîne de connexion de compte Stockage Azure valide.  |
    | **Niveau d’autorisation** | **Anonyme** | La fonction créée peut être déclenchée par n’importe quel client sans fournir une clé. Ce paramètre d’autorisation facilite le test de votre nouvelle fonction. Pour plus d’informations sur les clés et autorisations, consultez [Clés d’autorisation](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) et [Liaisons HTTP et webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Paramètres de projet Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Veillez à définir le **Niveau d’autorisation** sur **Anonyme**. Si vous choisissez le niveau par défaut **Fonction**, vous êtes invité à présenter la [clé de fonction](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) dans les requêtes d’accès à votre point de terminaison de fonction.

1. Sélectionnez **Créer** pour créer le projet de fonction et la fonction de déclencheur HTTP.
