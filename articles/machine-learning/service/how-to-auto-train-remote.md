---
title: Entraîner des modèles avec le machine learning automatisé dans le cloud - Azure Machine Learning
description: Cet article explique comment créer une ressource de calcul distante pour entraîner automatiquement vos modèles Machine Learning.
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 00d34fd0fe5f62e4da4be7d80afceb29753251bc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946967"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud-with-azure-machine-learning"></a>Entraîner des modèles avec le machine learning automatisé dans le cloud avec Azure Machine Learning

Dans Azure Machine Learning, vous pouvez entraîner votre modèle sur différents types de ressources de calcul que vous gérez. La cible de calcul peut être un ordinateur local ou dans le cloud.

Vous pouvez facilement faire monter en puissance/en charge votre expérience de machine learning. Pour cela, il vous suffit d’ajouter des cibles de calcul, par exemple une image DSVM (Data Science Virtual Machine) basée sur Ubuntu ou Azure Batch AI. DSVM est une image de machine virtuelle personnalisée sur le cloud Microsoft Azure spécialement conçue pour la science des données. Elle offre de nombreux outils de science des données populaires et d’autres outils préinstallés et préconfigurés.  

Dans cet article, vous allez découvrir comment créer un modèle à l’aide du machine learning automatisé sur l’image DSVM.  

## <a name="how-does-remote-differ-from-local"></a>En quoi l’entraînement à distance diffère-t-il de l’entraînement local ?

Le tutoriel « [Entraîner un modèle de classification avec le machine learning automatisé](tutorial-auto-train-models.md) » vous explique comment utiliser un ordinateur local pour entraîner un modèle avec le machine learning automatisé.  Le workflow lors de l’entraînement local s’applique également aux cibles distantes. Avec les cibles de calcul distantes, les itérations d’expériences de machine learning automatisé sont exécutées de façon asynchrone. Cela vous permet d’annuler une itération particulière, de suivre l’état de l’exécution et de continuer à travailler sur d’autres cellules dans le bloc-notes Jupyter. Pour effectuer l’entraînement à distance, vous créez tout d’abord une cible de calcul distante telle qu’une image DSVM Azure, vous la configurez et vous y soumettez votre code.

Cet article décrit les étapes supplémentaires nécessaires pour exécuter une expérience de machine learning automatisé sur une image DSVM à distance.  L’objet d’espace de travail, `ws`, du tutoriel est utilisé ici dans tout le code.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Créer une ressource

Créez l’image DSVM dans votre espace de travail (`ws`) si elle n’existe pas encore. Si l’image DSVM a déjà été créée, ce code ignore le processus de création et charge les détails des ressources existantes dans l’objet `dsvm_compute`.  

**Durée estimée** : la création de la machine virtuelle prend environ cinq minutes.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Vous pouvez maintenant utiliser l’objet `dsvm_compute` comme cible de calcul distante.

Voici les restrictions concernant le nom de l’image DSVM :
+ Il ne doit pas compter plus de 64 caractères.  
+ Il ne doit pas inclure les caractères suivants : `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Si la création échoue avec un message concernant l’éligibilité à l’achat sur la Place de marché :
>    1. Accédez au [Portail Azure](https://portal.azure.com).
>    1. Commencez à créer une image DSVM. 
>    1. Sélectionnez « Je souhaite créer par programmation » pour autoriser la création par programmation.
>    1. Quittez sans réellement créer la machine virtuelle.
>    1. Réexécutez le code de création.


## <a name="create-a-runconfiguration-with-dsvm-name"></a>Créez une RunConfiguration avec le nom de l’image DSVM.
Ici, vous indiquez à la configuration d’exécution le nom de votre image DSVM.

```python

