---
title: 'Démarrage rapide : Créer une application Python'
description: Commencez à utiliser Azure App Service en déployant votre première application Python sur un conteneur Linux dans App Service.
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 77aafb53c1346c6723d055a8ae1c96297fdfbd52
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568912"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Démarrage rapide : Créer une application Python dans Azure App Service sur Linux

Dans ce démarrage rapide, vous déployez une application web Python dans [App Service sur Linux](overview.md#app-service-on-linux), le service d’hébergement web hautement scalable et autocorrectif d’Azure. Vous utilisez l’[interface de ligne de commande (CLI) Azure](/cli/azure/install-azure-cli) locale sur un ordinateur Mac, Linux ou Windows. L’application web que vous configurez utilise un niveau App Service gratuit. Vous n’êtes donc pas facturé quand vous suivez la procédure décrite dans cet article.

> [!NOTE]
> Si vous préférez déployer des applications via un IDE, consultez **[Déployer des applications Python sur App Service à partir de Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** .

## <a name="set-up-your-initial-environment"></a>Configurer votre environnement initial

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

1. Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installez <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 ou une version ultérieure</a>.
1. Installez l’interface <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>2.0.80 ou ultérieur, avec laquelle vous exécutez des commandes dans n’importe quel interpréteur de commandes pour provisionner et configurer des ressources Azure.

Ouvrez une fenêtre de terminal et vérifiez que la version de Python est bien la version 3.6 ou une version ultérieure :

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Vérifiez également que votre version d’Azure CLI est la version 2.0.80 ou une version ultérieure :

```azurecli
az --version
```

Connectez-vous ensuite à Azure par le biais de l’interface CLI :

```azurecli
az login
```

Cette commande ouvre un navigateur pour recueillir vos informations d’identification. Une fois l’exécution de la commande terminée, elle affiche une sortie JSON contenant des informations sur vos abonnements.

Une fois connecté, vous pouvez exécuter des commandes Azure avec l’interface de ligne de commande Azure CLI pour utiliser des ressources de votre abonnement.

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Clonage de l’exemple

Clonez l’exemple de dépôt à l’aide de la commande suivante, puis accédez au dossier. ([Installez Git](https://git-scm.com/downloads) si vous ne l’avez pas déjà fait.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

L’exemple de code contient un fichier *application.py*, qui indique à App Service que le code contient une application Flask. Pour plus d’informations, consultez [Processus de démarrage du conteneur](configure-language-python.md#container-startup-process).

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Exécution de l'exemple

# <a name="bash"></a>[Bash](#tab/bash)

Commencez par créer un environnement virtuel et installez les dépendances :

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Définissez ensuite la variable d’environnement `FLASK_APP` sur le module d’entrée de l’application, puis exécutez le serveur de développement Flask :

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Commencez par créer un environnement virtuel et installez les dépendances :

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Définissez ensuite la variable d’environnement `FLASK_APP` sur le module d’entrée de l’application, puis exécutez le serveur de développement Flask :

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Commencez par créer un environnement virtuel et installez les dépendances :

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Définissez ensuite la variable d’environnement `FLASK_APP` sur le module d’entrée de l’application, puis exécutez le serveur de développement Flask :

```cmd
SET FLASK_APP=application.py
flask run
```

---

Ouvrez un navigateur web et accédez à l’exemple d’application à l’adresse `http://localhost:5000/`. L’application affiche le message **Hello World !** .

![Exécuter un exemple d’application Python localement](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Dans la fenêtre de terminal, appuyez sur **Ctrl**+**C** pour quitter le serveur de développement Flask.

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Déployer l'exemple

Déployez le code dans votre dossier local (*python-docs-hello-world*) à l’aide de la commande `az webapp up` :

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Si la commande `az` n’est pas reconnue, vérifiez qu’Azure CLI est installé, comme décrit dans [Configurer votre environnement initial](#set-up-your-initial-environment).
- Remplacez `<app_name>` par un nom unique sur l’ensemble d’Azure (*les caractères valides sont `a-z`, `0-9` et `-`* ). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.
- L’argument `--sku F1` crée l’application web sur le niveau tarifaire Gratuit. Omettez cet argument pour utiliser un niveau Premium plus rapide, ce qui entraîne un coût horaire.
- Si vous le souhaitez, vous pouvez inclure l’argument `-l <location-name>`, dans lequel `<location_name>` est une région Azure, telle que **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, etc. Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations).
- Si vous voyez l’erreur « Impossible de détecter automatiquement la pile d’exécution de votre application », veillez à exécuter la commande dans le dossier *python-docs-hello-world* qui contient le fichier *requirements.txt*. (Consultez [Résolution des problèmes de détection automatique avec az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

La commande peut prendre quelques minutes. Lors de son exécution, elle fournit des messages sur la création du groupe de ressources, le plan App Service et l’application d’hébergement, la configuration de la journalisation, puis le déploiement ZIP. Elle affiche ensuite le message « Vous pouvez lancer l’application sur http://&lt;app-name&gt;.azurewebsites.net », qui est l’URL de l’application sur Azure.

![Exemple de sortie de la commande az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

[!INCLUDE [AZ Webapp Up Note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée à l’aide de votre navigateur web à l’URL `http://<app-name>.azurewebsites.net`.

L’exemple de code Python exécute un conteneur Linux dans App Service avec une image intégrée.

![Exécuter un exemple d’application Python dans Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Félicitations !** Vous venez de déployer votre application Python sur App Service.

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Redéployer les mises à jour

Dans l’éditeur de code de votre choix, ouvrez *application.py* et mettez à jour la fonction `hello` comme suit. Cette modification ajoute une instruction `print` pour générer la sortie de journalisation que vous allez utiliser à la section suivante. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Enregistrez vos modifications et quittez l’éditeur. 

Redéployez l’application en utilisant de nouveau la commande `az webapp up` :

```azurecli
az webapp up
```

Cette commande utilise des valeurs qui sont mises en cache localement dans le fichier *.azure/config*, notamment le nom de l’application, le groupe de ressources et le plan App Service.

Une fois le déploiement terminé, revenez à la fenêtre du navigateur ouverte sur `http://<app-name>.azurewebsites.net`. Actualisez la page, qui doit afficher le message modifié :

![Exécuter un exemple d’application Python mis à jour dans Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code fournit des extensions puissantes pour Python et Azure App Service, qui simplifient le processus de déploiement d’applications web Python sur App Service. Pour plus d’informations, consultez [Déployer des applications Python sur App Service à partir de Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Diffuser les journaux d’activité en continu

Vous pouvez accéder aux journaux de la console générés à l’intérieur de l’application et au conteneur dans lequel elle s’exécute. Les journaux incluent toute sortie générée à l’aide d’instructions `print`.

Pour envoyer les journaux en streaming, exécutez la commande suivante :

```azurecli
az webapp log tail
```

Actualisez l’application dans le navigateur pour générer des journaux de console, qui incluent des messages décrivant les requêtes HTTP envoyées à l’application. Si aucune sortie ne s’affiche immédiatement, réessayez dans 30 secondes.

Vous pouvez également inspecter les fichiers journaux à partir du navigateur sur `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pour arrêter le streaming de journaux à tout moment, appuyez sur **Ctrl**+**C**.

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Gérer l’application Azure

Accédez au <a href="https://portal.azure.com" target="_blank">portail Azure</a> pour gérer l’application que vous avez créée. Recherchez et sélectionnez **App Services**.

![Accéder à App Service dans le portail Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Sélectionnez le nom de votre application Azure.

![Accéder à votre application Python dans App Services dans le portail Azure](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

La sélection de l’application ouvre la page **Vue d’ensemble**, dans laquelle vous pouvez effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer).

![Gérer votre application Python dans la page Vue d’ensemble du portail Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Le menu App Service fournit différentes pages vous permettant de configurer votre application.

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Le groupe de ressources a un nom tel que « appsvc_rg_Linux_CentralUS » en fonction de votre emplacement. Si vous utilisez une référence SKU App Service autre que le niveau F1 gratuit, ces ressources entraînent des coûts récurrents (consultez [Tarifs App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de ressources en exécutant la commande suivante :

```azurecli
az group delete
```

La commande utilise le nom du groupe de ressources mis en cache dans le fichier *.azure/config*.

L’exécution de cette commande peut prendre une minute.

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Application web Python (Django) avec PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Ajouter une connexion utilisateur à une application web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Configurer une application Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Tutoriel : Exécuter une application Python dans un conteneur personnalisé](tutorial-custom-container.md)
