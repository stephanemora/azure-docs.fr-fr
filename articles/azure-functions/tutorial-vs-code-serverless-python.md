---
title: Créer et déployer Azure Functions dans Python avec Visual Studio Code
description: Explique comment utiliser l’extension Visual Studio Code pour Azure Functions dans le but de créer des fonctions serverless dans Python et de les déployer dans Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: f5591a3e0ca73649b1ffc51c75aa95e86e286768
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639094"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Déployer Python sur Azure Functions avec Visual Studio Code

Dans ce tutoriel, vous allez utiliser Visual Studio Code et l’extension Azure Functions pour créer un point de terminaison HTTP serverless avec Python et pour ajouter une connexion (ou « liaison ») au stockage. Azure Functions exécute votre code dans un environnement serverless sans nécessiter le provisionnement d’une machine virtuelle ou la publication d’une application web. L’extension Azure Functions pour Visual Studio Code simplifie grandement le processus d’utilisation d’Azure Functions en permettant de gérer automatiquement de nombreux problèmes de configuration.

Si vous rencontrez des problèmes lors d’une étape de ce tutoriel, nous vous invitons à nous contacter pour nous expliquer en détail ce qu’il s’est passé. Pour cela, utilisez le bouton **J’ai rencontré un problème** situé à la fin de chaque section pour nous envoyer des commentaires détaillés.

## <a name="prerequisites"></a>Prérequis

