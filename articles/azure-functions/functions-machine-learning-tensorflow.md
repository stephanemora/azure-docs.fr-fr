---
title: Utiliser Python et TensorFlow dans Azure Functions pour produire des inférences machine learning
description: Ce tutoriel montre comment appliquer des modèles Machine Learning TensorFlow dans Azure Functions
author: anthonychu
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e7c4e1bbd23d43d5e11ab8b26c3d4e1215b4946b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230502"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Didacticiel : Appliquer des modèles Machine Learning dans Azure Functions avec Python et TensorFlow

Cet article explique comment Azure Functions permet d’utiliser Python et TensorFlow avec un modèle Machine Learning pour classifier une image en fonction de son contenu.

Ce didacticiel vous apprend à effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Initialiser un environnement local pour le développement d’Azure Functions dans Python
> * Importer un modèle Machine Learning TensorFlow personnalisé dans une application de fonction
> * Créer une API HTTP serverless pour prédire si une photo contient un chien ou un chat
> * Consommer l’API à partir d’une application web

![Capture d’écran du projet terminé](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis 

Pour créer Azure Functions dans Python, vous devez installer quelques outils.

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/).

## <a name="clone-the-tutorial-repository"></a>Cloner le dépôt du tutoriel

Pour commencer, ouvrez un terminal et clonez le dépôt suivant à l’aide de Git :

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

Le dépôt contient quelques dossiers.

- *start* :  il s’agit de votre dossier de travail pour ce tutoriel.
- *end* : il s’agit du résultat final et de l’implémentation complète de votre référence.
- *Ressources* : contient le modèle Machine Learning et les bibliothèques d’assistance.
- *frontend* : site web qui appelle l’application de fonction.

## <a name="create-and-activate-a-python-virtual-environment"></a>Créer et activer un environnement virtuel Python

Azure Functions nécessite Python 3.6. x. Vous allez créer un environnement virtuel pour veiller à utiliser la version de Python demandée.

Remplacez le répertoire de travail actuel par le dossier *start*. Ensuite, créez et activez un environnement virtuel nommé *.venv*. Selon votre installation Python, il est possible que les commandes permettant de créer un environnement virtuel Python 3.6 soient différentes des instructions suivantes.

#### <a name="linux-and-macos"></a>Linux et macOS :

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows :

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

L’invite de commandes du terminal est maintenant précédée de `(.venv)`, ce qui indique que vous avez correctement activé l’environnement virtuel. Vérifiez que `python` dans l’environnement virtuel est effectivement Python 3.6. x.

```console
python --version
```

> [!NOTE]
> Pour le reste du tutoriel, vous exécutez les commandes dans l’environnement virtuel. Si vous devez réactiver l’environnement virtuel dans un terminal, exécutez la commande d’activation appropriée pour votre système d’exploitation.

## <a name="create-an-azure-functions-project"></a>Créer un projet Azure Functions

Dans le dossier *start*, utilisez Azure Functions Core Tools pour initialiser une application de fonction Python.

```console
func init --worker-runtime python
```

Une application de fonction peut contenir une ou plusieurs instances Azure Functions. Ouvrez le dossier *start* dans un éditeur et examinez son contenu.

