---
title: 'Tutoriel sur la classification d’images : Déployer des modèles'
titleSuffix: Azure Machine Learning
description: Ce tutoriel, qui est le deuxième d’une série de deux, montre comment utiliser Azure Machine Learning pour déployer un modèle de classification d’images avec scikit-learn dans un notebook Jupyter Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 81e02492f7e79b87e1513a910afe4719908adbbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159069"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutoriel : Déployer un modèle de classification d’images dans Azure Container Instances
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ce tutoriel est le **deuxième d’une série de deux**. Dans le [tutoriel précédent](tutorial-train-models-with-aml.md), vous avez entraîné des modèles Machine Learning, puis vous avez inscrit un modèle dans votre espace de travail sur le cloud.  Vous êtes maintenant prêt à déployer le modèle en tant que service web. Un service web est une image, dans le cas présent une image Docker. Elle encapsule la logique de scoring et le modèle proprement dit. 

Dans cette partie du tutoriel, vous utilisez Azure Machine Learning pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer votre environnement de test
> * Récupérer le modèle à partir de votre espace de travail
> * Tester le modèle localement
> * Déployer le modèle sur Azure Container Instances
> * Tester le modèle déployé

Container Instances est une excellente solution pour comprendre et tester le workflow. Pour les déploiements de production évolutifs, envisagez d’utiliser Azure Kubernetes Service. Pour plus d’informations, consultez [Comment et où déployer](how-to-deploy-and-where.md).

>[!NOTE]
> Le code présenté dans cet article a été testé avec le SDK Azure Machine Learning version 1.0.41.

## <a name="prerequisites"></a>Prérequis

Pour exécuter le notebook, effectuez tout d’abord l’entraînement du modèle dans le [Tutoriel (partie 1) : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).   Ouvrez ensuite le notebook *img-classification-part2-deploy.ipynb* dans le dossier *tutorials/image-classification-mnist-data* cloné.

