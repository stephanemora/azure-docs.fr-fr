---
title: Créer votre première fonction Python dans Azure
description: Découvrez comment créer votre première fonction Python dans Azure à l’aide d’Azure CLI et d’Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: a5abc90f5a40911538b6516622203f595adfed5c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091813"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Créer votre première fonction Python dans Azure (préversion)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Ce guide de démarrage rapide décrit comment utiliser Azure CLI pour créer votre première application de fonction Python [serverless](https://azure.microsoft.com/overview/serverless-computing/) exécutée sur Linux. Le code de fonction est créé en local, puis déployé sur Azure à l’aide d’[Azure Functions Core Tools](functions-run-local.md). Pour en savoir plus sur les considérations de préversion concernant l’exécution de vos applications de fonction sur Linux, consultez [cet article sur les fonctions sur Linux ](https://aka.ms/funclinux).

Les étapes suivantes sont prises en charge sur un ordinateur Mac, Windows ou Linux.

## <a name="prerequisites"></a>Prérequis

Pour générer et tester localement, vous devez :

+ Installer [Python 3.6](https://www.python.org/downloads/).

+ Installer [Azure Functions Core Tools](functions-run-local.md#v2) version 2.2.70 ou ultérieure.

Pour publier et exécuter l’application dans Azure

+ Installez [Azure CLI]( /cli/azure/install-azure-cli) version 2.x ou ultérieure.

+ Vous avez besoin d’un abonnement Azure actif.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Créer et activer un environnement virtuel

Pour créer un projet Functions, vous devez travailler dans un environnement virtuel Python 3.6. Exécutez les commandes suivantes pour créer et activer un environnement virtuel nommé `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Créer un projet Functions local

Vous pouvez maintenant créer un projet Functions local. Ce répertoire est l’équivalent d’une application de fonction dans Azure. Il peut contenir plusieurs fonctions qui partagent la même configuration locale et d’hébergement.

Dans la fenêtre de terminal ou à partir d’une invite de commandes, exécutez la commande suivante :

```bash
func init MyFunctionProj
```

Choisissez **python** comme runtime.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Vous verrez quelque chose de similaire à la sortie suivante :

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Un nouveau dossier nommé _MyFunctionProj_ est créé. Pour continuer, basculez vers ce dossier.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Créer une fonction

Exécutez la commande suivante pour créer une fonction :

```bash
func new
```

Choisissez `HTTP Trigger` comme modèle et spécifiez `HttpTrigger` comme **nom de fonction**.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Vous verrez quelque chose de similaire à la sortie suivante :

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Un sous-dossier nommé _HttpTrigger_ est créé. Ce fichier contient `__init__.py`, qui est le fichier de script principal, et le fichier `function.json`, qui décrit le déclencheur et les liaisons utilisés par la fonction. Pour en savoir plus sur le modèle de programmation, vous pouvez consulter le [guide du développeur Python Azure Functions](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Utilisez la commande suivante pour exécuter l’hôte Functions localement.

```bash
func host start
```

Quand l’hôte Functions démarre, il génère l’URL de votre fonction déclenchée par HTTP : (Notez que l’intégralité de la sortie a été tronquée pour une meilleure lisibilité.)

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Copiez l’URL de votre fonction à partir de la sortie et collez-la dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

La capture d’écran suivante montre la réponse retournée par la fonction quand elle est déclenchée à partir du navigateur :

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Vous êtes maintenant prêt à créer une application de fonction et d’autres ressources nécessaires pour la publication sur Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Créer une application de fonction Linux dans Azure

L’application de fonction fournit un environnement pour l’exécution de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une **application de fonction Python exécutée sur Linux** à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create).

Exécutez la commande suivante en spécifiant un nom d’application de fonction unique à la place de l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. `<app_name>` représente également le domaine DNS par défaut pour l’application de fonction. Ce nom doit être unique parmi toutes les applications dans Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Si vous disposez d’un groupe de ressources existant nommé `myResourceGroup` avec une application App Service n’étant pas hébergée sur Linux, vous devez utiliser un autre groupe de ressources. Vous ne pouvez pas héberger des applications Windows et Linux dans le même groupe de ressources.  

Une fois l’application de fonction créée, le message suivant s’affiche :

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Vous êtes maintenant prêt à publier votre projet de fonctions local sur Function App dans Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

À l’aide d’Azure Functions Core Tools, exécutez la commande suivante. Remplacez `<app_name>` par le nom de votre application obtenu à l’étape précédente.

```bash
func azure functionapp publish <app_name>
```

Une sortie similaire à la suivante, tronquée pour une meilleure lisibilité, s’affiche.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le développement de fonctions Azure à l’aide de Python.

> [!div class="nextstepaction"]
> [Guide du développeur Python Azure Functions](functions-reference-python.md)
> [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)