- [*local.settings.json*](functions-run-local.md#local-settings-file) : contient les paramètres d’application utilisés pour le développement local.
- [*host.json*](functions-host-json.md) : contient les paramètres des extensions et de l’hôte Azure Functions.
- [*requirements.txt*](functions-reference-python.md#package-management) : contient les packages Python exigés par cette application.

## <a name="create-an-http-function"></a>Créer une fonction HTTP

L’application a besoin d’un point de terminaison d’API HTTP unique, qui accepte une URL d’image comme entrée, et retourne une prédiction de la présence ou non d’un chien ou d’un chat dans l’image.

Dans le terminal, utilisez Azure Functions Core Tools pour générer automatiquement une nouvelle fonction HTTP nommée *classify*.

```console
func new --language python --template HttpTrigger --name classify
```

Un nouveau dossier nommé *classify* est créé, contenant deux fichiers.

- *\_\_init\_\_.py* : fichier pour la fonction principale.
- *function.json* :  fichier décrivant le déclencheur de la fonction et ses liaisons d’entrée et de sortie.

### <a name="run-the-function"></a>Exécuter la fonction

Dans le terminal où l’environnement virtuel Python est activé, démarrez l’application de fonction.

```console
func start
```

Ouvrez un navigateur et accédez à l'URL suivante. La fonction doit exécuter et retourner *Hello Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Utilisez `Ctrl-C` pour arrêter l’application de fonction.

## <a name="import-the-tensorflow-model"></a>Importer le modèle TensorFlow

Vous allez utiliser un modèle TensorFlow prégénéré, qui a été entraîné avec le service Azure Custom Vision avant d’être exporté par celui-ci.

> [!NOTE]
> Si vous souhaitez créer votre propre niveau gratuit du service Custom Vision, vous pouvez suivre les [instructions dans l’exemple de dépôt de projet](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

Le modèle se compose de deux fichiers présents dans le dossier *<REPOSITORY_ROOT>/resources/model* : *model.pb* et *labels.txt*. Copiez-les dans le dossier de la fonction *classify*.

#### <a name="linux-and-macos"></a>Linux et macOS :

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows :

```powershell
copy ..\resources\model\* classify
```

Veillez à inclure \* dans la commande ci-dessus. Vérifiez que *classify* contient maintenant les fichiers nommés *model.pb* et *labels.txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Ajouter les fonctions d’assistance et les dépendances

Certaines fonctions d’assistance, pour la préparation de l’image d’entrée et la création d’une prédiction à l’aide de TensorFlow, se trouvent dans un fichier nommé *predict.py*, dans le dossier *resources*. Copiez ce fichier dans le dossier de la fonction *classify*.

#### <a name="linux-and-macos"></a>Linux et macOS :

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows :

```powershell
copy ..\resources\predict.py classify
```

Vérifiez que *classify* contient à présent un fichier nommé *predict.py*.

### <a name="install-dependencies"></a>Installer des dépendances

La bibliothèque d’assistance comporte certaines dépendances qui doivent être installées. Ouvrez *start/requirements.txt* dans votre éditeur et ajoutez les dépendances suivantes au fichier.

```txt
tensorflow==1.14
Pillow
requests
```

Enregistrez le fichier .

Dans le terminal où l’environnement virtuel est activé, exécutez la commande suivante dans le dossier *start* pour installer les dépendances. Certaines étapes de l’installation peuvent prendre quelques minutes.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Mise en cache du modèle dans les variables globales

Dans l’éditeur, ouvrez *predict.py* et examinez la fonction `_initialize` au début du fichier. Notez que le modèle TensorFlow est chargé à partir du disque la première fois que la fonction est exécutée et enregistrée dans des variables globales. Le chargement à partir du disque est ignoré dans les exécutions ultérieures de la fonction `_initialize`. La mise en cache du modèle en mémoire avec cette technique accélère les prédictions ultérieures.

Pour plus d’informations sur les variables globales, reportez-vous au [Guide des développeurs Python sur Azure Functions](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Mettre à jour la fonction pour exécuter des prédictions

Ouvrez *classify/\_\_init\_\_.py* dans votre éditeur. Importez la bibliothèque *predict* que vous avez ajoutée précédemment dans le même dossier. Ajoutez les instructions `import` suivantes sous les autres importations déjà présentes dans le fichier.

```python
import json
from .predict import predict_image_from_url
```

Remplacez le code du modèle de fonction par le suivant.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

N’oubliez pas d’enregistrer vos modifications.

Cette fonction reçoit une URL d’image dans un paramètre de chaîne de requête nommé `img`. Elle appelle `predict_image_from_url` à partir de la bibliothèque d’assistance qui télécharge l’image et retourne une prédiction à l’aide du modèle TensorFlow. La fonction retourne ensuite une réponse HTTP avec les résultats.

Étant donné que le point de terminaison HTTP est appelé par une page web hébergée sur un autre domaine, la réponse HTTP comprend un en-tête `Access-Control-Allow-Origin` pour satisfaire aux exigences CORS (Cross-Origin Resource Sharing) du navigateur.

> [!NOTE]
> Dans une application de production, remplacez `*` par l’origine spécifique de la page web pour renforcer la sécurité.

### <a name="run-the-function-app"></a>Exécuter l’application de fonction

Vérifiez que l’environnement virtuel Python est toujours activé et démarrez l’application de fonction à l’aide de la commande suivante.

```console
func start
```

Dans un navigateur, ouvrez cette URL qui appelle votre fonction avec l’URL d’une photo de chat. Vérifiez qu’un résultat de prédiction valide est retourné.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Maintenez l’application de fonction en cours d’exécution.

### <a name="run-the-web-app"></a>Exécuter l’application web

Il existe une application web simple dans le dossier *frontend* qui utilise l’API HTTP dans l’application de fonction.

Ouvrez un terminal *separate* et accédez au dossier *frontend*. Démarrez un serveur HTTP avec Python 3.6.

#### <a name="linux-and-macos"></a>Linux et macOS :

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows :

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

Dans un navigateur, accédez à l’URL du serveur HTTP qui est affichée dans le terminal. Une application web doit s’afficher. Entrez l’une des URL de photo suivantes dans la zone de texte. Vous pouvez également utiliser l’URL d’une photo de chat ou de chien accessible publiquement.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Lorsque vous cliquez sur Envoyer, l’application de fonction est appelée et un résultat s’affiche dans la page.

## <a name="clean-up-resources"></a>Supprimer des ressources
Comme l’intégralité de ce tutoriel s’exécute localement sur votre machine, il n’y a pas de ressources ni de services Azure à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer et à personnaliser une API HTTP avec Azure Functions pour effectuer des prédictions à l’aide d’un modèle TensorFlow. Vous avez également appris à appeler l’API depuis une application web.

Vous pouvez utiliser les techniques de ce tutoriel pour élaborer des API plus complexes, qui s’exécutent sur le modèle de calcul serverless fourni par Azure Functions.

Pour déployer l’application de fonction sur Azure, utilisez [Azure Functions Core Tools](./functions-run-local.md#publish) ou [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Guide des développeurs Python sur Azure Functions](./functions-reference-python.md)
