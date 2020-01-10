---
title: 'Démarrage rapide : Créer une application Python Linux'
description: Découvrez comment bien démarrer avec les applications Linux sur Azure App Service en déployant votre première application Python sur un conteneur Linux dans App Service.
ms.topic: quickstart
ms.date: 10/22/2019
ms.custom: seo-python-october2019
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 67fbffbe96bc32b6ec38fa75c1e754c7f11d38d6
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687480"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Démarrage rapide : Créer une application Python dans Azure App Service sur Linux

Dans ce démarrage rapide, vous déployez une application web Python dans [App Service sur Linux](app-service-linux-intro.md), le service d’hébergement web hautement scalable et autocorrectif d’Azure. Vous utilisez l’[interface de ligne de commande (CLI) Azure](/cli/azure/install-azure-cli) locale sur un ordinateur Mac, Linux ou Windows. L’application web que vous configurez utilise un niveau App Service gratuit. Vous n’êtes donc pas facturé quand vous suivez la procédure décrite dans cet article.

Si vous préférez déployer des applications via un IDE, consultez [Déployer des applications Python sur App Service à partir de Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 est également pris en charge)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Interface de ligne de commande Azure</a>

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner l’exemple d’application sur votre ordinateur local. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ensuite, accédez à ce dossier :

```terminal
cd python-docs-hello-world
```

Le dépôt contient un fichier *application.py*, qui indique à App Service que le code contient une application Flask. Pour plus d’informations, consultez [Processus de démarrage et personnalisations du conteneur](how-to-configure-python.md).

## <a name="run-the-sample"></a>Exécution de l'exemple

Dans une fenêtre de terminal, utilisez les commandes ci-dessous (qui diffèrent en fonction du système d’exploitation) pour installer les dépendances requises et lancer le serveur de développement intégré. 

# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Ouvrez un navigateur web et accédez à l’exemple d’application à l’adresse `http://http://<app-name>.azurewebsites.net`. L’application affiche le message **Hello World !** .

![Exécuter un exemple d’application Python localement](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl**+**C** pour quitter le serveur web.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Azure CLI fournit de nombreuses commandes pratiques que vous pouvez utiliser à partir d’un terminal local pour provisionner et gérer des ressources Azure à partir de la ligne de commande. Vous pouvez utiliser des commandes pour effectuer les mêmes tâches que dans le Portail Azure dans un navigateur. Vous pouvez également utiliser des commandes CLI dans des scripts pour automatiser les processus de gestion.

Pour exécuter des commandes Azure dans Azure CLI, vous devez d’abord vous connecter à l’aide de la commande `az login`. Cette commande ouvre un navigateur pour recueillir vos informations d’identification.

```terminal
az login
```

## <a name="deploy-the-sample"></a>Déployer l'exemple

La commande [`az webapp up`](/cli/azure/webapp#az-webapp-up) crée l’application web sur App Service et déploie votre code.

Dans le dossier *python-docs-hello-world* contenant l’exemple de code, exécutez la commande `az webapp up` suivante. Remplacez `<app-name>` par un nom d’application global unique (les *caractères valides sont `a-z`, `0-9` et `-`* ). Remplacez également `<location-name>` par une région Azure telle que **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, etc. (Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations).)


```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Cette commande peut prendre plusieurs minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
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
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web à l’URL `http://<app-name>.azurewebsites.net`.

L’exemple de code Python exécute un conteneur Linux dans App Service avec une image intégrée.

![Exécuter un exemple d’application Python dans Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Félicitations !** Vous venez de déployer votre application Python sur App Service sur Linux.

## <a name="redeploy-updates"></a>Redéployer les mises à jour

Dans votre éditeur de code favori, ouvrez *application.py* et modifiez l’instruction `return` sur la dernière ligne pour qu’elle corresponde au code suivant. L’instruction `print` est incluse ici pour générer la sortie de journalisation que vous utilisez dans la section suivante. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Enregistrez vos modifications et quittez l’éditeur. 

Redéployez l’application à l’aide de la commande `az webapp up` suivante, en utilisant la même commande que celle utilisée pour déployer l’application la première fois, en remplaçant `<app-name>` et `<location-name>` par les mêmes noms que ceux que vous avez utilisés précédemment. 

```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte pour `http://<app-name>.azurewebsites.net` et actualisez la page, qui doit afficher le message modifié :

![Exécuter un exemple d’application Python mis à jour dans Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code fournit des extensions puissantes pour Python et Azure App Service, qui simplifient le processus de déploiement d’applications web Python sur App Service. Pour plus d’informations, consultez [Déployer des applications Python sur App Service à partir de Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Diffuser les journaux d’activité en continu

Vous pouvez accéder aux journaux de la console générés à l’intérieur de l’application et au conteneur dans lequel elle s’exécute. Les journaux incluent toute sortie générée à l’aide d’instructions `print`.

Tout d’abord, activez la journalisation de conteneur en exécutant la commande suivante dans un terminal, en remplaçant `<app-name>` par le nom de votre application et `<resource-group-name>` par le nom du groupe de ressources indiqué dans la sortie de la commande `az webapp up` que vous avez utilisée (par exemple « appsvc_rg_Linux_centralus » ) :

```terminal
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Une fois la journalisation du conteneur activée, exécutez la commande suivante pour voir le flux de journal :

```terminal
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Actualisez l’application dans le navigateur pour générer des journaux de console, qui doivent inclure des lignes similaires au texte suivant. Si vous ne voyez pas la sortie immédiatement, réessayez dans 30 secondes.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Vous pouvez également inspecter les fichiers journaux à partir du navigateur sur `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pour arrêter le streaming des journaux à tout moment, appuyez sur `Ctrl`+`C`.

## <a name="manage-the-azure-app"></a>Gérer l’application Azure

Accédez au <a href="https://portal.azure.com" target="_blank">portail Azure</a> pour gérer l’application que vous avez créée. Recherchez et sélectionnez **App Services**.

![Accéder à App Service dans le portail Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Sélectionnez le nom de votre application Azure.

![Accéder à votre application Python dans App Services dans le portail Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

La page Vue d’ensemble de votre application s’affiche. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple).

![Gérer votre application Python dans la page Vue d’ensemble du portail Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Le menu App Service fournit différentes pages vous permettant de configurer votre application.

## <a name="clean-up-resources"></a>Supprimer des ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Le groupe de ressources a un nom tel que « appsvc_rg_Linux_CentralUS » en fonction de votre emplacement. Si vous utilisez une référence App Service autre que le niveau F1 gratuit, ces ressources génèrent des coûts permanents.

Si vous n’envisagez pas d’avoir besoin de ces ressources à l’avenir, supprimez le groupe de ressources en exécutant la commande suivante, en remplaçant `<resource-group-name>` par le groupe de ressources affiché dans la sortie de la commande `az webapp up`, par exemple « appsvc_rg_Linux_centralus ». L’exécution de cette commande peut prendre une minute.

```terminal
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application web Python (Django) avec PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurer une application Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutoriel : Exécuter une application Python dans un conteneur personnalisé](tutorial-custom-docker-image.md)
