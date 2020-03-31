---
title: Convertir le code d’une expérience de Machine Learning en code de production
titleSuffix: Azure Machine Learning
description: Découvrez comment convertir du code expérimental Machine Learning en code de production à l’aide du modèle de code MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477134"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Tutoriel : Convertir du code expérimental ML en code de production

Un projet de Machine Learning nécessite des expérimentations au cours desquelles des hypothèses sont testées avec des outils Agile comme Jupyter Notebook sur des jeux de données réels. Quand le modèle est prêt pour la production, vous devez placer le code du modèle dans un dépôt de code de production. Dans certains cas, le code du modèle doit être converti en scripts Python qui sont placés dans le dépôt de code de production. Ce tutoriel décrit l’approche recommandée pour exporter du code d’expérimentation en scripts Python.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Nettoyer le code non essentiel
> * Refactoriser le code Jupyter Notebook en fonctions
> * Créer des scripts Python pour des tâches associées
> * Créer des tests unitaires

## <a name="prerequisites"></a>Prérequis

- Générez le [modèle MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) et utilisez les notebooks `experimentation/Diabetes Ridge Regression Training.ipynb` et `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Ces notebooks sont utilisés pour illustrer la conversion d’une expérimentation en production. Vous pouvez trouver ces notebooks à l’adresse [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Installez nbconvert. Suivez uniquement les instructions de la section __Installing nbconvert__ (Installation de nbconvert) dans la page [Installation](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Supprimer tout le code non essentiel

Certaines parties de code écrites durant l’expérimentation sont uniquement destinées à des fins exploratoires. La première étape de conversion de code expérimental en code de production consiste donc à supprimer le code non essentiel. Cette opération rend également le code plus facile à gérer. Dans cette section, vous allez supprimer du code du notebook `experimentation/Diabetes Ridge Regression Training.ipynb`. Les instructions qui impriment la forme de `X` et `y` et la cellule qui appelle `features.describe` sont uniquement destinées à l’exploration de données et peuvent être supprimées. Une fois le code non essentiel supprimé, `experimentation/Diabetes Ridge Regression Training.ipynb` doit ressembler au code suivant sans le markdown :

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib

X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refactoriser le code en fonctions

Deuxièmement, le code Jupyter doit être refactorisé en fonctions. La refactorisation du code en fonctions facilite le test unitaire et rend le code plus facile à gérer. Dans cette section, vous allez refactoriser :

- Le notebook Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Le notebook Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactoriser le notebook Diabetes Ridge Regression Training en fonctions

Dans `experimentation/Diabetes Ridge Regression Training.ipynb`, effectuez les étapes suivantes :

1. Créez une fonction appelée `train_model` qui prend les paramètres `data` et `alpha` et retourne un modèle.
1. Copiez le code sous les titres « Train Model on Training Set » et « Validate Model on Validation Set » dans la fonction `train_model`.

La fonction `train_model` doit ressembler au code suivant :

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Une fois la fonction `train_model` créée, remplacez le code sous les titres « Train Model on Training Set » et « Validate Model on Validation Set » par l’instruction suivante :

```python
reg = train_model(data, alpha)
```

L’instruction précédente appelle la fonction `train_model` passant les paramètres `data` et `alpha` et retourne le modèle.

Dans `experimentation/Diabetes Ridge Regression Training.ipynb`, effectuez les étapes suivantes :

1. Créez une fonction appelée `main`. Celle-ci ne prend aucun paramètre et ne retourne rien.
1. Copiez le code sous les titres « Load Data », « Split Data into Training and Validation Sets » et « Save Model » dans la fonction `main`.
1. Copiez l’appel que vous venez de créer à `train_model` dans la fonction `main`.

La fonction `main` doit ressembler au code suivant :

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Une fois la fonction `main` créée, remplacez tout le code sous les titres « Load Data », « Split Data into Training and Validation Sets » et « Save Model » ainsi que l’appel que vous venez de créer à `train_model` par l’instruction suivante :

```python
main()
```

Après refactorisation, `experimentation/Diabetes Ridge Regression Training.ipynb` doit ressembler au code suivant sans le markdown :

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactoriser le notebook Diabetes Ridge Regression Scoring en fonctions

Dans `experimentation/Diabetes Ridge Regression Scoring.ipynb`, effectuez les étapes suivantes :

1. Créez une fonction appelée `init`. Celle-ci ne prend aucun paramètre et ne retourne rien.
1. Copiez le code sous le titre « Load Model » dans la fonction `init`.

La fonction `init` doit ressembler au code suivant :

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Une fois la fonction `init` créée, remplacez tout le code sous le titre « Load model » par un appel unique à `init`, comme ceci :

```python
init()
```

Dans `experimentation/Diabetes Ridge Regression Scoring.ipynb`, effectuez les étapes suivantes :

1. Créez une fonction appelée `run` qui prend les paramètres `raw_data` et `request_headers` et retourne un dictionnaire de résultats, comme ceci :

    ```python
    {"result": result.tolist()}
    ```

1. Copiez le code sous les titres « Prepare Data » et « Score Data » dans la fonction `run`.

    Le code de la fonction `run` doit ressembler au suivant (n’oubliez pas de supprimer les instructions qui définissent les variables `raw_data` et `request_headers`, qui seront utilisées par la suite quand la fonction `run` sera appelée) :

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Une fois la fonction `run` créée, remplacez tout le code sous les titres « Prepare Data » et « Score Data » par le suivant :

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Le code précédent définit les variables `raw_data` et `request_header`, appelle la fonction `run` avec `raw_data` et `request_header`, puis imprime les prédictions.

Après refactorisation, `experimentation/Diabetes Ridge Regression Scoring.ipynb` doit ressembler au code suivant sans le markdown :

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Combiner des fonctions associées dans des fichiers Python

Troisièmement, les fonctions associées doivent être fusionnées dans des fichiers Python pour faciliter la réutilisation du code. Dans cette section, vous allez créer des fichiers Python pour les notebooks suivants :

- Le notebook Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Le notebook Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Créer un fichier Python pour le notebook Diabetes Ridge Regression Training

Convertissez votre notebook en script exécutable. Pour cela, exécutez dans une invite de commandes l’instruction suivante qui utilise le package nbconvert et le chemin de `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Une fois le notebook converti en `train.py`, supprimez tous les commentaires. Votre fichier `train.py` doit ressembler au code suivant :

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

