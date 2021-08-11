---
title: 'Tutoriel : Créer le modèle prédictif avec un notebook (partie 1 sur 2)'
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et déployer un modèle Machine Learning à l’aide du code d’un notebook Jupyter. Créez également un script de scoring qui définit l’entrée et la sortie pour une intégration facile dans Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 409936f98dab0fa975c1314a84096b7b46df7613
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459403"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Tutoriel : Intégration Power BI – Créer le modèle prédictif avec un notebook Jupyter (partie 1 sur 2)

Dans la partie 1 de ce tutoriel, vous allez entraîner et déployer un modèle Machine Learning prédictif en utilisant le code d’un notebook Jupyter. Vous allez aussi créer un script de scoring pour définir le schéma d’entrée et de sortie du modèle pour l’intégration dans Power BI.  Dans la partie 2, vous utiliserez le modèle pour prédire des résultats dans Microsoft Power BI.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un notebook Jupyter.
> * Créer une instance de calcul Azure Machine Learning
> * Entraîner un modèle de régression à l’aide de scikit-learn
> * Écrivez un script de scoring qui définit l’entrée et la sortie pour une intégration facile dans Microsoft Power BI.
> * Déployer le modèle sur un point de terminaison de scoring en temps réel

Il existe trois façons de créer et de déployer le modèle que vous utiliserez dans Power BI.  Cet article concerne l’« Option A : Entraîner et déployer des modèles à l’aide de notebooks ».  Cette option permet une expérience de création Code First. Elle utilise des notebooks Jupyter qui sont hébergés dans Azure Machine Learning Studio. 

Toutefois, vous pouvez également utiliser l’une des autres options disponibles :

