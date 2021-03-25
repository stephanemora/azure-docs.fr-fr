---
title: Développer et publier des fonctions .NET 5 à l’aide d’Azure Functions
description: Découvrez comment créer et déboguer des fonctions C# à l’aide de .NET 5.0, puis déployer le projet local sur un hébergement serverless dans Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201361"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Développer et publier des fonctions .NET 5 à l’aide d’Azure Functions 

Cet article explique comment utiliser les fonctions C# à l’aide de .NET 5.0 s’exécutant hors processus à partir du runtime Azure Functions. Vous allez apprendre à créer, déboguer localement et publier ces fonctions de processus isolé .NET dans Azure. Dans Azure, ces fonctions s’exécutent dans un processus isolé prenant en charge .NET 5.0. Pour plus d’informations, consultez [Guide d’exécution de fonctions sur .NET 5.0 dans Azure](dotnet-isolated-process-guide.md).

Si vous n’êtes pas tenu de prendre en charge .NET 5.0 ou d’exécuter vos fonctions hors processus, vous souhaiterez peut-être [créer une fonction de la bibliothèque de classes C#](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>Actuellement, le développement de fonctions de processus isolé .NET dans le portail Azure n’est pas pris en charge. Vous devez utiliser la publication Azure CLI ou Visual Studio Code pour créer une application de fonction dans Azure prenant en charge l’exécution des applications .NET 5.0 hors processus.   

## <a name="prerequisites"></a>Prérequis

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Kit de développement logiciel (SDK) .NET 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.0.3381 ou ultérieure.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.20 ou ultérieure.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code.  

+ [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code version 1.3.0 ou ultérieure.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), y compris la charge de travail de **développement Azure**.  
Actuellement, les modèles de projet de fonction isolé .NET et la publication ne sont pas disponibles dans Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Créer un projet de fonction local

Dans Azure Functions, un projet de fonction est un conteneur pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Actuellement, aucun modèle de projet Visual Studio ne prend en charge la création de projets de fonctions isolés .NET. Cet article explique comment utiliser Core Tools pour créer votre projet C#, que vous pouvez ensuite exécuter localement et déboguer dans Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, sélectionnez l’icône **Créer un projet**.

    ![Choisir Créer un projet](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Choisissez un emplacement de répertoire pour votre espace de travail de projet et optez pour **Sélectionner**.

    > [!NOTE]
    > Ces étapes ont été conçues pour être terminées en dehors d’un espace de travail. Dans ce cas, ne sélectionnez pas de dossier de projet qui fait partie d’un espace de travail.

1. Quand vous y êtes invité, indiquez les informations suivantes :

    + **Sélectionner un langage pour votre projet de fonction** : Choisissez `C#`.

    + **Sélectionnez un runtime .NET** : choisissez `.NET 5 isolated` .

    + **Sélectionner un modèle pour la première fonction de votre projet** : Choisissez `HTTP trigger`.

    + **Fournir un nom de fonction** : Tapez `HttpExample`.

    + **Fournir un espace de noms** : Tapez `My.Functions`.

    + **Niveau d’autorisation** : Choisissez l’option `Anonymous`, qui permet à quiconque d’appeler le point de terminaison de votre fonction. Pour en savoir plus sur le niveau d’autorisation, consultez [Clés d’autorisation](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Sélectionner la façon dont vous souhaitez ouvrir votre projet** : Choisissez `Add to workspace`.

1. À l’aide de ces informations, Visual Studio Code génère un projet Azure Functions avec un déclencheur HTTP. Vous pouvez voir les fichiers de projet locaux dans l’Explorateur. Pour en savoir plus sur les fichiers créés, consultez [Fichiers projet générés](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Exécutez la commande `func init`, comme suit, pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* :  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    La spécification de `dotnetisolated` crée un projet qui s’exécute sur .NET 5.0.


1. Accédez au dossier du projet :

    ```console
    cd LocalFunctionProj
    ```

    Ce dossier contient divers fichiers pour le projet, notamment les fichiers de configuration [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Dans la mesure où *local.settings.json* peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier *.gitignore*.

1. Ajoutez une fonction à votre projet à l’aide de la commande suivante, où l’argument `--name` est le nom unique de votre fonction (HttpExample) et où l’argument `--template` spécifie le déclencheur de la fonction (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` crée un fichier de code HttpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

À ce stade, vous pouvez exécuter la commande `func start` à partir de la racine de votre dossier de projet pour compiler et exécuter le projet de fonctions isolé C#. Actuellement, si vous souhaitez déboguer votre code de fonction hors processus dans Visual Studio, vous devez attacher manuellement un débogueur au processus d’exécution des fonctions en cours d’exécution en procédant comme suit :  

1. Ouvrez le fichier projet (.csproj) dans Visual Studio. Vous pouvez examiner et modifier votre code de projet et définir les points d’arrêt souhaités dans le code. 

1. À partir du dossier racine du projet, utilisez la commande suivante à partir du terminal ou une invite de commandes pour démarrer l’hôte du runtime :

    ```console
    func start --dotnet-isolated-debug
    ```

    L'option `--dotnet-isolated-debug` indique au processus d’attendre l’attachement d’un débogueur avant de continuer. À la fin de la sortie, vous devriez voir des informations similaires à celles-ci : 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX` indique l’ID de processus (PID) du processus dotnet.exe qui exécute l’hôte Functions.
 
1. Dans la sortie du runtime Azure Functions, notez l’ID de processus du processus hôte auquel vous attacherez un débogueur. Notez également l’URL de votre fonction locale.

1. Dans le menu **Déboguer** de Visual Studio, sélectionnez **Attacher au processus...** , localisez le processus correspondant à l’ID de processus, puis sélectionnez **Attacher**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Attacher le débogueur à un processus hôte Functions":::    

    Une fois le débogueur attaché, vous pouvez déboguer le code de votre fonction normalement.

1. Dans la barre d’adresse de votre navigateur, entrez l’URL de votre fonction locale, qui se présente comme suit, puis exécutez la requête. 

    <http://localhost:7071/api/HttpExample>

    Vous devez voir la sortie de suivi de la requête écrite dans le terminal en cours d’exécution. L’exécution du code s’arrête à tous les points d’arrêt que vous définissez dans le code de votre fonction.

1. Lorsque vous avez terminé, accédez au terminal et appuyez sur Ctrl + C pour arrêter le processus hôte.
 
Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

> [!NOTE]  
> Actuellement, la publication Visual Studio n’est pas disponible pour les applications de processus isolé .NET. Une fois votre projet développé dans Visual Studio, vous devez utiliser Azure CLI pour créer les ressources Azure distantes. Vous pouvez ensuite utiliser Azure Functions Core Tools à partir de la ligne de commande pour publier votre projet sur Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Créer des ressources Azure de prise en charge pour votre fonction

Avant de déployer le code de votre fonction dans Azure, vous devez créer trois ressources :

- Un [groupe de ressources](../azure-resource-manager/management/overview.md), qui est un conteneur logique pour les ressources associées.
- Un [compte de stockage](../storage/common/storage-account-create.md), qui sert à conserver l’état et d’autres informations sur vos fonctions.
- Une application de fonction, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Utilisez les commandes suivantes pour créer ces éléments.

1. Si vous ne l’avez pas déjà fait, connectez-vous à Azure :

    ```azurecli
    az login
    ```

    La commande [az login](/cli/azure/reference-index#az_login) vous connecte à votre compte Azure.

1. Créez un groupe de ressources nommé `AzureFunctionsQuickstart-rg` dans la région `westeurope` :

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    La commande [az group create](/cli/azure/group#az_group_create) crée un groupe de ressources. Vous créez généralement votre groupe de ressources et vos ressources dans une région près de chez vous, en utilisant une région disponible retournée par la commande `az account list-locations`.

1. Créez un compte de stockage universel dans votre groupe de ressources et votre région :

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    La commande [az storage account create](/cli/azure/storage/account#az_storage_account_create) crée le compte de stockage. 

    Dans l’exemple précédent, remplacez `<STORAGE_NAME>` par un nom qui vous convient et qui est unique dans Stockage Azure. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel, qui est [pris en charge par Functions](storage-considerations.md#storage-account-requirements).

1. Créer l’application de fonction dans Azure :
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    La commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crée l’application de fonction dans Azure. 
    
    Dans l’exemple précédent, remplacez `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente, puis remplacez `<APP_NAME>` par le nom global unique qui vous convient. `<APP_NAME>` représente également le domaine DNS par défaut pour l’application de fonction. 
    
    Cette commande crée une application de fonction exécutant .NET 5.0 sous le [Plan de consommation Azure Functions](consumption-plan.md). Ce plan doit être gratuit pour l’utilisation faite dans cet article. Cette commande approvisionne également une instance Azure Application Insights associée dans le même groupe de ressources. Utilisez cette instance pour superviser votre application de fonction et afficher les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Dans cette section, vous créez une application de fonction et les ressources associées dans votre abonnement Azure, puis vous déployez votre code.

> [!IMPORTANT]
> La publication sur une application de fonction existante remplace le contenu de cette application dans Azure.


1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, choisissez le bouton **Déployer sur une application de fonction**.

    ![Publier votre projet sur Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quand vous y êtes invité, indiquez les informations suivantes :

    - **Sélectionnez le dossier** : choisissez un dossier dans votre espace de travail ou accédez à un dossier qui contient votre application de fonction. Vous ne verrez pas cette invite si vous avez déjà ouvert une application de fonction valide.

    - **Sélectionnez l’abonnement** : choisissez l’abonnement à utiliser. Vous ne verrez pas cette invite si vous n’avez qu’un seul abonnement.

    - **Sélectionnez une application de fonction dans Azure** : Choisissez `- Create new Function App`. (Ne choisissez pas l’option `Advanced`, qui n’est pas abordée dans cet article.)
      
    - **Entrer un nom global unique pour l’application de fonction** : Tapez un nom valide dans un chemin d’URL. Le système vérifie que le nom que vous tapez est unique dans Azure Functions.
    
    - **Sélectionner une pile d’exécution** : Choisissez `.NET 5 (non-LTS)`. 
    
    - **Sélectionnez un emplacement pour les nouvelles ressources** :  Pour de meilleures performances, choisissez une [région](https://azure.microsoft.com/regions/) proche de vous. 
    
    Dans la zone de notification, vous pouvez voir l’état des ressources individuelles au fur et à mesure qu’elles sont créées dans Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notification de création de ressources Azure":::
    
1.  Quand vous avez terminé, les ressources Azure suivantes sont créées dans votre abonnement et leurs noms reposent sur le nom de votre application de fonction :
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Sélectionnez **Afficher la sortie de** dans cette notification pour afficher les résultats de la création et du déploiement, y compris les ressources Azure que vous avez créées. Si vous manquez la notification, sélectionnez l’icône de cloche dans le coin inférieur droit pour la voir de nouveau.

    ![Créer une notification de fin](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous avez créé des ressources pour terminer cet article. Vous pouvez être facturé pour ces ressources, en fonction de [l’état de votre compte](https://azure.microsoft.com/account/) et de la [tarification du service](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Utiliser les étapes suivantes pour supprimer l’application de fonction et ses ressources connexes afin d’éviter des coûts supplémentaires.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Utiliser les étapes suivantes pour supprimer l’application de fonction et ses ressources connexes afin d’éviter des coûts supplémentaires.

1. Dans Cloud Explorer, développez votre abonnement > **App Services**, cliquez avec le bouton droit sur votre application de fonction et choisissez **Ouvrir dans le portail**. 

1. Dans la page de l’application de fonction, sélectionnez l’onglet **Vue d’ensemble**, puis le lien sous **Groupe de ressources**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Sélectionner le groupe de ressources à supprimer dans la page de l’application de fonction":::

2. Dans la page **Groupe de ressources**, passez en revue la liste des ressources incluses et vérifiez qu’elles correspondent à celles que vous souhaitez supprimer.
 
3. Sélectionnez **Supprimer le groupe de ressources** et suivez les instructions.

   Cette opération peut prendre quelques minutes. Une fois terminée, une notification s’affiche pendant quelques secondes. Vous pouvez également sélectionner l’icône représentant une cloche en haut de la page pour afficher la notification.
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les fonctions isolées .NET](dotnet-isolated-process-guide.md)