Le fichier `train.py` situé dans le répertoire `diabetes_regression/training` du dépôt MLOpsPython prend en charge les arguments de ligne de commande (à savoir `build_id`, `model_name`et `alpha`). Vous pouvez ajouter la prise en charge des arguments de ligne de commande à votre fichier `train.py` pour prendre en charge les noms de modèles dynamiques et les valeurs `alpha`, mais cela n’est pas obligatoire pour que le code s’exécute correctement.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Créer un fichier Python pour le notebook Diabetes Ridge Regression Scoring

Convertissez votre notebook en script exécutable. Pour cela, exécutez dans une invite de commandes l’instruction suivante qui utilise le package nbconvert et le chemin de `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Une fois le notebook converti en `score.py`, supprimez tous les commentaires. Votre fichier `score.py` doit ressembler au code suivant :

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

La fonction `train_model` doit être modifiée pour instancier un modèle de variable global visible dans tout le script. Ajoutez l’instruction suivante au début de la fonction `init` :

```python
global model
```

Une fois l’instruction précédente ajoutée, la fonction `init` doit ressembler au code suivant :

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Créer des tests unitaires pour chaque fichier Python

Quatrièmement, des tests unitaires doivent être créés pour chaque fichier Python afin de rendre le code plus robuste et plus facile à gérer. Dans cette section, vous allez créer un test unitaire pour l’une des fonctions de `train.py`.

`train.py` contient deux fonctions : `train_model` et `main`. Chaque fonction a besoin d’un test unitaire, mais nous n’allons en créer qu’un seul pour la fonction `train_model` à l’aide du framework Pytest dans ce tutoriel. Pytest n’est pas le seul framework de test unitaire Python, mais c’est l’un des plus couramment utilisés. Pour plus d’informations, visitez [Pytest](https://pytest.org).

Un test unitaire contient généralement trois actions principales :

- Organiser l’objet : création et configuration des objets nécessaires
- Agir sur un objet
- Déclarer ce qui est attendu

Le passage de `data` et d’une valeur `alpha` est une condition courante pour `train_model`. Le résultat attendu est l’appel des fonctions `Ridge.train` et `Ridge.predict`. Étant donné que les méthodes d’entraînement de Machine Learning prennent souvent du temps, l’appel à `Ridge.train` est simulé. Et, dans la mesure où la valeur de retour de `Ridge.train` est un objet fictif, nous simulons également `Ridge.predict`. Le test unitaire pour `train_model` teste le passage de `data` et d’une valeur `alpha` avec comme résultat attendu l’appel des fonctions `Ridge.train` et `Ridge.predict` au moyen d’une simulation et du framework Pytest. Ce test doit ressembler au code suivant :

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Utiliser votre propre modèle avec le modèle de code MLOpsPython

Si vous avez suivi les étapes de ce guide, vous disposez d’un ensemble de scripts qui correspondent aux scripts d’entraînement, de score et de test disponibles dans le dépôt MLOpsPython.  Conformément à la structure mentionnée ci-dessus, les étapes suivantes expliquent ce qu’il faut faire pour utiliser ces fichiers dans votre propre projet de Machine Learning :

1. Suivez le guide [Bien démarrer](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) pour MLOpsPython
2. Suivez les [instructions de démarrage](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) de MLOpsPython pour créer le point de départ de votre projet
3. Remplacer le code d’entraînement
4. Remplacer le code de calcul du score
5. Mettre à jour le code d’évaluation

### <a name="follow-the-getting-started-guide"></a>Suivre le guide de démarrage
Suivre le guide [Bien démarrer](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) est nécessaire pour disposer des pipelines et de l’infrastructure de prise en charge nécessaires à l’exécution de MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Suivre les instructions de démarrage

Le guide [Démarrer à partir du dépôt MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) vous aidera à préparer rapidement le dépôt pour votre projet.

**Remarque :** Comme le script de démarrage renomme le dossier diabetes_regression avec le nom de projet de votre choix, nous faisons référence à votre projet sous la forme `[project name]` quand des chemins sont impliqués.

### <a name="replace-training-code"></a>Remplacer le code d’entraînement

Le remplacement du code utilisé pour entraîner le modèle et la suppression ou le remplacement des tests unitaires correspondants sont nécessaires pour que la solution fonctionne avec votre propre code. Suivez ces étapes spécifiquement :

1. Remplacez `[project name]/training/train.py`. Ce script entraîne votre modèle localement ou sur la capacité de calcul Azure ML.
1. Supprimez ou remplacez les tests unitaires d’entraînement présents dans `[project name]/training/test_train.py`.

### <a name="replace-score-code"></a>Remplacer le code de calcul du score

Pour que le modèle fournisse des fonctionnalités d’inférence en temps réel, le code de calcul du score doit être remplacé. Le modèle MLOpsPython utilise le code de calcul du score pour déployer le modèle afin d’effectuer un scoring en temps réel sur des applications ACI, AKS ou web. Si vous souhaitez conserver le scoring, remplacez `[project name]/scoring/score.py`.

### <a name="update-evaluation-code"></a>Mettre à jour le code d’évaluation

Le modèle MLOpsPython utilise le script evaluate_model pour comparer les performances du modèle récemment entraîné à celles du modèle de production actuel selon l’erreur quadratique moyenne. Si les performances du modèle récemment entraîné sont supérieures à celles du modèle de production actuel, les pipelines continuent. Sinon, les pipelines sont annulés. Pour conserver l’évaluation, remplacez toutes les instances de `mse` dans `[project name]/evaluate/evaluate_model.py` par la métrique de votre choix.

Pour vous débarrasser de l’évaluation, définissez la variable de pipeline DevOps `RUN_EVALUATION` dans `.pipelines/[project name]-variables-template.yml` avec la valeur `false`.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment convertir une expérience en code de production, utilisez les liens suivants pour apprendre à superviser les exécutions d’expérimentation et les modèles déployés en tant que services web :

> [!div class="nextstepaction"]
> [Superviser les exécutions et les métriques des expériences Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Superviser et collecter des données à partir des points de terminaison de service web Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