- Un [abonnement Azure](#azure-subscription).
- L’extension [Visual Studio Code avec Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension).
- [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Abonnement Azure

Si vous n’avez pas d’abonnement Azure, [inscrivez-vous dès maintenant](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) pour obtenir un compte gratuit de 30 jours avec 200 $ de crédits Azure, ce qui vous permettra d’essayer toutes les combinaisons de services.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python et l’extension Azure Functions

Installez les logiciels suivants :

- Python 3.6.x (nécessaire pour Azure Functions). [Python 3.6.8](https://www.python.org/downloads/release/python-368/) est la dernière version de 3.6.x.
- [Visual Studio Code](https://code.visualstudio.com/).
- L’[extension Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), comme décrit dans [Tutoriel Visual Studio Code pour Python - Prérequis](https://code.visualstudio.com/docs/python/python-tutorial).
- L’[extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Pour obtenir des informations générales, consultez le [dépôt GitHub vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Suivez les instructions correspondant à votre système d’exploitation dans [Utiliser Azure Functions Core Tools](functions-run-local.md#v2). Les outils sont écrits à l’aide de .NET Core, et il est préférable d’installer le package Core Tools à l’aide du gestionnaire de package (NPM) Node.js. Ceci explique pourquoi vous devez installer .NET Core et Node.js, même pour le code Python. Toutefois, vous pouvez contourner l’exigence .NET Core à l’aide de « bundles d’extensions ». Ce processus est décrit dans la documentation mentionnée précédemment. Quoi qu’il en soit, vous ne devez installer ces composants qu’une seule fois. Une fois ceux-ci installés, Visual Studio Code vous invite automatiquement à installer les mises à jour.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Une fois l’extension Functions installée, connectez-vous à votre compte Azure en accédant à l’explorateur **Azure : Functions**, sélectionnez **Se connecter à Azure**, puis suivez les invites.

![Se connecter à Azure via Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Une fois connecté, vérifiez que le compte e-mail de votre abonnement Azure s’affiche dans la barre d’état :

![Barre d’état Visual Studio Code affichant le compte Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Le nom que vous avez attribué à votre abonnement s’affiche également dans l’explorateur **Azure : Functions** (« Primary » dans l’image ci-dessous) :

![Explorateur Azure App Service Visual Studio Code où figurent les abonnements](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Si vous rencontrez l’erreur **« Cannot find subscription with name [subscription ID] »** (L’abonnement avec le nom [ID d’abonnement] est introuvable), cela peut être dû au fait que vous vous trouvez derrière un proxy qui vous empêche d’atteindre l’API Azure. Configurez les variables d’environnement `HTTP_PROXY` et `HTTPS_PROXY` dans votre terminal à l’aide de vos informations de proxy :
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Vérifier la configuration requise

Pour vérifier que tous les outils Azure Functions ont été installés, ouvrez la palette de commandes Visual Studio Code (F1), sélectionnez la commande **Terminal : Créer un terminal intégré**, et une fois que le terminal s’ouvre, exécutez la commande `func` :

![Vérification des prérequis concernant Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-prereqs.png)

La sortie qui commence par le logo Azure Functions (vous devez faire défiler la sortie vers le haut) indique que les outils Azure Functions Core Tools sont bien présents.

Si la commande `func` n’est pas reconnue, vérifiez que le dossier dans lequel vous avez installé Azure Functions Core Tools est inclus dans votre variable d’environnement PATH.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Création de la fonction

1. Le code Azure Functions est géré dans un _projet_ Functions, que vous devez créer avant de créer le code. Dans l’explorateur **Azure : Functions** (ouvert à l’aide de l’icône Azure sur le côté gauche), sélectionnez l’icône de la commande **Nouveau projet**, ou ouvrez la palette de commandes (F1), puis sélectionnez **Azure Functions : Créer un projet**.

    ![Bouton Créer un projet dans l’explorateur Functions](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. Dans les invites qui suivent :

    | Prompt | Valeur | Description | 
    | --- | --- | --- |
    | Spécifier un dossier pour le projet | Dossier actuellement ouvert | Dossier dans lequel créer le projet. Vous pouvez créer le projet dans un sous-dossier. |
    | Sélectionnez un langage pour votre projet d’application de fonction | **Python** | Langage à utiliser pour la fonction, qui détermine le modèle utilisé pour le code. |
    | Sélectionner un modèle pour la première fonction de votre projet | **Déclencheur HTTP** | Fonction qui utilise un déclencheur HTTP qui est exécuté chaque fois qu’une requête HTTP est envoyée au point de terminaison de la fonction. Il existe de nombreux autres déclencheurs pour Azure Functions. Pour plus d’informations, consultez [Que puis-je faire avec Azure Functions ?](functions-overview.md#what-can-i-do-with-functions) |
    | Fournir un nom de fonction | HttpExample | Nom utilisé pour un sous-dossier qui contient le code de la fonction, ainsi que des données de configuration. Il définit également le nom du point de terminaison HTTP. Utilisez « HttpExample » au lieu d’accepter le « HTTPTrigger » par défaut, afin de distinguer la fonction du déclencheur. |
    | Niveau d’autorisation | **Anonyme** | L’autorisation Anonyme rend la fonction publiquement accessible à quiconque. |
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | **Ouvrir dans la fenêtre active** | Ouvre le projet dans la fenêtre Visual Studio Code active. |

1. Après un bref laps de temps, un message indique que le nouveau projet a été créé. Le sous-dossier créé pour la fonction se trouve dans l’**Explorateur** et Visual Studio Code ouvre le fichier *\_\_init\_\_.py* qui contient le code de fonction par défaut :

    [![Résultat de la création d’un projet Functions pour Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Si Visual Studio Code vous indique qu’aucun interpréteur Python n’est sélectionné lorsqu’il ouvre *\_\_init\_\_.py*, ouvrez la palette de commandes (F1), sélectionnez la commande **Python : Sélectionner un interpréteur**, puis sélectionnez l’environnement virtuel dans le dossier local `.env` (qui a été créé dans le cadre du projet). L’environnement doit être basé sur Python 3.6x, comme indiqué dans les [Prérerequis](#prerequisites).
    >
    > ![Sélection de l’environnement virtuel créé avec le projet](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Chaque fois que vous souhaitez créer une autre fonction dans un même projet, utilisez la commande **Créer une fonction** dans l’explorateur **Azure : Functions**, ou ouvrez la palette de commandes (F1) et sélectionnez **Azure Functions : Créer une fonction**. Les deux commandes vous invitent à entrer un nom de fonction (c’est-à-dire, le nom du point de terminaison), puis elles créent un sous-dossier avec les fichiers par défaut.
>
> ![Fonction Nouvelle commande dans l’explorateur Azure : Functions](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Examiner le fichiers de code

Le sous-dossier de fonction qui vient d’être créé contient trois fichiers : *\_\_init\_\_.py*, qui contient le code de la fonction, *function.json*, qui décrit la fonction à Azure Functions, et *sample.dat*, qui est un exemple de fichier de données. Vous pouvez supprimer *sample.dat* si vous le souhaitez, car il sert uniquement à montrer que vous pouvez ajouter d’autres fichiers au sous-dossier.

Intéressons-nous d’abord à *function.json*, puis au code de *\_\_init\_\_.py*.

### <a name="functionjson"></a>function.json

Le fichier function.json fournit les informations de configuration nécessaires pour le point de terminaison Azure Functions :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

La propriété `scriptFile` identifie le fichier de démarrage du code, et ce code doit contenir une fonction Python nommée `main`. Vous pouvez factoriser votre code dans plusieurs fichiers tant que le fichier spécifié ici contient une fonction `main`.

L’élément `bindings` contient deux objets, un pour décrire les requêtes entrantes, et l’autre pour décrire la réponse HTTP. Pour les requêtes entrantes (`"direction": "in"`), la fonction répond aux requêtes HTTP GET ou POST qui ne nécessitent pas d’authentification. La réponse (`"direction": "out"`) est une réponse HTTP qui retourne la valeur retournée par la fonction Python `main`.

### <a name="__initpy__"></a>\_\_init.py\_\_

Quand vous créez une fonction, Azure Functions fournit le code Python par défaut dans *\_\_init\_\_.py* :

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Les parties importantes du code sont les suivantes :

- Vous devez importer `func` à partir de `azure.functions`. L’importation du module de journalisation est facultative, mais recommandée.
- La fonction Python `main` nécessaire reçoit un objet `func.request` nommé `req`, et retourne une valeur de type `func.HttpResponse`. Pour plus d’informations sur les fonctionnalités de ces objets, consultez les références [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) et [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python).
- Le corps de `main` traite ensuite la requête et génère une réponse. Dans ce cas, le code recherche un paramètre `name` dans l’URL. En cas d’échec, il vérifie si le corps de la requête contient du code JSON (à l’aide de `func.HttpRequest.get_json`) et si le JSON contient une valeur `name` (à l’aide de la méthode `get` de l’objet JSON retourné par `get_json`).
- Si un nom est trouvé, le code retourne la chaîne « Hello » avec le nom ajouté. Sinon, elle retourne un message d’erreur.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Déboguer en local

1. Lorsque vous créez le projet Functions, l’extension Visual Studio Code crée également une configuration de lancement dans `.vscode/launch.json` qui contient une configuration unique nommée **Attach to Python Functions**. Avec cette configuration, vous pouvez simplement appuyer sur F5 ou utiliser l’Explorateur de débogage pour démarrer le projet :

    ![Explorateur de débogage montrant la configuration de lancement de Functions](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. Quand vous démarrez le débogueur, un terminal s’ouvre et affiche la sortie Azure Functions, y compris la liste des points de terminaison disponibles. Votre URL peut être différente si vous avez utilisé un nom autre que « HttpExample » :

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Dans la fenêtre **Sortie** de Visual Studio Code, utilisez **Ctrl + clic** ou **Cmd + clic** sur l’URL pour ouvrir un navigateur et accéder à cette adresse. Vous pouvez aussi démarrer un navigateur et y coller cette même URL. Quoi qu’il en soit, le point de terminaison est `api/<function_name>` (dans ce cas, `api/HttpExample`). Toutefois, étant donné que cette URL n’inclut pas de paramètre Name, la fenêtre du navigateur doit seulement afficher « Please pass a name on the query string or in the request body » (Passez un nom dans la chaîne de requête ou dans le corps de la requête), selon le chemin de votre code.

1. Essayez maintenant d’ajouter un paramètre Name à l’utilisation, par exemple, `http://localhost:7071/api/HttpExample?name=VS%20Code`. La fenêtre du navigateur doit afficher le message « Hello Visual Studio Code! », ce qui prouve que vous avez exécuté ce chemin de code.

1. Pour passer la valeur de nom dans un corps de requête JSON, vous pouvez utiliser un outil de type curl avec JSON inline :

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Vous pouvez également créer un fichier comme *data.json* qui contient `{"name":"Visual Studio Code"}`, et utiliser la commande `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Pour tester le débogage de la fonction, définissez un point d’arrêt sur la ligne `name = req.params.get('name')` et renvoyez une requête à l’URL. Le débogueur Visual Studio Code doit s’arrêter sur cette ligne, ce qui vous permet d’examiner les variables et d’analyser le code pas à pas. Pour suivre une courte procédure pas à pas de débogage de base, consultez [Tutoriel Visual Studio Code - Configurer et exécuter le débogueur](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).

1. Une fois que vous avez testé la fonction localement de manière suffisamment approfondie, arrêtez le débogueur (à l’aide de la commande de menu **Déboguer** > **Arrêter le débogage** ou de la commande **Déconnecter** dans la barre d’outils de débogage).

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Déployer sur Azure Functions

Dans ces étapes, vous allez utiliser l’extension Functions pour créer une application de fonction dans Azure, ainsi que d’autres ressources Azure nécessaires. Une Function App vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Elle nécessite également un compte de stockage Azure pour les données et pour un [plan d’hébergement](functions-scale.md#hosting-plan-support). Toutes ces ressources sont organisées au sein d’un même groupe de ressources.

1. Dans la zone **Azure : Functions**, sélectionnez la commande **Deploy to Function App** (Déployer vers l’application de fonction) ou ouvrez la palette de commandes (F1) et sélectionnez la commande **Azure Functions : Deploy to Function App** (Déployer vers l’application de fonction). Là encore, c’est dans l’application de fonction que votre projet Python s’exécute dans Azure.

    ![Commande Déployer vers l’application de fonction](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Lorsque vous y êtes invité, sélectionnez **Create New Function App in Azure** (Créer une application de fonction dans Azure), puis indiquez un nom unique dans tout Azure. Celui-ci peut être composé de votre nom ou de celui de votre entreprise, ainsi que d’autres identificateurs uniques (vous devez uniquement utiliser des lettres, des chiffres et des traits d’union). Si vous avez déjà créé une application de fonction, le nom de celle-ci apparaît dans cette liste d’options.

1. L’extension effectue les actions suivantes, que vous pouvez observer dans les messages contextuels Visual Studio Code et dans la fenêtre **Sortie** (ce processus prend quelques minutes) :

    - Créez un groupe de ressources en lui donnant le nom que vous avez attribué à l’application (en supprimant les traits d’union).
    - Dans ce groupe de ressources, créez le compte de stockage, le plan d’hébergement et l’application de fonction. Par défaut, un [plan de consommation](functions-scale.md#consumption-plan) est créé. Pour exécuter vos fonctions dans un plan dédié, vous devez [activer la publication avec les options de création avancées](functions-develop-vs-code.md).
    - Déployez votre code dans l’application de fonction.

    L’explorateur **Azure : Functions** affiche également la progression :

    ![Indicateur de progression du déploiement dans l’explorateur Azure : Functions](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Une fois le déploiement terminé, l’extension Azure Functions affiche un message comprenant les boutons de trois actions supplémentaires :

    ![Message indiquant que le déploiement a réussi avec des actions supplémentaires](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Pour **Stream logs** (Diffuser les journaux en streaming) et **Upload settings** (Charger des paramètres), consultez les sections suivantes. Pour **View output** (Afficher la sortie), consultez l’étape 5 qui suit.

1. Après le déploiement, la fenêtre **Sortie** affiche également le point de terminaison public dans Azure :

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Utilisez ce point de terminaison pour exécuter les mêmes tests que vous avez effectués localement, en utilisant des paramètres d’URL et/ou des requêtes dont le corps contient des données JSON. Les résultats du point de terminaison public doivent correspondre aux résultats du point de terminaison local que vous avez testé précédemment.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Diffuser les journaux d’activité en continu

La prise en charge du streaming des journaux est en cours de développement (comme expliqué dans le [problème 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) concernant l’extension Azure Functions). Le bouton **Stream logs** (Diffuser les journaux en streaming) situé dans la fenêtre contextuelle de déploiement permet de connecter la sortie du journal Azure à Visual Studio Code. Vous pouvez également démarrer et arrêter la diffusion en streaming des journaux dans l’Explorateur **Azure Functions** en cliquant avec le bouton droit sur le projet Functions et en sélectionnant **Start streaming logs** (Démarrer le streaming des journaux) ou **Stop streaming logs** (Arrêter le streaming des journaux).

Toutefois, ces commandes ne sont pas encore opérationnelles. Vous pouvez également utiliser le streaming des journaux dans un navigateur à l’aide de la commande suivante, en remplaçant `<app_name>` par le nom de votre application de fonction sur Azure :

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Synchroniser les paramètres locaux avec Azure

Le bouton **Upload settings** (Charger les paramètres) situé dans la fenêtre contextuelle de déploiement permet d’appliquer toutes les modifications que vous avez apportées à votre fichier *local.settings.json* dans Azure. Vous pouvez également appeler la commande dans l’**Explorateur** Azure Functions en développant le nœud de projet Functions, en cliquant avec le bouton droit sur **Paramètres d’application**, puis en sélectionnant **Upload local settings** (Charger les paramètres locaux). Vous pouvez également utiliser la palette de commandes pour sélectionner la commande **Azure Functions : Upload Local Settings** (Charger les paramètres locaux).

Le chargement de paramètres met à jour les paramètres existants et ajoute les nouveaux paramètres définis dans *local.settings.json*. Le chargement ne supprime pas d’Azure les paramètres qui ne sont pas listés dans le fichier local. Pour supprimer ces paramètres, développez le nœud **Paramètres d’application** dans l’Explorateur **Azure Functions**, cliquez avec le bouton droit sur le paramètre, puis sélectionnez **Supprimer le paramètre**. Vous pouvez également modifier les paramètres directement dans le portail Azure.

Pour appliquer les modifications que vous apportez via le portail ou via l’**Explorateur Azure** dans le fichier *local.settings.json*, cliquez avec le bouton droit sur le nœud **Paramètres d’application**, puis sélectionnez la commande **Download remote settings** (Télécharger les paramètres distants). Vous pouvez également utiliser la palette de commandes pour sélectionner la commande **Azure Functions : Télécharger les paramètres distants**.

## <a name="add-a-second-function"></a>Ajouter une deuxième fonction

Après votre premier déploiement, vous pouvez apporter des modifications à votre code, telles que des fonctions supplémentaires, puis les redéployer vers la même application de fonction.

1. Dans la zone **Azure : Functions**, sélectionnez la commande **Créer une fonction** ou utilisez **Azure Functions : Créer une fonction** à partir de la palette de commandes. Spécifiez les informations suivantes pour la fonction :

    - Modèle : Déclencheur HTTP
    - Nom : "DigitsOfPi"
    - Niveau d’autorisation : Anonyme

1. Dans l’Explorateur Visual Studio Code se trouve un sous-dossier portant le nom de votre fonction et qui contient les fichiers *\_\_init\_\_.py*, *function.json* et *sample.dat*.

1. Remplacez le contenu de *\_\_init\_\_.py* pour qu’il corresponde au code suivant, qui génère une chaîne contenant la valeur PI, selon le nombre de chiffres spécifiés dans l’URL (ce code utilise uniquement un paramètre d’URL)

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Étant donné que le code prend uniquement en charge HTTP GET, modifiez *function.json* pour que la collection `"methods"` contienne uniquement `"get"` (autrement dit, supprimez `"post"`). Le fichier entier doit se présenter ainsi :

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Démarrez le débogueur en appuyant sur F5 ou en sélectionnant la commande de menu **Déboguer** > **Démarrer le débogage**. La fenêtre **Sortie** doit maintenant afficher les deux points de terminaison de votre projet :

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Dans un navigateur, ou dans curl, envoyez une requête `http://localhost:7071/api/DigitsOfPi?digits=125` et examinez la sortie (vous remarquerez peut-être que l’algorithme du code n’est pas tout à fait correct, mais nous vous laissons le soin de l’améliorer !). Arrêtez le débogueur lorsque vous avez terminé.

1. Redéployez le code à l’aide de l’option **Deploy to Function App** (Déployer vers l’application de fonction) dans l’explorateur **Azure : Functions**. Si vous y êtes invité, sélectionnez l’application de fonction créée précédemment.

1. Une fois le déploiement terminé (au bout de quelques minutes), la fenêtre **Sortie** affiche les points de terminaison publics sur lesquels vous pouvez répéter vos tests.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Ajouter une liaison pour écrire des messages dans le stockage Azure

Une _liaison_ vous permet de connecter le code de votre fonction à des ressources, telles que le stockage Azure, sans avoir à écrire de code d’accès aux données. Une liaison est définie dans le fichier *function.json* et peut représenter à la fois l’entrée et la sortie. Une fonction peut utiliser plusieurs liaisons d’entrée et de sortie, mais elle ne peut utiliser qu’un seul déclencheur. Pour en savoir plus, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

Dans cette section, vous allez ajouter une liaison de stockage à la fonction HttpExample créée précédemment dans ce tutoriel. La fonction utilise cette liaison pour écrire des messages dans le stockage à chaque requête. Le stockage en question utilise le même compte de stockage par défaut que celui utilisé par l’application de fonction. Toutefois, si vous prévoyez de faire une utilisation intensive du stockage, vous pouvez créer un compte distinct.

1. Synchronisez les paramètres distants de votre projet Azure Functions dans votre fichier *local.settings.json* en ouvrant la palette de commandes et en sélectionnant **Azure Functions : Download Remote Settings** (Télécharger les paramètres distants). Ouvrez *local.settings.json* pour vérifier qu’il contient une valeur pour `AzureWebJobsStorage`. Cette valeur est la chaîne de connexion du compte de stockage.

1. Dans le dossier `HttpExample`, cliquez avec le bouton droit sur *function.json*, puis sélectionnez **Ajouter une liaison** :

    ![Commande Ajouter une liaison dans l’Explorateur Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Dans les invites qui suivent dans Visual Studio Code, sélectionnez ou fournissez les valeurs suivantes :

    | Prompt | Valeur à fournir |
    | --- | --- |
    | Set binding direction (Définir le sens de la liaison) | out |
    | Select binding with direction out (Sélectionner une liaison vers l’extérieur) | Stockage File d’attente Azure |
    | Nom utilisé pour identifier cette liaison dans votre code | msg |
    | File d’attente à laquelle le message sera envoyé | outqueue |
    | Sélectionner un paramètre dans *local.settings.json* (en demandant la connexion de stockage) | AzureWebJobsStorage |

1. Après avoir effectué ces sélections, vérifiez que la liaison suivante est ajoutée à votre fichier *function.json* :

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Maintenant que vous avez configuré la liaison, vous pouvez l’utiliser dans le code de votre fonction. Là encore, la liaison nouvellement définie s’affiche dans votre code comme un argument de la fonction `main` dans *\_\_init\_\_.py*. Par exemple, vous pouvez modifier le fichier *\_\_init\_\_.py* dans HttpExample pour qu’il corresponde au code suivant, où l’argument `msg` est utilisé pour écrire un message horodaté avec le nom utilisé dans la requête. Les commentaires expliquent les modifications apportées :

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Pour tester ces modifications localement, redémarrez le débogueur dans Visual Studio Code en appuyant sur F5 ou en sélectionnant la commande de menu **Déboguer** > **Démarrer le débogage**. Comme précédemment, la fenêtre **Sortie** doit afficher les points de terminaison de votre projet.

1. Dans un navigateur, accédez à l’URL `http://localhost:7071/api/HttpExample?name=VS%20Code` pour créer une requête vers le point de terminaison HttpExample, qui doit également écrire un message dans la file d’attente.

1. Pour vérifier que le message a été écrit dans la file d’attente « outqueue » (telle que nommée dans la liaison), vous pouvez utiliser l’une des trois méthodes suivantes :

    1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez au groupe de ressources qui contient votre projet Functions. Dans ce groupe de ressources, accédez au compte de stockage du projet, puis accédez à **Files d’attente**. Dans cette page, accédez à « outqueue », qui doit afficher tous les messages journalisés.

    1. Parcourez la file d’attente avec l’Explorateur Stockage Azure, qui s’intègre à Visual Studio, comme décrit dans [Connecter des fonctions à Stockage Azure avec Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md) et plus particulièrement, dans la section [Analyser la file d’attente de sortie](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue).

    1. Utilisez Azure CLI pour interroger la file d’attente de stockage, comme décrit dans [Interroger la file d’attente de stockage](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. Pour effectuer des tests dans le cloud, redéployez le code à l’aide de l’option **Deploy to Function App** (Déployer vers l’application de fonction) dans l’explorateur **Azure : Functions**. Si vous y êtes invité, sélectionnez l’application de fonction créée précédemment. Une fois le déploiement terminé (au bout de quelques minutes), la fenêtre **Sortie** affiche encore une fois les points de terminaison publics sur lesquels vous pouvez répéter vos tests.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Supprimer des ressources

L’application de fonction que vous avez créée comprend des ressources qui peuvent entraîner des coûts minimaux (voir [Tarifs Functions](https://azure.microsoft.com/pricing/details/functions/)). Pour nettoyer les ressources, cliquez avec le bouton droit sur l’application de fonction dans l’explorateur **Azure : Functions**, puis sélectionnez **Supprimer la fonction**. Vous pouvez également accéder au [portail Azure](https://portal.azure.com), sélectionner **Groupes de ressources** dans le volet de navigation de gauche, sélectionner le groupe de ressources qui a été créé dans ce tutoriel, puis utiliser la commande **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé le déploiement du code Python dans Azure Functions ! Vous êtes maintenant prêt à créer d’autres fonctions serverless.

Comme indiqué précédemment, si vous souhaitez en savoir plus sur l’extension Functions, consultez le dépôt GitHub correspondant, c’est-à-dire, [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). N’hésitez pas à nous faire part des problèmes que vous avez rencontrés ainsi que de vos suggestions.

Pour connaître les différents déclencheurs que vous pouvez utiliser, lisez [Vue d’ensemble d’Azure Functions](functions-overview.md).

Pour plus d’informations sur les services Azure que vous pouvez utiliser dans Python, notamment le stockage de données avec les services d’intelligence artificielle et de Machine Learning, consultez le [Centre de développement Azure pour Python](/azure/python/?view=azure-python).

Il existe également d’autres extensions Azure pour Visual Studio Code. Il vous suffit de lancer une recherche sur le mot clé « Azure » dans l’Explorateur d’extensions :

![Extensions Azure pour Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Voici quelques extensions populaires :

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Outils Azure CLI](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
