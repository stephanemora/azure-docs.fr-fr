---
title: Ajouter une API dans Azure Static Web Apps avec Azure Functions
description: Prenez en main Azure Static Web Apps en ajoutant une API serverless à votre application web statique à l’aide d’Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596148"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Ajouter une API dans Azure Static Web Apps (en préversion) avec Azure Functions

Vous pouvez ajouter des API serverless à Azure Static Web Apps via l’intégration à Azure Functions. Cet article explique comment ajouter et déployer une API sur un site Azure Static Web Apps.

Pour plus d’informations sur la sécurisation des itinéraires d’API, reportez-vous au [guide de routage](routes.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code
- Extension [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) de Visual Studio Code.

## <a name="create-a-git-repository"></a>Créer un référentiel git 

Les étapes suivantes montrent comment créer un référentiel et cloner les fichiers sur votre ordinateur.

1. Accédez à https://github.com/staticwebdev/vanilla-basic/generate pour créer un référentiel.
1. Dans la zone _Repository name_ (Nom du référentiel), entrez **my-vanilla-api**.
1. Cliquez sur **Create repository from template** (Créer un référentiel à partir du modèle).

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Créer un référentiel à partir de vanilla-basic":::

Une fois votre projet créé, vous pouvez utiliser Visual Studio Code pour cloner le référentiel git.

1. Appuyez sur **F1** pour ouvrir la commande dans la palette de commandes
1. Collez l’URL dans l’invite _Git: Clone_, puis appuyez sur **Entrée**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Cloner un projet GitHub à l’aide de Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Cloner un projet GitHub à l’aide de Visual Studio Code":::


## <a name="create-your-local-project"></a>Créer votre projet local

Dans cette section, vous utilisez Visual Studio Code pour créer un projet Azure Functions local. Par la suite, vous publiez l’application Functions sur Azure.

1. Dans le projet _my-vanilla-api_, créez un sous-dossier nommé **api**.

   > [!NOTE]
   > Vous pouvez attribuer n’importe quel nom à ce dossier. Cet exemple utilise `api`. 

2. Appuyez sur **F1** pour ouvrir la palette de commandes
3. Entrez **Azure Functions : Create New Project...** (Créer un projet...)
4. Appuyez sur **Entrée**
5. Choisissez **Parcourir**
6. Sélectionner le dossier **api** comme répertoire de votre espace de travail de projet
7. Choisissez **Select** (Sélectionner)

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Créer un projet Azure Functions à l’aide de Visual Studio Code":::

8. Quand vous y êtes invité, indiquez les informations suivantes :

    - _Sélectionner un langage pour votre projet de fonction_ : Choisir **JavaScript**
    - _Sélectionner un modèle pour la première fonction de votre projet_ : Choisir **HTTP trigger** (Déclencheur HTTP)
    - _Fournir un nom de fonction_ : Entrer **GetMessage**
    - _Niveau d’autorisation_ : Choisissez l’option **Anonymous** (Anonyme), qui permet à quiconque d’appeler le point de terminaison de votre fonction.
        - Pour en savoir plus sur les niveaux d’autorisation, consultez [Clés d’autorisation](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. À l’aide de ces informations, Visual Studio Code génère un projet Azure Functions avec un déclencheur HTTP.
    - Vous pouvez afficher les fichiers de projet locaux dans la fenêtre de l’Explorateur de Visual Studio Code.
    - Pour en savoir plus sur les fichiers créés, consultez [Fichiers projet générés](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. Votre application doit maintenant avoir une structure de projet similaire à celle de cet exemple.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Ensuite, mettez à jour la fonction `GetMessage` sous _API/GetMessage/index.js_ avec le code suivant.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Mettez à jour la configuration de `GetMessage` sous `api/GetMessage/function.json` avec les paramètres suivants.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
Avec les paramètres ci-dessus, le point de terminaison de l’API est :

- Déclenché avec une requête HTTP adressée à la fonction
- Disponible pour toutes les requêtes, quel que soit l’état de l’authentification
- Exposé via l’itinéraire _/api/message_

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Visual Studio Code s’intègre à [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) pour vous permettre d’exécuter ce projet sur votre ordinateur de développement local avant toute publication sur Azure.

1. Exécutez la fonction en appuyant sur **F5** pour démarrer l’application Functions. La sortie Core Tools s’affiche dans le panneau _Terminal_.

2. Si Azure Functions Core Tools n’est pas encore installé, sélectionnez **Installer** à l’invite.

    Quand Core Tools est installé, votre application démarre dans le panneau _Terminal_. Dans la sortie, vous pouvez voir le point de terminaison de l’URL de votre fonction déclenchée par HTTP en train de s’exécuter localement.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Créer un projet Azure Functions à l’aide de Visual Studio Code":::

3. Avec Core Tools en cours d’exécution, accédez à l’URL suivante pour exécuter une requête `GET`.

   <http://localhost:7071/api/message>

   Une réponse est retournée, semblable à celle-ci dans un navigateur :

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Créer un projet Azure Functions à l’aide de Visual Studio Code":::

Une fois que vous avez vérifié que la fonction s’exécute correctement, vous pouvez appeler l’API à partir de l’application JavaScript.

### <a name="call-the-api-from-the-application"></a>Appeler l’API à partir de l’application

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Mettre à jour les fichiers pour accéder à l’API

1. Ensuite, mettez à jour le contenu du fichier _index.html_ à l’aide du code suivant pour extraire le texte de la fonction API et l’afficher à l’écran :

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Alors que Core Tools s’exécute, utilisez l’extension [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) de Visual Studio Code pour traiter le fichier _index.html_ et l’ouvrir dans un navigateur. 

2. Appuyez sur **F1** et choisissez **Live Server : Ouvrir avec Live Server**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Créer un projet Azure Functions à l’aide de Visual Studio Code":::

   > [!NOTE]
   > Vous pouvez utiliser d’autres serveurs ou proxys HTTP pour servir le fichier `index.html`. L’accès au fichier `index.html` n’est pas possible à l’aide de `file:///`.

### <a name="commit-and-push-your-changes-to-github"></a>Valider et envoyer vos modifications à GitHub

À l’aide de Visual Studio Code, validez et envoyez vos modifications dans le référentiel git distant.

1. Appuyer sur **F1** pour ouvrir la palette de commandes
1. Taper **Git: Commit All**
1. Ajouter un message de validation
1. Taper **Git: push**

## <a name="create-a-static-web-app"></a>Créer une application web statique

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Créer une application statique sur le Portail Azure - écran 1":::

1. Accéder au [portail Azure](https://portal.azure.com)
1. Cliquer sur **Créer une ressource**
1. Rechercher **Static Web Apps**
1. Cliquer sur **Static Web Apps (préversion)**
1. Cliquez sur **Créer**
1. Sélectionner votre _abonnement Azure_
1. Sélectionner ou créer un _groupe de ressources_
1. Nommez l’application **my-vanilla-api**.
1. Sélectionner la _région_ la plus proche de vous
1. Sélectionner **SKU** _gratuite_
1. Cliquer sur le bouton **Sign-in with GitHub** (Se connecter à GitHub) et authentifiez-vous auprès de GitHub
1. Sélectionner votre _Organisation_ préférée
1. Sélectionner **my-vanilla-api** dans la liste déroulante _Repository_ (Référentiel)
1. Sélectionner **master** maître dans la liste déroulante _Branch_ (Branche)
1. Cliquer sur le bouton **Suivant : Générer >** pour modifier la configuration de build

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Créer une application statique sur le Portail Azure - écran 2":::

Ensuite, ajoutez les détails de build suivants.

1. Entrez **./** dans _App location_ (Emplacement de l’application).

1. Entrez **api** dans _App location_.

   Il s’agit du nom du dossier d’API créé à l’étape précédente.
   
1. Désactivez la valeur par défaut de l’_emplacement de l’artefact d’application_ en laissant la zone vide.

1. Cliquez sur **Vérifier + créer**.

| Paramètre | Description             | Obligatoire |
| -------- | ----------------------- |
|  Emplacement de l’application | Emplacement du code source de l’application statique | Oui |
|  Emplacement de l’API | Emplacement du backend de l’API. Il pointe vers le dossier racine du projet d’application Azure Functions | Non |
|  Emplacement de l’artefact de l’application | Emplacement du dossier de sortie du build. Certaines infrastructures JavaScript frontales comportent une étape de génération qui place les fichiers de production dans un dossier. Ce paramètre pointe vers le dossier de sortie du build. | Non |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Créer une application statique sur le Portail Azure - écran 3":::

1. Cliquez sur **Créer**
1. Attendre la fin du déploiement (cette opération peut prendre une minute)
1. Accédez à `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`.
1. Vérifier que la compilation a réussi

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Workflow GitHub":::

L’API déployée sera disponible sur `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>`.

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Workflow GitHub":::

Vous pouvez également trouver l’URL de l’application dans le Portail Azure :

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Accéder à l’URL de l’application statique à partir du Portail Azure":::

Vous pouvez également accéder directement à votre application Azure Static Web App à l’adresse `https://<STATIC_APP_NAME>.azurestaticapps.net` et vérifier le résultat dans le navigateur.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne souhaitez pas conserver cette application pour une utilisation ultérieure, vous pouvez utiliser la procédure suivante pour supprimer l’application Azure Static Web App et ses ressources associées.

1. Accéder au [portail Azure](https://portal.azure.com)
1. Dans la barre de recherche supérieure, taper **Groupes de ressources**
1. Cliquer sur **Groupes de ressources** 
1. Sélectionner **myResourceGroup**
1. Dans la page _myResourceGroup_, assurez-vous que les ressources répertoriées sont bien celles que vous souhaitez supprimer.
1. Sélectionnez **Supprimer**.
1. Taper **myResourceGroup** dans la zone de texte
1. Sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer les paramètres d’application](./application-settings.md)