* [Option B : Entraîner et déployer des modèles à l’aide du concepteur Azure Machine Learning](tutorial-power-bi-designer-model.md). Cette expérience de création avec code minimal utilise une interface utilisateur de type glisser-déposer.
* [Option C : Entraîner et déployer des modèles à l’aide du machine learning automatisé](tutorial-power-bi-automated-model.md). Cette expérience de création sans code automatise entièrement la préparation des données et l’entraînement du modèle.


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/free/). 
- Un espace de travail Azure Machine Learning. Si vous ne disposez pas encore d’un espace de travail, consultez [Créer et gérer les espaces de travail Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Avoir des connaissances préliminaires du langage Python et des workflows Machine Learning.

## <a name="create-a-notebook-and-compute"></a>Créer un notebook et calculer

Dans la page d’accueil [**Azure Machine Learning Studio**](https://ml.azure.com), sélectionnez **Créer nouveau** > **Notebook** :

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Capture d’écran montrant comment créer un notebook.":::
 
Dans la page **Créer un fichier** :

1. Attribuez un nom à votre notebook (par exemple, *my_model_notebook*).
1. Modifiez **Type de fichier** en spécifiant **Notebook**.
1. Sélectionnez **Create** (Créer). 
 
Ensuite, pour exécuter des cellules de code, créez une instance de calcul et attachez-la à votre notebook. Commencez par sélectionner l’icône plus (+) située en haut du notebook :

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Capture d’écran montrant comment créer une instance de calcul.":::

Dans la page **Créer une instance de calcul** :

1. Pour la taille de machine virtuelle, choisissez Processeur. Pour ce tutoriel, vous pouvez choisir **Standard_D11_v2**, avec 2 cœurs et 14 Go de RAM.
1. Sélectionnez **Suivant**. 
1. Dans la page **Configurer les paramètres**, fournissez un **nom de capacité de calcul** valide. Les caractères valides sont les lettres majuscules et minuscules, les chiffres et les traits d’union (-).
1. Sélectionnez **Create** (Créer).

Dans le notebook, vous verrez peut-être que le cercle en regard de **Calcul** est passé au bleu. Ce changement de couleur indique que l’instance de calcul est en cours de création :

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Capture d’écran montrant le calcul en cours de création.":::

> [!NOTE]
> Le provisionnement de l’instance de calcul prend de 2 à 4 minutes.

Une fois le calcul provisionné, vous pouvez utiliser le notebook pour exécuter des cellules de code. Par exemple, dans la cellule, vous pouvez taper le code suivant :

```python
import numpy as np

np.sin(3)
```

Ensuite, sélectionnez Maj + Entrée (vous pouvez également sélectionner Ctrl + Entrée ou le bouton **Lire** en regard de la cellule). Vous devez normalement voir la sortie suivante :

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Capture d’écran montrant la sortie d’une cellule.":::

Vous êtes maintenant prêt à créer un modèle Machine Learning.

## <a name="build-a-model-by-using-scikit-learn"></a>Générer un modèle à l’aide de scikit-learn

Dans ce tutoriel, vous allez utiliser le [jeu de données Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Ce jeu de données est disponible dans [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Importer des données

Pour importer vos données, copiez-collez le code ci-dessous dans une nouvelle *cellule de code* de votre notebook.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

La trame de données pandas `X_df` contient 10 variables d’entrée de base de référence. Ces variables incluent l’âge, le sexe, l’indice de masse corporelle, la pression artérielle moyenne, ainsi que six dosages du plasma sanguin. La trame de données pandas `y_df` correspond à la variable cible. Elle comprend une mesure quantitative de la progression de la maladie, effectuée un an après la base de référence. La trame de données contient 442 enregistrements.

### <a name="train-the-model"></a>Effectuer l’apprentissage du modèle

Créez une *cellule de code* dans votre notebook. Ensuite, copiez le code suivant et collez-le dans la cellule. Cet extrait de code construit un modèle de régression Ridge, puis le sérialise à l’aide du format pickle de Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Inscrire le modèle

En plus du contenu du fichier de modèle, le modèle inscrit stocke des métadonnées. Ces métadonnées incluent la description du modèle, des étiquettes et des informations sur le framework. 

Les métadonnées sont utiles lorsque vous gérez et déployez des modèles dans votre espace de travail. Les étiquettes, par exemple, vous permettent de catégoriser vos modèles et d’appliquer des filtres lorsque vous listez les modèles dans votre espace de travail. En outre, si vous marquez ce modèle avec le framework scikit-learn, vous simplifiez son déploiement en tant que service web.

Copiez-collez le code ci-dessous dans une nouvelle *cellule de code* de votre notebook.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Vous pouvez également afficher le modèle dans Azure Machine Learning Studio. Dans le menu de gauche, sélectionnez **Modèles** :

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Capture d’écran montrant comment afficher un modèle.":::

## <a name="define-the-scoring-script"></a>Définir le script de scoring

Lorsque vous déployez un modèle à intégrer dans Power BI, vous devez définir un *script de scoring* Python et un environnement personnalisé. Le script de scoring contient deux fonctions :

- La fonction `init()` s’exécute au démarrage du service. Elle charge le modèle (qui est automatiquement téléchargé à partir du registre de modèle) et le désérialise.
- La fonction `run(data)` s’exécute lorsqu’un appel au service comprend des données d’entrée qui doivent être évaluées. 

>[!NOTE]
> Les décorateurs Python dans le code ci-dessous définissent le schéma des données d’entrée et de sortie, ce qui est important pour l’intégration dans Power BI.

Copiez-collez le code ci-dessous dans une nouvelle *cellule de code* de votre notebook. L’extrait de code suivant contient un magic de cellule qui écrit le code dans un fichier nommé *score.py*.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Définir l’environnement personnalisé

Ensuite, définissez l’environnement pour évaluer le modèle. Dans l’environnement, définissez les packages Python, tels que pandas et scikit-learn, dont a besoin le script de scoring (*score.py*).

Pour définir l’environnement, copiez-collez le code ci-dessous dans une nouvelle *cellule de code* de votre notebook.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Déployer le modèle

Pour déployer le modèle, copiez-collez le code ci-dessous dans une nouvelle *cellule de code* de votre notebook.

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Le déploiement du service prend de 2 à 4 minutes.

Si le service est déployé correctement, la sortie suivante doit s’afficher :

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Vous pouvez également afficher le service dans Azure Machine Learning Studio. Dans le menu de gauche, sélectionnez **Points de terminaison** :

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Capture d’écran montrant comment afficher un service.":::

Il est recommandé de tester le service web pour vérifier qu’il fonctionne comme prévu. Pour retourner votre notebook, dans Azure Machine Learning Studio, dans le menu de gauche, sélectionnez **Notebooks**. Ensuite, copiez-collez le code ci-dessous dans une nouvelle *cellule de code* de votre notebook pour tester le service.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

La sortie doit ressembler à cette structure JSON : `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez vu comment générer et déployer un modèle de manière à ce qu’il puisse être consommé par Power BI. Dans la partie suivante, vous allez voir comment consommer ce modèle dans un rapport Power BI.

> [!div class="nextstepaction"]
> [Tutoriel : Consommer un modèle dans Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
