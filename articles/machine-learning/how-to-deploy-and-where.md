---
title: Comment et où déployer des modèles ?
titleSuffix: Azure Machine Learning
description: Découvrez comment et où déployer vos modèles Azure Machine Learning service, notamment Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge et FPGA (Field Programmable Gate Arrays).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.custom: seoapril2019
ms.openlocfilehash: bbb0992eaeef7892e5940130131ac139a339b47d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083240"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Déployer des modèles avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment déployer votre modèle Machine Learning en tant que service web dans le cloud Azure ou sur des appareils Azure IoT Edge.

Le workflow est similaire quel que soit l’endroit [où vous déployez](#target) votre modèle :

1. Inscrire le modèle.
1. Préparer le déploiement (spécifier les ressources, l’utilisation et la cible de calcul).
1. Déployer le modèle sur la cible de calcul.
1. Tester le modèle déployé, également appelé service web.

Pour plus d’informations sur les concepts impliqués dans le workflow de déploiement, consultez [Déployer, gérer et superviser des modèles avec Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

- Un modèle Si vous n’avez pas de modèle entraîné, vous pouvez utiliser les fichiers de modèle et de dépendance fournis dans [ce tutoriel](https://aka.ms/azml-deploy-cloud).

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’[extension Azure Machine Learning pour Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Se connecter à un espace de travail

Le code suivant montre comment se connecter à un espace de travail Azure Machine Learning à l’aide des informations mises en cache dans l’environnement de développement local :

+ **Avec le kit SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Pour plus d’informations sur l’utilisation du kit SDK pour se connecter à un espace de travail, consultez la documentation sur le [kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

+ **Avec l’interface CLI**

   Quand vous utilisez l’interface CLI, utilisez le paramètre `-w` ou `--workspace-name` pour spécifier l’espace de travail de la commande.

+ **Avec VS Code**

   Quand vous utilisez VS Code, vous sélectionnez l’espace de travail dans une interface graphique. Pour plus d’informations, consultez [Déployer et gérer des modèles](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) dans la documentation relative à l’extension VS Code.

## <a id="registermodel"></a> Inscrire votre modèle

Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous disposez d’un modèle stocké dans plusieurs fichiers, vous pouvez enregistrer ces derniers en tant que modèle unique dans l’espace de travail. Après l’inscription des fichiers, vous pouvez télécharger ou déployer le modèle inscrit et recevoir tous les fichiers que vous avez inscrits.

> [!TIP]
> Quand vous inscrivez un modèle, vous fournissez le chemin d’un emplacement cloud (à partir d’une exécution d’entraînement) ou d’un répertoire local. Ce chemin sert uniquement à localiser les fichiers à charger dans le cadre du processus d’inscription. Il ne doit pas forcément correspondre au chemin utilisé dans le script d’entrée. Pour plus d’informations, consultez [Localiser les fichiers de modèles dans votre script d’entrée](#locate-model-files-in-your-entry-script).

Les modèles Machine Learning sont inscrits dans votre espace de travail Azure Machine Learning. Le modèle peut provenir d’Azure Machine Learning ou d’un autre emplacement. Les exemples suivants montrent comment inscrire un modèle.

### <a name="register-a-model-from-an-experiment-run"></a>Inscrire un modèle à partir d’une exécution d’expérience

Les extraits de code de cette section montrent comment inscrire un modèle à partir d’une exécution d’entraînement :

> [!IMPORTANT]
> Pour utiliser ces extraits, vous devez déjà avoir effectué une exécution d’entraînement et vous devez avoir accès à l’objet `Run` (exemple du kit SDK) ou à la valeur de l’ID d’exécution (exemple de l’interface CLI). Pour plus d’informations sur l’entraînement de modèles, consultez [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

+ **Avec le kit SDK**

  Quand vous utilisez le kit SDK pour entraîner un modèle, vous pouvez recevoir un objet [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) en fonction de la façon dont vous avez entraîné le modèle. Chaque objet peut être utilisé pour inscrire un modèle créé par un exécution d’expérimentation.

  + Inscrire un modèle à partir d’un objet `azureml.core.Run` :
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Le paramètre `model_path` fait référence à l’emplacement cloud du modèle. Dans cet exemple, le chemin d’un fichier unique est utilisé. Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` avec le chemin d’un dossier contenant les fichiers. Pour plus d’informations, consultez la documentation sur [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-).

  + Inscrire un modèle à partir d’un objet `azureml.train.automl.run.AutoMLRun` :

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    Dans cet exemple, les paramètres `metric` et `iteration` n’étant pas spécifiés, l’itération avec la meilleure métrique principale est inscrite. La valeur `model_id` retournée par l’exécution est utilisée à la place d’un nom de modèle.

    Pour plus d’informations, consultez la documentation sur [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-).

+ **Avec l’interface CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  Le paramètre `--asset-path` fait référence à l’emplacement cloud du modèle. Dans cet exemple, le chemin d’un fichier unique est utilisé. Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `--asset-path` avec le chemin d’un dossier contenant les fichiers.

+ **Avec VS Code**

  Utilisez l’extension [VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) pour inscrire des modèles à l’aide de fichiers ou de dossiers de modèles.

### <a name="register-a-model-from-a-local-file"></a>Inscrire un modèle à partir d’un fichier local

Vous pouvez inscrire un modèle en indiquant son chemin local. Vous pouvez fournir le chemin d’un dossier ou d’un seul fichier. Vous pouvez utiliser cette méthode pour inscrire les modèles qui ont été entraînés avec Azure Machine Learning et téléchargés. Vous pouvez également utiliser cette méthode pour inscrire des modèles entraînés en dehors d’Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Utilisation du kit SDK et d’ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` avec le chemin d’un dossier contenant les fichiers.

+ **Avec l’interface CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `-p` avec le chemin d’un dossier contenant les fichiers.

**Durée estimée** : Environ 10 secondes.

Pour plus d’informations, consultez la documentation sur la [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Pour plus d’informations sur l’utilisation de modèles formés en dehors d’Azure Machine Learning, consultez [Déployer un modèle existant](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Choisir une cible de calcul

Vous pouvez utiliser les cibles de calcul suivantes, ou ressources de calcul, pour héberger le déploiement de votre service web :

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="single-versus-multi-model-endpoints"></a>Points de terminaison à un seul modèle ou à plusieurs modèles
Azure ML prend en charge le déploiement d’un ou de plusieurs modèles derrière un point de terminaison unique.

Les points de terminaison à plusieurs modèles utilisent un conteneur partagé pour héberger plusieurs modèles. Les coûts de traitement s’en retrouvent réduits, l’utilisation est améliorée et vous pouvez chaîner des modules dans des ensembles. Les modèles que vous spécifiez dans votre script de déploiement sont montés et mis à disposition sur le disque du conteneur de service. Vous pouvez les charger dans la mémoire à la demande et noter en fonction du modèle spécifique demandé au moment de la notation.

Pour obtenir un exemple E2E qui montre comment utiliser plusieurs modèles derrière un seul point de terminaison conteneurisé, consultez [cet exemple](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model).

## <a name="prepare-deployment-artifacts"></a>Préparer des artefacts de déploiement

Pour déployer le modèle, vous avez besoin des éléments suivants :

* **Dépendances de script d’entrée et de code source**. Ce script accepte les requêtes, calcule le score de la requête à l’aide du modèle et retourne les résultats.

    > [!IMPORTANT]
    > * Le script d’entrée est spécifique de votre modèle. Il doit comprendre le format des données de la requête entrante, le format des données attendues par votre modèle et le format des données retournées aux clients.
    >
    >   Si le format des données de la requête n’est pas utilisable par votre modèle, le script peut les convertir dans un format acceptable. Il peut également transformer la réponse avant de la retourner au client.
    >
    > * Les déploiements de services web et d’IoT Edge ne sont pas en mesure d’accéder aux magasins de données ou jeux de données d’espace de travail. Si votre service déployé doit accéder à des données stockées à l’extérieur du déploiement, par exemple dans un compte de stockage Azure, vous devez développer une solution de code personnalisée à l’aide du Kit de développement logiciel (SDK) approprié. Exemple : [Kit de développement logiciel (SDK) Stockage Azure pour Python](https://github.com/Azure/azure-storage-python).
    >
    >   Une autre solution possible pour votre scénario consiste à utiliser les [prédictions par lots](how-to-use-parallel-run-step.md), qui donnent accès aux magasins de travail durant le scoring.

* **Environnement d’inférence**. Image de base avec les dépendances de package installées requises pour exécuter le modèle.

* **Configuration de déploiement** de la cible de calcul qui héberge le modèle déployé. Cette configuration décrit notamment les besoins en mémoire et en ressources CPU pour exécuter le modèle.

Ces éléments sont encapsulés dans une *configuration d’inférence* et une *configuration de déploiement*. La configuration d’inférence référence le script d’entrée et d’autres dépendances. Vous définissez ces configurations par programmation quand vous utilisez le kit SDK pour effectuer le déploiement. Vous les définissez dans des fichiers JSON quand vous utilisez l’interface CLI.

### <a id="script"></a> 1. Définir votre script d’entrée et les dépendances

Le script d’entrée reçoit les données envoyées à un service web déployé, puis les passe au modèle. Ensuite, il prend la réponse retournée par le modèle et la retourne au client. *Le script est propre à votre modèle*. Il doit comprendre les données que le modèle attend et retourne.

Le script contient deux fonctions qui chargent et exécutent le modèle :

* `init()`: En général, cette fonction charge le modèle dans un objet global. Cette fonction est exécutée une seule fois quand le conteneur Docker de votre service web est démarré.

* `run(input_data)`: cette fonction utilise le modèle pour prédire une valeur basée sur les données d'entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation. Vous pouvez également utiliser des données binaires brutes. Vous pouvez transformer les données avant de les envoyer au modèle ou avant de les retourner au client.

#### <a name="locate-model-files-in-your-entry-script"></a>Localiser les fichiers de modèles dans votre script d’entrée

Il existe deux façons de localiser les modèles dans votre script d’entrée :
* `AZUREML_MODEL_DIR`: variable d’environnement contenant le chemin de l’emplacement du modèle.
* `Model.get_model_path`: API qui retourne le chemin du fichier du modèle d’après le nom de modèle inscrit.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR est une variable d’environnement créée au moment du déploiement du service. Vous pouvez utiliser cette variable d’environnement pour rechercher l’emplacement de chaque modèle déployé.

Le tableau suivant indique la valeur de la variable AZUREML_MODEL_DIR en fonction du nombre de modèles déployés :

| Déploiement | Valeur de la variable d’environnement |
| ----- | ----- |
| Modèle unique | Chemin du dossier contenant le modèle. |
| Modèles multiples | Chemin du dossier contenant tous les modèles. Les modèles sont localisés par nom et version dans ce dossier (`$MODEL_NAME/$VERSION`) |

Pendant l'inscription et le déploiement des modèles, ceux-ci sont placés à l'emplacement AZUREML_MODEL_DIR et ils conservent leur nom de fichier d'origine.

Pour obtenir le chemin d'accès à un fichier modèle dans votre script d'entrée, combinez la variable d'environnement avec le chemin du fichier que vous recherchez.

**Exemple avec un modèle unique**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exemple avec des modèles multiples**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Quand vous inscrivez un modèle, vous fournissez un nom de modèle qui est utilisé pour gérer le modèle dans le Registre. Vous utilisez ce nom avec la méthode [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pour récupérer le chemin du ou des fichiers de modèle sur le système de fichiers local. Si vous inscrivez un dossier ou une collection de fichiers, cette API retourne le chemin du répertoire contenant ces fichiers.

Quand vous inscrivez un modèle, vous lui donnez un nom. Le nom correspond à l’endroit où le modèle est placé, localement ou durant le déploiement du service.

> [!IMPORTANT]
> Si vous avez utilisé le Machine Learning automatisé pour entraîner un modèle, une valeur `model_id` est utilisée comme nom de modèle. Pour obtenir un exemple d’inscription et de déploiement d’un modèle entraîné avec le Machine Learning automatisé, consultez [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) sur GitHub.

L’exemple suivant retourne le chemin d’un seul fichier appelé `sklearn_mnist_model.pkl` (inscrit sous le nom `sklearn_mnist`) :

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>(Facultatif) Génération automatique d'un schéma

Si vous voulez générer automatiquement un schéma pour votre service web, spécifiez un exemple d’entrée et/ou de sortie dans le constructeur pour l’un des objets de type définis. Le type et l’exemple sont utilisés pour créer automatiquement le schéma. Azure Machine Learning crée ensuite une spécification (Swagger) [OpenAPI](https://swagger.io/docs/specification/about/) pour le service web pendant le déploiement.

Les types suivants sont pris en charge :

* `pandas`
* `numpy`
* `pyspark`
* Objet Python standard

Pour utiliser la génération de schéma, incluez le package `inference-schema` dans votre fichier d’environnement Conda. Pour plus d’informations sur ce package, consultez [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Exemple de fichier de dépendances

L’extrait YAML suivant est un exemple de fichier de dépendances Conda pour l’inférence. Notez que vous devez spécifier azureml-defaults avec la version 1.0.45 ou une version supérieure en tant que dépendance pip, car elle contient les fonctionnalités nécessaires pour héberger le modèle comme un service web.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Si votre dépendance est disponible via Conda et PIP (à partir de PyPi), Microsoft recommande d’utiliser la version Conda, car les packages Conda sont généralement fournis avec des fichiers binaires prédéfinis qui rendent l’installation plus fiable.
>
> Pour plus d’informations, consultez [Compréhension de Conda et PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Pour vérifier si votre dépendance est disponible via Conda, utilisez la commande `conda search <package-name>` ou utilisez les index de package sur [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) et [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Si vous souhaitez utiliser la génération de schéma automatique, votre script d’entrée doit importer les packages `inference-schema`.

Définissez les exemples de formats d’entrée et de sortie dans les variables `input_sample` et `output_sample`, qui représentent les formats de requête et de réponse pour le service web. Utilisez ces exemples dans les éléments décoratifs des fonctions d’entrée et de sortie sur la fonction `run()`. L’exemple scikit-learn suivant utilise la génération de schéma.

##### <a name="example-entry-script"></a>Exemple de script d’entrée

L’exemple suivant montre comment accepter et retourner des données JSON :

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

L’exemple suivant montre comment définir les données d’entrée en tant que dictionnaire `<key: value>` à l’aide d’un DataFrame. Cette méthode est prise en charge pour l’utilisation du service web déployé à partir de Power BI. ([Découvrez-en plus sur l’utilisation du service web à partir de Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Pour obtenir d’autres exemples, consultez les scripts suivants :

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Données binaires

Si votre modèle accepte les données binaires, comme une image, vous devez modifier le fichier `score.py` utilisé pour votre déploiement afin d’accepter les demandes HTTP brutes. Pour accepter des données brutes, utilisez la classe `AMLRequest` dans votre script d’entrée et ajoutez l’ élément décoratif `@rawhttp` à la fonction `run()`.

Voici l’exemple d’un `score.py` qui accepte des données binaires :

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La classe `AMLRequest` se trouve dans l’espace de noms `azureml.contrib`. Les entités dans cet espace de noms changent fréquemment car nous cherchons à améliorer le service. Tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer un test dans votre environnement de développement local, vous pouvez installer les composants à l’aide de la commande suivante :
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)

Le partage des ressources Cross-Origin permet de demander des ressources dans une page web à partir d’un autre domaine. CORS fonctionne par le biais d’en-têtes HTTP envoyés avec la demande du client et retournés avec la réponse du service. Pour plus d’informations sur CORS et les en-têtes valides, consultez [Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) sur Wikipédia.

Pour configurer que votre modèle de déploiement prend en charge le partage des ressources Cross-Origin, utilisez la classe `AMLResponse` dans votre script d’entrée. Cette classe vous permet de définir les en-têtes de l’objet de réponse.

L’exemple suivant définit l’en-tête `Access-Control-Allow-Origin` de la réponse à partir du script d’entrée :

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La classe `AMLResponse` se trouve dans l’espace de noms `azureml.contrib`. Les entités dans cet espace de noms changent fréquemment car nous cherchons à améliorer le service. Tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer un test dans votre environnement de développement local, vous pouvez installer les composants à l’aide de la commande suivante :
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inference-environment"></a>2. Définir votre environnement d’inférence

La configuration de l’inférence décrit comment configurer le modèle pour les prédictions. Cette configuration ne fait pas partie de votre script d’entrée. Elle référence votre script d’entrée et sert à localiser toutes les ressources exigées par le déploiement. Elle est utilisée ultérieurement, quand vous déployez le modèle.

La configuration de l’inférence utilise des environnements Azure Machine Learning pour définir les dépendances logicielles nécessaires pour votre déploiement. Les environnements vous permettent de créer, de gérer et de réutiliser les dépendances logicielles requises pour l’apprentissage et le déploiement. L’exemple suivant présente le chargement d’un environnement à partir de votre espace de travail, puis son utilisation avec la configuration de l’inférence :

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig

myenv = Environment.get(workspace=ws, name="myenv", version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=myenv)
```

Pour plus d’informations sur les environnements , consultez [Créer et gérer des environnements pour la formation et le déploiement](how-to-use-environments.md).

Vous pouvez également spécifier directement les dépendances sans utiliser d’environnement. L’exemple suivant montre comment créer une configuration d’inférence qui charge des dépendances logicielles à partir d’un fichier Conda :

Pour plus d’informations sur les environnements , consultez [Créer et gérer des environnements pour la formation et le déploiement](how-to-use-environments.md).

Pour plus d’informations sur la configuration des inférences, consultez la documentation sur la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

Pour plus d’informations sur l’utilisation d’une image Docker personnalisée avec une configuration d’inférence, consultez le [guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Exemple CLI InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

La commande suivante montre comment déployer un modèle à l’aide de l’interface CLI :

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Dans cet exemple, la configuration spécifie les paramètres suivants :

* Le fait que le modèle nécessite Python.
* Le [script d’entrée](#script) qui est utilisé pour gérer les requêtes web envoyées au service déployé.
* Le fichier Conda qui décrit les packages Python nécessaires à l’inférence.

Pour plus d’informations sur l’utilisation d’une image Docker personnalisée avec une configuration d’inférence, consultez le [guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Définir la configuration de votre déploiement

Avant de déployer votre modèle, vous devez définir la configuration de déploiement. *La configuration dr déploiement est propre à la cible de calcul qui va héberger le service web.* . Par exemple, quand vous déployez un modèle localement, vous devez spécifier le port sur lequel le service accepte les requêtes. La configuration de déploiement ne fait pas partie de votre script d’entrée. Elle est utilisée pour définir les caractéristiques de la cible de calcul qui hébergera le modèle et le script d’entrée.

Vous devrez peut-être également créer la ressource de calcul si, par exemple, aucune instance AKS (Azure Kubernetes Service) n’est encore associée à votre espace de travail.

Le tableau suivant donne un exemple de configuration de déploiement créée pour chaque cible de calcul :

| Cible de calcul | Exemple de configuration de déploiement |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Les classes des services web Local, Azure Container Instances et AKS peuvent être importées à partir de `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

## <a name="deploy-to-target"></a>Déployer sur la cible

Le déploiement utilise la configuration de déploiement de configuration de l’inférence pour déployer les modèles. Le processus de déploiement est similaire, quelle que soit la cible de calcul. Le déploiement sur AKS est légèrement différent, car vous devez fournir une référence au cluster AKS.

### <a name="securing-deployments-with-ssl"></a>Sécurisation des déploiements avec SSL

Pour plus d’informations sur comment sécuriser un déploiement de service web, consultez [Utiliser SSL pour sécuriser un service web](how-to-secure-web-service.md#enable).

### <a id="local"></a> Déploiement local

Pour déployer un modèle localement, Docker doit être installé sur votre ordinateur local.

#### <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Pour plus d’informations, consultez la documentation sur [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Utilisation de l’interface CLI

Pour déployer un modèle à l’aide de l’interface CLI, utilisez la commande suivante. Remplacez `mymodel:1` par le nom et la version du modèle inscrit :

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Pour plus d’informations, consultez la documentation sur [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

### <a id="notebookvm"></a>Service web d’instances de calcul (développement/test)

Consultez [Déployer un modèle sur une instance de calcul Azure Machine Learning](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a> Azure Container Instances (dev/test)

Consultez [Procéder à un déploiement sur Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes Service (dev/test et production)

Consultez [Procéder à un déploiement sur Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Test A/B (lancement contrôlé)
Pour plus d’informations, consultez [Lancement contrôlé des modèles ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

## <a name="consume-web-services"></a>Utiliser des services web

Chaque service web déployé fournit un point de terminaison REST, qui vous permet de créer des applications clientes dans tous les langages de programmation.
Si vous avez activé l’authentification basée sur les clés pour votre service, vous devez fournir une clé de service comme jeton dans l’en-tête de la requête.
Si vous avez activé l’authentification basée sur les jetons pour votre service, vous devez fournir un jeton JWT (JSON Web Token) Azure Machine Learning comme jeton du porteur dans l’en-tête de la requête. 

La principale différence est que **les clés sont statiques et peuvent être regénérées manuellement** et que **les jetons doivent être actualisés à l’expiration**. L’authentification basée sur les clés est prise en charge pour les services web déployés avec Azure Container Instance et Azure Kubernetes Service, alors que l’authentification basée sur les jetons est disponible **uniquement** pour les déploiements avec Azure Kubernetes Service. Pour plus d’informations et obtenir des exemples de code spécifiques, consultez le [guide pratique](how-to-setup-authentication.md#web-service-authentication) sur l’authentification.

> [!TIP]
> Après avoir déployé le service, vous pouvez récupérer le document JSON du schéma. Utilisez la [propriété swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) du service web déployé, par exemple `service.swagger_uri`, pour obtenir l’URI du fichier Swagger du service web local.

### <a name="request-response-consumption"></a>Consommation de requête-réponse

Voici un exemple montrant comment appeler votre service dans Python :
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Pour plus d’informations, consultez [Créer des applications clientes pour utiliser des services web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schéma de service web (spécification OpenAPI)

Si vous avez utilisé la génération automatique de schéma dans le cadre de votre déploiement, vous pouvez obtenir l’adresse de la spécification OpenAPI du service à l’aide de la [propriété swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Par exemple, `print(service.swagger_uri)`.) Utilisez une requête GET ou ouvrez l’URI dans un navigateur pour récupérer la spécification.

Le document JSON suivant est un exemple de schéma (spécification OpenAPI) généré pour un déploiement :

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Pour plus d’informations, consultez la [spécification OpenAPI](https://swagger.io/specification/).

Pour disposer d'un utilitaire permettant de créer des bibliothèques clientes à partir de la spécification, consultez [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a> Inférence par lots
Les cibles de calcul Azure Machine Learning sont créées et managées par Azure Machine Learning. Elles peuvent être utilisées pour la prédiction par lots à partir de pipelines Azure Machine Learning.

Pour obtenir une présentation détaillée de l’inférence par lots avec la capacité de calcul Azure Machine Learning, consultez le [guide pratique pour exécuter des prédictions par lots](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a> Inférence IoT Edge
La prise en charge du déploiement en périphérie est en préversion. Pour plus d’informations, consultez [Déployer Azure Machine Learning en tant que module IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a> Mettre à jour les services web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Déployer des modèles en continu

Vous pouvez déployer des modèles en continu à l’aide de l’extension Machine Learning pour [Azure DevOps](https://azure.microsoft.com/services/devops/). Vous pouvez utiliser l’extension Machine Learning pour Azure DevOps pour déclencher un pipeline de déploiement quand un nouveau modèle Machine Learning est inscrit dans un espace de travail Azure Machine Learning.

1. Inscrivez-vous sur [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), ce qui permet l’intégration et la livraison continues de votre application vers n’importe quelle plateforme ou n’importe quel cloud. (Azure Pipelines et les [pipelines Machine Learning](concept-ml-pipelines.md#compare) sont deux choses différentes.)

1. [Créez un projet Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installez l’[extension Machine Learning pour Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Utilisez les connexions au service pour configurer une connexion de principal de service à votre espace de travail Azure Machine Learning afin d’accéder à vos artefacts. Accédez aux paramètres du projet, puis sélectionnez **Connexions au service** et **Azure Resource Manager** :

    [![Sélectionner Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Dans la liste **Niveau de portée**, sélectionnez **AzureMLWorkspace**, puis entrez le reste des valeurs :

    ![Sélectionner AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Pour déployer en continu votre modèle Machine Learning à l’aide d’Azure Pipelines, sélectionnez **Mise en production** sous Pipelines. Ajoutez un nouvel artefact, puis sélectionnez l’artefact **Modèle AzureML** et la connexion au service que vous avez créée précédemment. Sélectionnez le modèle et la version pour déclencher un déploiement :

    [![Sélectionner le modèle AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Activez le déclencheur de modèle sur votre artefact de modèle. Quand vous activez le déclencheur, chaque fois que la version spécifiée de ce modèle (autrement dit, la version la plus récente) est inscrite dans votre espace de travail, un pipeline de mise en production Azure DevOps est déclenché.

    [![Activer le déclencheur de modèle](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Pour obtenir d’autres exemples de projets et des exemples, consultez les dépôts d’exemples suivants dans GitHub :

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Télécharger un modèle
Si vous souhaitez télécharger votre modèle pour l’utiliser dans votre propre environnement d’exécution, vous pouvez le faire avec les commandes du kit de développement logiciel (SDK)/de l’interface CLI suivantes :

SDK :
```python
model_path = Model(ws,'mymodel').download()
```

Interface CLI :
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Préversion) Modèle de déploiement sans code

Le modèle de déploiement sans code est actuellement en version préliminaire et prend en charge les infrastructures d’apprentissage automatique suivants :

### <a name="tensorflow-savedmodel-format"></a>Format Tensorflow SavedModel
Les modèles Tensorflow doivent être inscrits au **format SavedModel** pour fonctionner avec un modèle de déploiement sans code.

Pour plus d’informations sur la création d’un SavedModel, consultez [ce lien](https://www.tensorflow.org/guide/saved_model).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modèles ONNX

L’inscription et le déploiement du modèle ONNX sont pris en charge pour tout graphique d’inférence ONNX. Les étapes de prétraitement et de post-traitement ne sont pas prises en charge actuellement.

Voici un exemple d’inscription et de déploiement d’un modèle MNIST ONNX :

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Modèles Scikit-learn

Aucun modèle de déploiement de code n’est pris en charge pour tous les types de modèles Scikit-learn intégrés.

Voici un exemple d’inscription et de déploiement d’un modèle sklearn sans code supplémentaire :

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

REMARQUE :  Ces dépendances sont incluses dans le conteneur d’inférence sklearn prédéfini :

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modèles de package

Dans certains cas, vous pouvez être amené à créer une image Docker sans déployer le modèle (par exemple, si vous envisagez un [déploiement sur Azure App Service](how-to-deploy-app-service.md)). Vous pouvez également télécharger l’image et l’exécuter sur une installation locale de Docker. Vous pouvez même télécharger les fichiers utilisés pour générer l’image, les inspecter, les modifier, puis générer l’image manuellement.

L’empaquetage de modèle vous permet d’effectuer ces deux opérations. Il empaquette toutes les ressources nécessaires pour héberger un modèle en tant que service web, et vous permet de télécharger une image Docker entièrement générée ou les fichiers nécessaires pour en générer une. Vous pouvez utiliser l’empaquetage de modèle de deux façons :

**Télécharger un modèle empaqueté :** téléchargez une image Docker contenant le modèle et les autres fichiers nécessaires pour l’héberger en tant que service web.

**Générer un fichier Docker :** téléchargez le fichier Docker, le modèle, le script d’entrée et les autres ressources nécessaires pour générer une image Docker. Vous pouvez ensuite inspecter les fichiers ou apporter des modifications avant de générer l’image localement.

Les deux packages permettent d’obtenir une image Docker locale.

> [!TIP]
> La création d’un package est similaire au déploiement d’un modèle. Vous utilisez un modèle inscrit et une configuration d’inférence.

> [!IMPORTANT]
> Pour télécharger une image entièrement générée ou générer une image localement, [Docker](https://www.docker.com) doit être installé dans votre environnement de développement.

### <a name="download-a-packaged-model"></a>Télécharger un modèle empaqueté

L’exemple suivant génère une image qui est inscrite dans le registre de conteneurs Azure pour votre espace de travail :

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Après avoir créé un package, vous pouvez utiliser `package.pull()` pour extraire l’image dans votre environnement Docker local. La sortie de cette commande affiche le nom de l’image. Par exemple : 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Une fois le modèle téléchargé, utilisez la commande `docker images` pour lister les images locales :

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Pour démarrer un conteneur local basé sur cette image, utilisez la commande suivante afin de démarrer un conteneur nommé à partir de l’interpréteur de commandes ou de la ligne de commande. Remplacez la valeur `<imageid>` par l’ID d’image retourné par la commande `docker images`.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Cette commande démarre la version la plus récente de l’image nommée `myimage`. Elle mappe le port local 6789 au port du conteneur sur lequel le service web écoute (5001). Elle affecte également le nom `mycontainer` au conteneur, ce qui en facilite l’arrêt. Une fois le conteneur démarré, vous pouvez envoyer des demandes à `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Générer un fichier Docker et des dépendances

L’exemple suivant montre comment télécharger le fichier Docker, le modèle et les autres ressources nécessaires pour générer une image localement. Le paramètre `generate_dockerfile=True` indique que vous souhaitez les fichiers, et non une image entièrement générée.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ce code télécharge les fichiers nécessaires à la création de l’image dans le répertoire `imagefiles`. Le fichier Docker inclus dans les fichiers enregistrés référence une image de base stockée dans un registre de conteneurs Azure. Quand vous générez l’image sur votre installation Docker locale, vous devez utiliser l’adresse, le nom d’utilisateur et le mot de passe pour vous authentifier auprès du registre. Pour générer l’image à l’aide d’une installation Docker locale, effectuez les étapes suivantes :

1. À partir d’un interpréteur de commandes ou d’une session de ligne de commande, utilisez la commande suivante pour authentifier Docker auprès du registre de conteneurs Azure. Remplacez `<address>`, `<username>` et `<password>` par les valeurs récupérées par `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Pour créer l’image, utilisez la commande suivante. Remplacez `<imagefiles>` par le chemin du répertoire dans lequel la commande `package.save()` a enregistré les fichiers.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Cette commande définit le nom de l’ image sur `myimage`.

Pour vérifier que l’image est générée, utilisez la commande `docker images`. L’image `myimage` doit figurer dans la liste :

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Pour démarrer un conteneur basé sur cette image, utilisez la commande suivante :

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Cette commande démarre la version la plus récente de l’image nommée `myimage`. Elle mappe le port local 6789 au port du conteneur sur lequel le service web écoute (5001). Elle affecte également le nom `mycontainer` au conteneur, ce qui en facilite l’arrêt. Une fois le conteneur démarré, vous pouvez envoyer des demandes à `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Exemple de client pour tester le conteneur local

Le code suivant est un exemple de client Python utilisable avec le conteneur :

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Pour obtenir des exemples de clients dans d’autres langages de programmation, consultez [Utiliser des modèles déployés en tant que services web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Arrêter le conteneur Docker

Pour arrêter le conteneur, utilisez la commande suivante à partir d’un autre interpréteur de commandes ou d’une autre ligne de commande :

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer un service web déployé, utilisez `service.delete()`.
Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation sur [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) et [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle Azure Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)

