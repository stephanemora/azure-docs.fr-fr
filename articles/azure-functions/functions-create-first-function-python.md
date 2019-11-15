---
title: Créer une fonction Python déclenchée par HTTP dans Azure
description: Découvrez comment créer votre première fonction Python dans Azure à l’aide d’Azure CLI et d’Azure Functions Core Tools.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 791348088d909785b36934c3b9a2ae00fc0acbb7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622047"
---
# <a name="create-an-http-triggered-python-function-in-azure"></a>Créer une fonction Python déclenchée par HTTP dans Azure

Cet article explique comment utiliser des outils en ligne de commande pour créer un projet Python s’exécutant dans Azure Functions. Vous créez également une fonction qui est déclenchée par une requête HTTP. Enfin, vous publiez votre projet de sorte qu’il s’exécute en tant que [fonction serverless](functions-scale.md#consumption-plan) dans Azure.

Cet article est le premier de deux guides de démarrage rapide Python pour Azure Functions. Après avoir terminé ce guide de démarrage rapide, vous pouvez [ajouter une liaison de sortie de file d’attente de Stockage Azure](functions-add-output-binding-storage-queue-python.md) à votre fonction.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez :

+ Installez [Python 3.6.8](https://www.python.org/downloads/). Cette version de Python est vérifiée avec Functions. Les versions 3.7 et ultérieures ne sont pas encore prises en charge.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1575 ou une version ultérieure.

+ Installez [Azure CLI](/cli/azure/install-azure-cli) version 2.x ou une version ultérieure.

+ Avoir un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Créer et activer un environnement virtuel (facultatif)

Vous devez utiliser un environnement Python 3.6.x pour développer localement des fonctions Python. Exécutez les commandes suivantes pour créer et activer un environnement virtuel nommé `.venv`.

> [!NOTE]
> Si Python n’a pas installé venv sur votre distribution Linux, vous pouvez l’installer à l’aide de la commande suivante :
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash :

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell ou une invite de commandes Windows :

```powershell
py -m venv .venv
.venv\scripts\activate
```

Maintenant que vous avez activé l’environnement virtuel, exécutez-y les commandes restantes. Pour sortir de l’environnement virtuel, exécutez `deactivate`.

## <a name="create-a-local-functions-project"></a>Créer un projet de fonctions local

Un projet de fonctions est l’équivalent d’une application de fonction dans Azure. Il peut contenir plusieurs fonctions qui partagent les mêmes configurations locale et d’hébergement.

1. Dans l’environnement virtuel, exécutez la commande suivante :

    ```console
    func init MyFunctionProj
    ```

1. Sélectionnez **python** comme runtime de worker.

    La commande crée un dossier _MyFunctionProj_. Il contient les trois fichiers suivants :

    * *local.settings.json* : utilisé pour stocker les paramètres d’application et les chaînes de connexion lors d’une exécution en local. Ce fichier n’est pas publié sur Azure.
    * *requirements.txt* : contient la liste des packages que le système installera lors de la publication sur Azure.
    * *host.json* : contient les options de configuration globale qui affectent toutes les fonctions d’une application de fonction. Ce fichier est publié sur Azure.

1. Accédez au nouveau dossier *MyFunctionProj* :

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Créer une fonction

Ajoutez une fonction au nouveau projet.

1. Pour ajouter une fonction à votre projet, exécutez la commande suivante :

    ```console
    func new
    ```

1. Utilisez la flèche vers le bas pour sélectionner le modèle **Déclencheur HTTP**.

1. Quand vous êtes invité à indiquer un nom de fonction, entrez *HttpTrigger*, puis appuyez sur Entrée.

Ces commandes créent un sous-dossier nommé _HttpTrigger_. Il contient les fichiers suivants :

* *function.JSON* : fichier de configuration qui définit la fonction, le déclencheur et d’autres liaisons. Notez que dans ce fichier, la valeur de `scriptFile` pointe sur le fichier contenant la fonction et que le tableau `bindings` définit le déclencheur d’invocation et les liaisons.

    Chaque liaison requiert une direction, un type et un nom unique. Le déclencheur HTTP comporte une liaison d’entrée de type [`httpTrigger`](functions-bindings-http-webhook.md#trigger) et une liaison de sortie de type [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py* : fichier de script qui est votre fonction déclenchée via HTTP. Notez que ce script a une valeur `main()` par défaut. Les données HTTP du déclencheur sont passées à la fonction à l’aide du `req` nommé `binding parameter`. `req`, qui est défini dans function.json, est une instance de la [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    L’objet retourné, défini comme `$return` dans *function.json*, est une instance de la [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

La fonction s’exécute localement à l’aide du runtime Azure Functions.

1. Cette commande démarre l’application de fonction :

    ```console
    func host start
    ```

    Quand l’hôte Azure Functions démarre, il écrit une sortie similaire à la suivante. Elle est tronquée ici pour vous donner une meilleure lisibilité :

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
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Copiez l’URL de votre fonction `HttpTrigger` à partir de la sortie du runtime et collez-la dans la barre d’adresses de votre navigateur.

1. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. La capture d’écran suivante montre la réponse à la requête GET que la fonction locale retourne au navigateur :

    ![Vérifier localement dans le navigateur](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Sélectionnez Ctrl+C pour arrêter votre application de fonction.

Maintenant que vous avez exécuté votre fonction localement, vous pouvez créer l’application de fonction et d’autres ressources nécessaires dans Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Créer une application de fonction dans Azure

Une application de fonction fournit un environnement pour l’exécution du code de votre fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Exécutez la commande ci-dessous. Remplacez `<APP_NAME>` par un nom d’application de fonction unique. Remplacez `<STORAGE_NAME>` par un nom de compte de stockage. `<APP_NAME>` représente également le domaine DNS par défaut pour l’application de fonction. Ce nom doit être unique parmi toutes les applications dans Azure.

> [!NOTE]
> Vous ne pouvez pas héberger des applications Linux et Windows dans le même groupe de ressources. Si vous disposez d’un groupe de ressources existant nommé `myResourceGroup` avec une application de fonction Windows ou une application web, vous devez utiliser un autre groupe de ressources.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

La commande précédente provisionne également une instance Azure Application Insights associée dans le même groupe de ressources. Vous pouvez utiliser cette instance pour superviser votre application de fonction et afficher les journaux.

Vous êtes désormais prêt à publier votre projet de fonctions local sur l’application de fonction dans Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

Après avoir créé l’application de fonction dans Azure, vous pouvez utiliser la commande Core Tools [func azure functionapp publish](functions-run-local.md#project-file-deployment) pour déployer votre code de projet sur Azure. Dans cet exemple, remplacez `<APP_NAME>` par le nom de votre application.

```console
func azure functionapp publish <APP_NAME> --build remote
```

L’option `--build remote` permet de générer votre projet Python à distance dans Azure à partir des fichiers du package de déploiement. 

La sortie ressemble au message suivant. Elle est tronquée ici pour vous donner une meilleure lisibilité :

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Vous pouvez copier la valeur `Invoke url` pour votre `HttpTrigger` et l’utiliser pour vérifier votre fonction dans Azure. L’URL contient une valeur de chaîne de requête `code` qui est votre clé de fonction. Cette dernière empêche les autres utilisateurs d’appeler le point de terminaison de votre déclencheur HTTP dans Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Pour voir les journaux en quasi temps réel d’une application Python publiée, utilisez le [flux de métriques temps réel Application Insights](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un projet de fonctions Python avec une fonction déclenchée via HTTP, l’avez exécuté sur votre ordinateur local et l’avez déployé sur Azure. À présent, étendez votre fonction en effectuant un...

> [!div class="nextstepaction"]
> [Ajout de liaison de sortie de file d’attente Stockage Azure](functions-add-output-binding-storage-queue-python.md)