Vous trouverez également ce tutoriel sur [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) si vous souhaitez l’utiliser dans votre propre [environnement local](how-to-configure-environment.md#local).  Veillez à installer `matplotlib` et `scikit-learn` dans votre environnement. 

> [!Important]
> Le reste de cet article contient le même contenu que ce que vous voyez dans le notebook.  
>
> Basculez maintenant vers le notebook Jupyter si vous voulez lire le code à mesure que vous l’exécutez.
> Pour exécuter une seule cellule de code dans un notebook, cliquez sur celle-ci et appuyez sur **Maj + Entrée**. Sinon, exécutez l’intégralité du notebook en choisissant **Run all** (Tout exécuter) dans la barre d’outils supérieure.

## <a name="set-up-the-environment"></a><a name="start"></a>Configurer l’environnement

Commencez par configurer un environnement de test.

### <a name="import-packages"></a>Importer des packages

Importez les packages Python nécessaires pour ce tutoriel :

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Récupérer le modèle

Vous avez inscrit un modèle dans votre espace de travail au cours du tutoriel précédent. Chargez maintenant cet espace de travail et téléchargez le modèle dans votre répertoire local :


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Tester le modèle localement

Avant de déployer, vérifiez que votre modèle fonctionne localement :
* Chargez les données de test.
* Effectuez des prédictions avec les données de test.
* Examinez la matrice de confusion.

### <a name="load-test-data"></a>Charger les données de test

Chargez les données de test à partir du répertoire **./data/** créé au cours du tutoriel consacré à l’entraînement :

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Effectuant des prédictions avec les données de test

Pour obtenir des prédictions, fournissez le jeu de données de test au modèle.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Examiner la matrice de confusion

Générez une matrice de confusion pour voir combien d’exemples du jeu de test sont classifiés correctement. Notez la valeur indiquant la mauvaise classification pour les prédictions incorrectes : 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

La sortie montre la matrice de confusion :

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Utilisez `matplotlib` pour afficher la matrice de confusion sous forme de graphe. Dans ce graphe, l’axe x montre les valeurs réelles et l’axe y montre les valeurs prédites. La couleur dans chaque grille montre le taux d’erreur. Plus la couleur est claire, plus le taux d’erreurs est élevé. Par exemple, beaucoup de 5 sont incorrectement classifiés en 3. Par conséquent, vous voyez une grille claire en (5,3) :

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Schéma montrant la matrice de confusion](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Déployer en tant que service web

Une fois que vous avez testé le modèle et que vous êtes satisfait des résultats, déployez le modèle en tant que service web hébergé dans Azure Container Instances. 

Pour créer l’environnement approprié pour Azure Container Instances, fournissez les composants suivants :
* Un script de scoring pour montrer comment utiliser le modèle
* Un fichier d’environnement pour indiquer quels packages doivent être installés
* Un fichier de configuration pour générer l’instance de conteneur
* Le modèle entraîné précédemment

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Créer le script de scoring

Créez le script de scoring, appelé **score.py**. L’appel de service web utilise ce script pour montrer comment utiliser le modèle.

Incluez ces deux fonctions nécessaires dans le script de scoring :
* La fonction `init()`, qui charge en général le modèle dans un objet global. Cette fonction est exécutée une seule fois lors du démarrage du conteneur Docker. 

* La fonction `run(input_data)` utilise le modèle pour prédire une valeur sur la base des données d’entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation, mais d’autres formats sont pris en charge.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Créer un fichier d’environnement

Ensuite, créez un fichier d’environnement nommé **myenv.yml**, qui spécifie toutes les dépendances de package du script. Ce fichier sert à vérifier que toutes ces dépendances sont installées dans l’image Docker. Ce modèle nécessite `scikit-learn` et `azureml-sdk`. Tous les fichiers d’environnement personnalisés ont besoin de lister azureml-defaults avec une version >= 1.0.45 en tant que dépendance pip. Ce package contient les fonctionnalités nécessaires pour héberger le modèle en tant que service web.

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")
myenv.add_pip_package("azureml-defaults")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Examinez le contenu du fichier `myenv.yml` :

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Créer un fichier de configuration

Créez un fichier de configuration de déploiement. Spécifiez le nombre de processeurs et de gigaoctets de RAM nécessaires pour votre conteneur Azure Container Instances. Bien que cela dépende de votre modèle, les valeurs par défaut de 1 cœur et de 1 gigaoctet de RAM sont suffisantes pour de nombreux modèles. Si plus tard vous avez besoin de davantage, vous devez recréer l’image et redéployer le service.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Déployer dans Container Instances
Le temps estimé pour terminer le déploiement est de **sept à huit minutes**.

Configurez l’image et déployez. Le code suivant effectue ces étapes :

1. Générez une image à l’aide de ces fichiers :
   * Le fichier de scoring, `score.py`
   * Le fichier d’environnement, `myenv.yml`.
   * Le fichier de modèle.
1. Inscrivez l’image sous l’espace de travail. 
1. Envoyer l’image au conteneur Azure Container Instances.
1. Démarrer un conteneur dans Azure Container Instances à l’aide de l’image.
1. Obtenir le point de terminaison HTTP du service web.

Notez que si vous définissez votre propre fichier d’environnement, vous devez lister azureml-defaults avec une version >= 1.0.45 en tant que dépendance pip. Ce package contient les fonctionnalités nécessaires pour héberger le modèle en tant que service web.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Obtenir le point de terminaison HTTP du service web de scoring qui accepte les appels du client REST. Vous pouvez partager ce point de terminaison avec toute personne souhaitant tester le service web ou l’intégrer dans une application : 

```python
print(service.scoring_uri)
```

## <a name="test-the-deployed-service"></a>Tester le service déployé

Vous avez précédemment attribué un score à toutes les données de test avec la version locale du modèle. Vous pouvez maintenant tester le modèle déployé avec un échantillon aléatoire de 30 images provenant des données de test.  

Le code suivant effectue ces étapes :
1. Envoyer les données sous forme de tableau JSON au service web hébergé dans Azure Container Instances. 

1. Utiliser l’API `run` du SDK pour appeler le service. Vous pouvez également effectuer des appels directs avec n’importe quel outil HTTP, comme **curl**.

1. Affichez les prédictions retournées et tracez-les avec les images d’entrée. Une police rouge et une image inverse (blanc sur fond noir) sont utilisées pour mettre en évidence les exemples mal classifiés. 

La précision du modèle étant élevée, il peut être nécessaire d’exécuter le code suivant plusieurs fois avant de voir un exemple mal classifié :

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Ce résultat provient d’un échantillon aléatoire d’images de test :

![Graphisme montrant les résultats](./media/tutorial-deploy-models-with-aml/results.png)

Vous pouvez également envoyer une requête HTTP brute pour tester le service web :

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour conserver le groupe de ressources et l’espace de travail pour d’autres tutoriels et d’autres explorations, vous pouvez supprimer uniquement le déploiement Azure Container Instances avec cet appel d’API :

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Étapes suivantes

+ Découvrez toutes les [options de déploiement d’Azure Machine Learning](how-to-deploy-and-where.md).
+ Découvrez comment [créer des clients pour le service web](how-to-consume-web-service.md).
+  [Effectuez des prédictions sur de grandes quantités de données](how-to-use-parallel-run-step.md) de façon asynchrone.
+ Supervisez vos modèles Azure Machine Learning avec [Application Insights](how-to-enable-app-insights.md).
+ Suivez le tutoriel [Sélection automatique d’un algorithme](tutorial-auto-train-models.md). 
