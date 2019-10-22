---
title: 'Démarrage rapide : Créer une application Python sur Linux - Azure App Service'
description: Déployez votre première application Python hello world dans Azure App Service sur Linux en quelques minutes.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: cephalin
ms.custom: seo-python-october2019
experimental: true
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: e8ca84f233b3e6202a4647d15e07b36c2b8f1128
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433138"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Démarrage rapide : Créer une application Python dans Azure App Service sur Linux

Dans cet article, vous allez déployer une application Python simple dans [App Service sur Linux](app-service-linux-intro.md), qui fournit un service d’hébergement web hautement scalable et autocorrectif. Vous allez utiliser l’interface de ligne de commande Azure ([Azure CLI](/cli/azure/install-azure-cli)) via Azure Cloud Shell, qui est interactif et basé sur le navigateur. Ainsi, vous pourrez suivre les étapes pour un ordinateur Mac, Linux ou Windows.

![Exécuter un exemple d’application Python dans App Service dans Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

* <a href="https://www.python.org/downloads/" target="_blank">Installez Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Installez Git</a>
* Un abonnement Azure. Si vous n’en avez pas déjà un, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

## <a name="download-the-sample-locally"></a>Téléchargez l’exemple localement

Dans une fenêtre de terminal, exécutez les commandes suivantes pour cloner l’exemple d’application sur votre machine locale, puis accédez au répertoire contenant l’exemple de code.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Le référentiel contient un fichier *application.py*, qui indique à App Service que le référentiel contient une application Flask. Pour plus d’informations, consultez [Processus de démarrage et personnalisations du conteneur](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Exécutez l’application localement.

Exécutez l’application localement pour voir à quoi elle devrait ressembler lorsque vous la déploierez sur Azure. Ouvrez une fenêtre de terminal et utilisez les commandes ci-dessous pour installer les dépendances requises et lancer le serveur de développement intégré. 

```bash
# In Bash (for Linux or Mac)
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run
```
```powershell
# In Powershell (for Windows)
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Ouvrez un navigateur web et accédez à l’exemple d’application à l’adresse `http://localhost:5000/`.

Vous voyez apparaître sur la page le message **Hello World !** de l’exemple d’application.

![Exécuter un exemple d’application Python localement](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl + C** pour quitter le serveur web.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans Cloud Shell, créez un répertoire de démarrage rapide, puis utilisez-le.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Exécutez ensuite la commande suivante pour cloner le référentiel de l’exemple d’application sur votre répertoire de démarrage rapide.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Pendant son exécution, des informations semblables à ce qui suit s’affichent :

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Créer une application web

Passez au répertoire qui contient l’exemple de code et exécutez la commande `az webapp up`.

Dans l’exemple suivant, remplacez `<app-name>` par un nom d’application unique (*les caractères valides sont `a-z`, `0-9` et `-`* ).

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

L’exécution de cette commande peut prendre quelques minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web.

```bash
http://<app-name>.azurewebsites.net
```

L’exemple de code Python s’exécute dans App Service sur Linux avec une image intégrée.

![Exécuter un exemple d’application Python dans Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Félicitations !** Vous venez de déployer votre première application Python sur App Service sur Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Mettre à jour localement et redéployer le code

Dans Cloud Shell, entrez `code application.py` pour ouvrir l’éditeur Cloud Shell.

![Ouvrir application.py dans l’éditeur Cloud Shell](./media/quickstart-python/open-application-py-in-the-cloud-shell-editor.png)

 Apportez une petite modification au texte de l’appel pour `return` :

```python
return "Hello Azure!"
```

Enregistrez vos modifications et quittez l’éditeur. Utilisez la commande `^S` pour enregistrer et `^Q` pour quitter.

Redéployez l’application à l’aide de la commande [`az webapp up`](/cli/azure/webapp#az-webapp-up). Remplacez le nom de votre application par `<app-name>`, puis spécifiez un emplacement pour `<location-name>` (en utilisant l’une des valeurs indiquées dans la commande [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations)).

```bash
az webapp up -n <app-name> -l <location-name>
```

Une fois le déploiement terminé, revenez à la fenêtre du navigateur que vous avez ouverte à l’étape **Accéder à l’application**, puis actualisez la page.

![Exécuter un exemple d’application Python mis à jour dans Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gérer votre nouvelle application Azure

Accédez au <a href="https://portal.azure.com" target="_blank">portail Azure</a> pour gérer l’application que vous avez créée.

Dans le menu de gauche, sélectionnez **App Services**, puis sélectionnez le nom de votre application Azure.

![Accéder à votre application Python dans App Services dans le portail Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

La page Vue d’ensemble de votre application s’affiche. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple).

![Gérer votre application Python dans la page Vue d’ensemble du portail Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Le menu de gauche fournit différentes pages vous permettant de configurer votre application. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application web Python (Django) avec PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurer une application Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutoriel : Exécuter une application Python dans un conteneur personnalisé](tutorial-custom-docker-image.md)