# create the run configuration to use for remote training
from azureml.core.runconfig import RunConfiguration
run_config = RunConfiguration() 
# set the target to dsvm_compute created above
run_config.target = dsvm_compute 
```

Vous pouvez maintenant utiliser l’objet `run_config` comme cible pour le machine learning automatisé. 

## <a name="access-data-using-get-data-file"></a>Accéder aux données à l’aide du fichier get_data

Accordez à la ressource distante l’accès à vos données d’entraînement. Pour les expériences de machine learning automatisé exécutées sur la cible de calcul distante, les données doivent être extraites à l’aide d’une fonction `get_data()`.  

Pour fournir l’accès, vous devez :
+ Créer un fichier get_data.py contenant une fonction `get_data()`. 
* Placer ce fichier dans le répertoire racine du dossier contenant vos scripts. 

Vous pouvez encapsuler du code pour lire des données à partir d’un stockage d’objets blob ou d’un disque local dans le fichier get_data.py. Dans l’exemple de code suivant, les données proviennent du package sklearn.

>[!Warning]
>Si vous utilisez la cible de calcul distante, vous devez utiliser `get_data()` pour effectuer vos transformations de données.


```python
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-automated-machine-learning-experiment"></a>Configurer l’expérience de machine learning automatisé

Spécifiez les paramètres pour `AutoMLConfig`.  (Consultez la [liste complète des paramètres]() et leurs valeurs possibles.)

Dans les paramètres, `run_configuration` est défini sur l’objet `run_config`, qui contient les paramètres et la configuration de l’image DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "max_time_sec": 600,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             run_configuration=run_config,
                             data_script=project_folder + "./get_data.py",
                             **automl_settings
                            )
```

## <a name="submit-automated-machine-learning-training-experiment"></a>Soumettre l’expérience d’entraînement de machine learning automatisé

À présent, soumettez la configuration afin de sélectionner automatiquement l’algorithme et les hyper-paramètres, et entraînez le modèle. (Apprenez-en [davantage sur les paramètres]() pour la méthode `submit`.)

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```
Vous verrez une sortie semblable à ceci :

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0.0                      0.954     0.954
             7      Normalizer DT                         0.0                      0.161     0.954
             0      Scale MaxAbs 1 extra trees            0.0                      0.936     0.954
             4      Robust Scaler SGD classifier          0.0                      0.867     0.954
             1      Normalizer kNN                        0.0                      0.984     0.984
             9      Normalizer extra trees                0.0                      0.834     0.984
             5      Robust Scaler DT                      0.0                      0.736     0.984
             8      Standardize kNN                       0.0                      0.981     0.984
             6      Standardize SVM                       2.2                      0.984     0.984
            10      Scale MaxAbs 1 DT                     0.0                      0.077     0.984
            11      Standardize SGD classifier            0.0                      0.863     0.984
             3      Standardize gradient boosting         5.4                      0.971     0.984
            12      Robust Scaler logistic regression     2.0                      0.955     0.984
            14      Scale MaxAbs 1 SVM                    0.0                      0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      3.4                      0.971     0.989
            15      Robust Scaler kNN                     0.0                      0.904     0.989
            17      Standardize kNN                       0.0                      0.974     0.989
            16      Scale 0/1 gradient boosting           2.8                      0.968     0.989
            18      Scale 0/1 extra trees                 0.0                      0.828     0.989
            19      Robust Scaler kNN                     0.0                      0.983     0.989


## <a name="explore-results"></a>Explorer les résultats

Vous pouvez utiliser le même widget Jupyter que celui du [tutoriel d’entraînement](tutorial-auto-train-models.md#explore-the-results) pour afficher un graphe et le tableau de résultats.

```python
from azureml.train.widgets import RunDetails
RunDetails(remote_run).show()
```
Voici une image statique du widget.  Dans le bloc-notes, vous pouvez cliquer sur n’importe quelle ligne du tableau pour voir les propriétés d’exécution et les journaux de sortie pour cette série.   Vous pouvez également utiliser la liste déroulante au-dessus du graphe pour afficher un graphe de chaque métrique disponible pour chaque itération.

![tableau de widget](./media/how-to-auto-train-remote/table.png)
![tracé de widget](./media/how-to-auto-train-remote/plot.png)

Le widget affiche une URL qui vous permet de voir et d’explorer les différents détails de l’exécution.
 
### <a name="view-logs"></a>Consulter les journaux

Vous pouvez consulter les journaux sur l’image DSVM sous /tmp/azureml_run/{iterationid}/azureml-logs.

## <a name="example"></a>Exemples

Le bloc-notes `automl/03.auto-ml-remote-execution.ipynb` illustre les concepts de cet article.  Consultez ce bloc-notes :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Guide pratique pour configurer les paramètres d’entraînement automatique]().
