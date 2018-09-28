---
title: 'Tutoriel : Entraîner un modèle de classification d’images avec Azure Machine Learning'
description: Découvrez comment entraîner un modèle de classification d’images scikit-learn avec un bloc-notes Jupyter Python. Ce tutoriel est le premier d’une série de deux.
author: hning86
ms.author: haining
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: bed4abcce3019607715416b5194a2ddecc89b76a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966609"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning"></a>Tutoriel 1 : Entraîner un modèle de classification d’images avec Azure Machine Learning

Dans ce tutoriel, vous allez entraîner un modèle Machine Learning à la fois localement et sur des ressources de calcul distantes. Vous utiliserez le workflow de déploiement et d’entraînement pour le service Azure Machine Learning dans un bloc-notes Jupyter Python.  Vous pourrez ensuite utiliser le bloc-notes en tant que modèle pour entraîner votre propre modèle Machine Learning avec vos propres données. Ce tutoriel est le **premier d’une série de deux**.  

Ce tutoriel entraîne une régression logistique simple à l’aide du jeu de données [MNIST](http://yann.lecun.com/exdb/mnist/) et de [scikit-learn](http://scikit-learn.org) avec Azure Machine Learning.  MNIST est un jeu de données populaire composé de 70 000 images en nuances de gris. Chaque image est un chiffre manuscrit de 28x28 pixels, représentant un nombre de 0 à 9. L’objectif est de créer un classifieur multiclasse pour identifier le chiffre représenté par une image donnée. 

Découvrez comment :

> [!div class="checklist"]
> * Configurer votre environnement de développement.
> * Accéder aux données et les examiner.
> * Entraîner une régression logistique simple localement à l’aide de la bibliothèque de machine learning populaire scikit-learn. 
> * Entraîner plusieurs modèles sur un cluster distant.
> * Examiner les résultats de l’entraînement et inscrire le meilleur modèle.

Vous découvrirez comment sélectionner un modèle et le déployer dans la [deuxième partie de ce tutoriel](tutorial-deploy-models-with-aml.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="get-the-notebook"></a>Obtenir le bloc-notes

Pour des raisons pratiques, ce tutoriel est disponible en tant que bloc-notes Jupyter. Appliquez l’une ou l’autre de ces méthodes pour exécuter le bloc-notes `tutorials/01.train-models.ipynb` :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="set-up-your-development-environment"></a>Configurer votre environnement de développement

Toute la configuration pour votre travail de développement peut être effectuée dans un bloc-notes Python.  La configuration comprend les tâches suivantes :

* Importation des packages Python
* Connexion à un espace de travail pour permettre la communication entre votre ordinateur local et les ressources distantes
* Création d’une expérience pour effectuer le suivi de toutes vos exécutions
* Création d’une cible de calcul distante à utiliser pour l’entraînement

### <a name="import-packages"></a>Importer des packages

Importez les packages Python dont vous avez besoin dans cette session. Affichez également la version du SDK Azure Machine Learning.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-workspace"></a>Se connecter à l’espace de travail

Créez un objet d’espace de travail à partir de l’espace de travail existant. `Workspace.from_config()` lit le fichier **config.json** et charge les détails dans un objet nommé `ws`.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>Créer une expérience

Créez une expérience pour effectuer le suivi de toutes les exécutions dans votre espace de travail.  

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>Créer une cible de calcul distante

Azure Batch AI est un service managé qui permet aux scientifiques des données d’entraîner des modèles Machine Learning sur des clusters de machines virtuelles Azure, notamment des machines virtuelles prenant en charge les GPU.  Dans ce tutoriel, vous allez créer un cluster Azure Batch AI comme environnement d’entraînement. Ce code crée pour vous un cluster s’il n’existe pas encore dans votre espace de travail. 

 **La création du cluster prend environ cinq minutes.** Si le cluster se trouve déjà dans l’espace de travail, ce code l’utilise et ignore le processus de création.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if compute_target is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

Vous disposez désormais des packages et des ressources de calcul nécessaires pour entraîner un modèle dans le cloud. 

## <a name="explore-data"></a>Explorer les données

Avant d’entraîner un modèle, vous devez comprendre les données que vous utilisez pour l’entraîner.  Vous devez également copier les données dans le cloud afin qu’elles soient accessibles par votre environnement d’entraînement cloud.  Dans cette section, vous allez découvrir comment :

* Télécharger le jeu de données MNIST.
* Afficher des exemples d’images.
* Charger des données dans le cloud.

### <a name="download-the-mnist-dataset"></a>Télécharger le jeu de données MNIST

Téléchargez le jeu de données MNIST et enregistrez les fichiers dans un répertoire `data` localement.  Les images et les étiquettes pour l’entraînement et les tests sont téléchargées.  


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>Afficher des exemples d’images

Chargez les fichiers compressés dans des tableaux `numpy`. Utilisez ensuite `matplotlib` pour tracer 30 images aléatoires du jeu de données avec leurs étiquettes au-dessus.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Un échantillon aléatoire d’images affiche :

![échantillon aléatoire d’images](./media/tutorial-train-models-with-aml/digits.png)

Vous avez maintenant une idée de l’aspect de ces images et du résultat de prédiction attendu.

### <a name="upload-data-to-the-cloud"></a>Charger des données dans le cloud

Maintenant, vous allez rendre les données accessibles à distance en les chargeant de votre ordinateur local vers le cloud. Elles seront alors accessibles pour l’entraînement à distance. La banque de données est une construction pratique associée à votre espace de travail, dans laquelle vous pouvez charger/télécharger des données et interagir avec elles à partir de vos cibles de calcul distantes. 

Les fichiers MNIST sont chargés dans un répertoire nommé `mnist` à la racine de la banque de données.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
Vous avez maintenant tout ce dont vous avez besoin pour commencer à entraîner un modèle. 

## <a name="train-a-model-locally"></a>Entraîner un modèle localement

Entraînez un modèle de régression logistique simple à partir de scikit-learn localement.

**L’entraînement local peut prendre une minute ou deux**, en fonction de la configuration de votre ordinateur.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

Ensuite, effectuez des prédictions à l’aide du jeu de tests et calculez la précision. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

La précision du modèle local indique :

`0.9202`

En quelques lignes de code, vous avez une précision de 92 %.

## <a name="train-on-a-remote-cluster"></a>Effectuer l’entraînement sur un cluster distant

Vous pouvez maintenant étendre ce modèle simple en générant un modèle avec un taux de régularisation différent. Cette fois-ci, vous allez entraîner le modèle sur une ressource distante.  

Pour cette tâche, envoyez le travail au cluster d’entraînement distant défini précédemment.  Pour envoyer un travail, vous devez :
* Créer un répertoire.
* Créer un script d’entraînement.
* Créer un estimateur.
* Envoyer le travail. 

### <a name="create-a-directory"></a>Créer un répertoire

Créez un répertoire pour fournir le code nécessaire à partir de votre ordinateur à la ressource distante.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Créer un script d’entraînement

Pour envoyer le travail au cluster, vous devez d’abord créer un script d’entraînement. Exécutez le code suivant pour créer le script d’entraînement nommé `train.py` dans le répertoire que vous venez de créer. Cet entraînement ajoute un taux de régularisation à l’algorithme d’entraînement, et génère par conséquent un modèle légèrement différent de la version locale.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_submitted_run()

print('Train a logistic regression model with regularizaion rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Notez comment le script obtient les données et enregistre les modèles :

+ Le script d’entraînement lit un argument pour rechercher le répertoire contenant les données.  Quand vous envoyez le travail ultérieurement, vous pointez vers la banque de données pour cet argument : `parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`

    
+ Le script d’entraînement enregistre votre modèle dans un répertoire nommé outputs. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
Tout ce qui est écrit dans ce répertoire est chargé automatiquement dans votre espace de travail. Vous accéderez à votre modèle à partir de ce répertoire plus loin dans le tutoriel.

Le fichier `utils.py` est référencé à partir du script d’entraînement pour charger le jeu de données correctement.  Copiez ce script dans le dossier de script afin qu’il soit accessible en plus du script d’entraînement sur la ressource distante.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Créer un estimateur

Un objet estimateur est utilisé pour soumettre l’exécution.  Créez votre estimateur en exécutant le code suivant pour définir :

* Le nom de l’objet estimateur, `est`.
* Le répertoire qui contient vos scripts. Tous les fichiers dans ce répertoire sont chargés dans les nœuds de cluster pour l’exécution. 
* La cible de calcul.  Ici, vous utiliserez le cluster Batch AI que vous avez créé.
* Le nom du script d’entraînement, train.py.
* Les paramètres requis à partir du script d’entraînement. 
* Les packages Python nécessaires pour l’entraînement.

Dans ce tutoriel, cette cible est le cluster Batch AI. Tous les fichiers dans le répertoire du projet sont chargés dans les nœuds de cluster pour l’exécution. data_folder est configuré pour utiliser la banque de données (`ds.as_mount()`).

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Envoyer le travail au cluster

Exécutez l’expérience en soumettant l’objet estimateur.

```python
run = exp.submit(config=est)
run
```

L’appel étant asynchrone, il retourne un état **en cours d’exécution** dès le démarrage du travail.

## <a name="monitor-a-remote-run"></a>Superviser une exécution à distance

Au total, la première exécution prend **environ 10 minutes**. Mais pour les exécutions suivantes, tant que les dépendances de script ne changent pas, la même image est réutilisée et la durée de démarrage du conteneur est donc beaucoup plus courte.

Voici ce qui se passe pendant que vous attendez :

- **Création d’image** : une image Docker correspondant à l’environnement Python spécifié par l’estimateur est créée. L’image est chargée dans l’espace de travail. La création et le chargement de l’image prennent **environ cinq minutes**. 

  Cette étape se produit une fois pour chaque environnement Python, puisque le conteneur est mis en cache pour les exécutions suivantes.  Lors de la création d’image, les journaux sont diffusés en continu vers l’historique d’exécutions. Vous pouvez superviser la progression de la création d’image à l’aide de ces journaux.

- **Mise à l’échelle** : si le cluster distant nécessite plus de nœuds que la quantité disponible actuellement, des nœuds supplémentaires sont ajoutés automatiquement. En général, la mise à l’échelle prend **environ cinq minutes.**

- **Exécution** : durant cette étape, les fichiers et les scripts nécessaires sont envoyés à la cible de calcul, les banques de données sont montées/copiées, puis entry_script est exécuté. Pendant que le travail s’exécute, stdout et le répertoire ./logs sont diffusés en continu vers l’historique d’exécutions. Vous pouvez superviser la progression de l’exécution à l’aide de ces journaux.

- **Post-traitement** : le répertoire ./outputs de l’exécution est copié dans l’historique d’exécutions dans votre espace de travail afin que vous puissiez accéder à ces résultats.


Vous pouvez vérifier la progression d’un travail en cours d’exécution de plusieurs façons. Ce tutoriel utilise un widget Jupyter ainsi qu’une méthode `wait_for_completion`. 

### <a name="jupyter-widget"></a>Widget Jupyter

Surveillez la progression de l’exécution avec un widget Jupyter.  Comme la soumission d’exécution, le widget est asynchrone et fournit des mises à jour automatiques toutes les 10 à 15 secondes jusqu’à ce que le travail soit terminé.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

Voici un instantané du widget fourni à la fin de l’entraînement :

![widget de bloc-notes](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Obtenir des résultats du journal lors de l’achèvement

L’entraînement et la supervision du modèle se produisent en arrière-plan. Attendez que l’entraînement du modèle soit terminé avant d’exécuter davantage de code. Utilisez `wait_for_completion` pour indiquer quand l’entraînement du modèle est terminé. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Afficher les résultats de l’exécution

Vous disposez maintenant d’un modèle entraîné sur un cluster distant.  Récupérez la précision du modèle :

```python
print(run.get_metrics())
```
La sortie montre que le modèle distant a une précision légèrement plus élevée que le modèle local, en raison de l’ajout du taux de régularisation pendant l’entraînement.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

Dans le tutoriel de déploiement, vous explorerez ce modèle plus en détail.

## <a name="register-model"></a>Inscrire le modèle

La dernière étape du script d’entraînement a écrit le fichier `outputs/sklearn_mnist_model.pkl` dans un répertoire nommé `outputs` sur la machine virtuelle du cluster où le travail est exécuté. `outputs` est un répertoire spécial, dans la mesure où tout son contenu est chargé automatiquement dans votre espace de travail.  Ce contenu apparaît dans l’enregistrement d’exécution dans l’expérience sous votre espace de travail. Ainsi, le fichier de modèle est désormais disponible également dans votre espace de travail.

Vous pouvez voir les fichiers associés à cette exécution.

```python
print(run.get_file_names())
```

Inscrivez le modèle dans l’espace de travail afin de pouvoir (vous-même ou d’autres collaborateurs) interroger, examiner et déployer ultérieurement ce modèle.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez aussi supprimer uniquement le cluster de calcul managé Azure. Toutefois, étant donné que la mise à l’échelle automatique est activée et que la valeur minimale de cluster est 0, cette ressource particulière n’entraînera aucun frais de calcul supplémentaire en cas de non-utilisation.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel Azure Machine Learning, vous avez utilisé Python pour :

> [!div class="checklist"]
> * Configurer l’environnement de développement.
> * Accéder aux données et les examiner.
> * Entraîner une régression logistique simple localement à l’aide de la bibliothèque de machine learning populaire scikit-learn.
> * Entraîner plusieurs modèles sur un cluster distant.
> * Examiner les détails de l’entraînement et inscrire le meilleur modèle.

Vous êtes prêt à déployer ce modèle inscrit en suivant les instructions fournies dans la deuxième partie de cette série de tutoriels :

> [!div class="nextstepaction"]
> [Tutoriel 2 : Déployer des modèles](tutorial-deploy-models-with-aml.md)