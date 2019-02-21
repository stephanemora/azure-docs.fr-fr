---
title: Créer une application web Node.js - Azure App Service | Microsoft Docs
description: Déployez votre premier programme Hello World Node.js dans Azure App Service Web Apps en quelques minutes.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8d7bd808540ac697bde073201e88f49ace8b527a
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308670"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Créer une application web Node.js dans Azure

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour déployer une application App Service sur _Linux_, consultez [Créer et déployer une application web Node.js dans Azure App Service sur Linux](./containers/quickstart-nodejs.md).
>

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment déployer une application Node.js sur Azure App Service. Vous créez l’application web à l’aide de [l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), et vous utilisez ZipDeploy pour déployer l’exemple de code Node.js sur l’application web.

![Exemple d’application s’exécutant dans Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Vous pouvez suivre ces étapes en utilisant un ordinateur Mac, Windows ou Linux. Une fois les composants requis installés, l’exécution de cette procédure prend environ cinq minutes.   

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

* <a href="https://nodejs.org/" target="_blank">Installez Node.js et NPM</a>

## <a name="download-the-sample"></a>Télécharger l’exemple

Téléchargez l’exemple de projet Node.js à partir de [https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) et extrayez l’archive ZIP.

Ouvrez _index.js_ et recherchez la ligne suivante :

```javascript
var port = process.env.PORT || 1337;
```

App Service injecte process.env.PORT dans votre application, afin que le code utilise la variable pour savoir quel port écouter. 

Dans une fenêtre de terminal, accédez au répertoire racine de l’exemple de projet Node.js (celui qui contient _index.js_).

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

Exécutez l’application localement pour voir à quoi elle devrait ressembler lorsque vous la déploierez sur Azure. Ouvrez une fenêtre de terminal et utilisez le script `npm start` pour lancer le serveur HTTP Node.js.

```bash
npm start
```

Ouvrez un navigateur web et accédez à l’application exemple à l’adresse `http://localhost:1337`.

Vous voyez apparaître sur la page le message **Hello World** de l’exemple d’application.

![Exemple d’application s’exécutant localement](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl + C** pour quitter le serveur web.

> [!NOTE]
> Dans Azure App Service, l’application est exécutée dans l’IIS à l’aide de [iisnode](https://github.com/Azure/iisnode). Pour permettre à l’application d’être exécutée avec iisnode, le répertoire racine d’application contient un fichier web.config. Le fichier est lisible par IIS, et les paramètres liés à iisnode sont documentés dans [le référentiel GitHub d’iisnode](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

[!INCLUDE [Create ZIP file](../../includes/app-service-web-create-zip.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)] 

## <a name="create-a-web-app"></a>Créer une application web

Dans Cloud Shell, créez une application web dans le plan App Service `myAppServicePlan` avec la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). 

Dans l’exemple suivant, remplacez `<app_name>` par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Définir le runtime Node.js

Définissez le runtime Node avec la valeur 8.11.1. Pour voir tous les runtimes, exécutez [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes).

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=8.11.1
```

Accédez à votre nouvelle application web. Remplacez `<app_name>` par un nom d’application unique.

```bash
http://<app_name>.azurewebsites.net
```

Voici à quoi doit ressembler votre nouvelle application web :

![Page d’application web vide](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web.

```
http://<app_name>.azurewebsites.net
```

L’exemple de code Node.js s’exécute dans une application web Azure App Service.

![Exemple d’application s’exécutant dans Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**Félicitations !** Vous avez déployé votre première application Node.js dans App Service.

## <a name="update-and-redeploy-the-code"></a>Mettre à jour et redéployer le code

À l’aide d’un éditeur de texte, ouvrez le fichier `index.js` dans l’application Node.js et apportez une petite modification au texte contenu dans l’appel pour `response.end` :

```nodejs
response.end("Hello Azure!");
```

Dans la fenêtre de terminal en local, accédez au répertoire racine de votre application, créez un nouveau fichier ZIP pour votre projet mis à jour.

```
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
```

Déployez ce nouveau fichier ZIP dans App Service en utilisant la même procédure qu’à l’étape [Déployer un fichier Zip](#deploy-zip-file).

Revenez à la fenêtre du navigateur que vous avez ouverte à l’étape **Accéder à l’application**, puis actualisez la page.

![Mise à jour de l’exemple d’application s’exécutant dans Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gérer votre nouvelle application Azure

Accédez au <a href="https://portal.azure.com" target="_blank">Portail Azure</a> pour gérer l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis sur le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). 

![Page App Service du Portail Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Le menu de gauche fournit différentes pages vous permettant de configurer votre application. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Node.js avec MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
