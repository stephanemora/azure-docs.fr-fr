---
title: 'Didacticiel : Déployer un modèle de classification d’images dans Azure Container Instances avec le service Azure Machine Learning'
description: Ce didacticiel montre comment utiliser le service Azure Machine Learning pour déployer un modèle de classification d’images avec scikit-learn dans un bloc-notes Jupyter Python.  Ce tutoriel est le deuxième d’une série de deux.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 841448f477accb8a73d543447cd317bb9b427408
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497596"
---
# <a name="tutorial-2--deploy-an-image-classification-model-in-azure-container-instance-aci"></a>Tutoriel #2 : Déployer un modèle de classification d’images dans Azure Container Instances

Ce tutoriel est le **deuxième d’une série de deux**. Dans le [tutoriel précédent](tutorial-train-models-with-aml.md), vous avez entraîné des modèles Machine Learning, puis vous avez inscrit un modèle dans votre espace de travail sur le cloud.  

Vous êtes maintenant prêt à déployer le modèle en tant que service web dans [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Un service web est une image, dans ce cas une image Docker, qui encapsule la logique de scoring et le modèle lui-même. 

Dans cette partie du tutoriel, vous utilisez le service Azure Machine Learning pour :

> [!div class="checklist"]
> * Configurer votre environnement de test
> * Récupérer le modèle à partir de votre espace de travail
> * Tester le modèle localement
> * Déployer le modèle sur ACI
> * Tester le modèle déployé

ACI n’est pas idéal pour les déploiements de production, mais il est très utile pour tester et comprendre le flux de travail. Pour les déploiements de production évolutifs, envisagez d’utiliser [Azure Kubernetes Service](how-to-deploy-to-aks.md).

## <a name="get-the-notebook"></a>Obtenir le bloc-notes

Pour des raisons pratiques, ce didacticiel est disponible en tant que [bloc-notes Jupyter](https://aka.ms/aml-notebook-tut-02). Exécutez le bloc-notes `02.deploy-models.ipynb` dans des Azure Notebooks ou dans votre propre serveur de bloc-notes Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]


## <a name="prerequisites"></a>Prérequis

Effectuez l’entraînement du modèle dans le bloc-notes [Didacticiel n° 1 : entraîner un modèle de classification d’images avec le service Azure Machine Learning](tutorial-train-models-with-aml.md).  


## <a name="set-up-the-environment"></a>Configurer l’environnement

Commencez par configurer un environnement de test.

### <a name="import-packages"></a>Importer des packages

Importer les packages Python nécessaires pour ce tutoriel.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Récupérer le modèle

Vous avez inscrit un modèle dans votre espace de travail au cours du tutoriel précédent. Chargez maintenant cet espace de travail et téléchargez le modèle dans votre répertoire local.


```python
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')
model.download(target_dir = '.')
import os 
# verify the downloaded model file
os.stat('./sklearn_mnist_model.pkl')
```

## <a name="test-model-locally"></a>Tester le modèle localement

Avant de déployer, vérifiez que votre modèle fonctionne localement :
* En chargeant des données de test
* En effectuant des prédictions avec les données de test
* En examinant la matrice de confusion

### <a name="load-test-data"></a>Charger les données de test

Chargez les données de test à partir du répertoire **./data/** créé au cours du tutoriel consacré à l’entraînement.

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>Effectuant des prédictions avec les données de test

Alimentez le jeu de données de test du modèle pour obtenir des prédictions.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Examiner la matrice de confusion

Générez une matrice de confusion pour voir combien d’exemples du jeu de test sont classifiés correctement. Notez la valeur indiquant la mauvaise classification pour les prédictions incorrectes. 

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
   

Utilisez `matplotlib` pour afficher la matrice de confusion sous forme de graphe. Dans ce graphe, l’axe X représente les valeurs réelles, et l’axe Y représente les valeurs prédites. La couleur dans chaque grille représente le taux d’erreur. Plus la couleur est claire, plus le taux d’erreurs est élevé. Par exemple, beaucoup de 5 sont incorrectement classifiés en 3. Par conséquent, vous voyez une grille claire en (5,3).

```python
# normalize the diagnal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
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

![Matrice de confusion](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-web-service"></a>Déployer en tant que service web

Une fois que vous avez testé le modèle et que vous êtes satisfait des résultats, déployez le modèle en tant que service web hébergé dans ACI. 

Pour créer l’environnement approprié pour ACI, fournissez les éléments suivants :
* Un script de scoring pour montrer comment utiliser le modèle
* Un fichier d’environnement pour indiquer quels packages doivent être installés
* Un fichier de configuration pour créer l’ACI
* Le modèle que vous avez précédemment entraîné

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Créer le script de scoring

Créez le script de scoring, nommé score.py, utilisé par l’appel du service web pour montrer comment utiliser le modèle.

Vous devez inclure deux fonctions nécessaires dans le script de scoring :
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
    # retreive the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Créer un fichier d’environnement

Ensuite, créez un fichier d’environnement nommé myenv.yml, qui spécifie toutes les dépendances de package du script. Ce fichier est utilisé pour vérifier que toutes ces dépendances sont installées dans l’image Docker. Ce modèle nécessite `scikit-learn` et `azureml-sdk`.

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Examinez le contenu du fichier `myenv.yml`.

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-configuration-file"></a>Création d’un fichier de configuration

Créez un fichier de configuration de déploiement, et spécifiez le nombre de processeurs et de gigaoctets de RAM nécessaires pour votre conteneur ACI. Bien que cela dépende de votre modèle, les valeurs par défaut de 1 cœur et de 1 gigaoctet de RAM sont généralement suffisantes pour de nombreux modèles. Si vous pensez plus tard que des valeurs supérieures sont nécessaires, vous devrez recréer l’image et redéployer le service.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Déployer dans ACI
Durée estimée : **environ 7 à 8 minutes**

Configurez l’image et déployez. Le code suivant effectue ces étapes :

1. Créer une image avec :
   * Le fichier de scoring (`score.py`)
   * Le fichier d’environnement (`myenv.yml`)
   * Le fichier de modèle
1. Inscrire cette image sous l’espace de travail. 
1. Envoyer l’image dans le conteneur ACI.
1. Démarrer un conteneur dans ACI en utilisant l’image.
1. Obtenir le point de terminaison HTTP du service web.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Obtenir le point de terminaison HTTP du service web de scoring qui accepte les appels du client REST. Ce point de terminaison peut être partagé avec toute personne souhaitant tester le service web ou l’intégrer dans une application. 

```python
print(service.scoring_uri)
```


## <a name="test-deployed-service"></a>Tester le service déployé

Vous avez précédemment attribué un score à toutes les données de test avec la version locale du modèle. Vous pouvez maintenant tester le modèle déployé avec un échantillon aléatoire de 30 images provenant des données de test.  

Le code suivant effectue ces étapes :
1. Envoyer les données sous forme de tableau JSON au service web hébergé dans ACI. 

1. Utiliser l’API `run` du SDK pour appeler le service. Vous pouvez également effectuer des appels directs avec n’importe quel outil HTTP, comme curl.

1. Affichez les prédictions retournées et tracez-les avec les images d’entrée. Une police rouge et une image inverse (blanc sur fond noir) sont utilisées pour mettre en évidence les exemples mal classifiés. 

 Comme la précision du modèle est élevée, il peut être nécessaire d’exécuter le code suivant plusieurs fois avant de voir un exemple mal classifié.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
result = json.loads(service.run(input_data=test_samples))

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Voici le résultat d’un échantillon aléatoire d’images de test : ![résultats](./media/tutorial-deploy-models-with-aml/results.png)

Vous pouvez également envoyer une demande HTTP directe pour tester le service web.

```python
import requests
import json

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour conserver le groupe de ressources et l’espace de travail pour d’autres tutoriels et d’autres explorations, vous pouvez supprimer seulement le déploiement ACI avec cet appel d’API :

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel sur le service Azure Machine Learning, vous avez utilisé Python pour :

> [!div class="checklist"]
> * Configurer votre environnement de test
> * Récupérer le modèle à partir de votre espace de travail
> * Tester le modèle localement
> * Déployer le modèle sur ACI
> * Tester le modèle déployé
 
Vous pouvez également essayer le didacticiel [Sélection automatique d’un algorithme](tutorial-auto-train-models.md) pour voir comment le service Azure Machine Learning peut sélectionner automatiquement et optimiser le meilleur algorithme pour votre modèle et générer ce modèle pour vous.
