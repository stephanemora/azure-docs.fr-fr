---
title: 'Didacticiel : Pipelines ML pour le scoring par lots'
titleSuffix: Azure Machine Learning
description: 'Dans ce tutoriel, vous créez un pipeline Machine Learning pour exécuter un scoring par lots sur un modèle de classification d’images dans Azure Machine Learning. Les pipelines Machine Learning optimisent votre workflow à divers niveaux : vitesse, portabilité et réutilisation. Ainsi, vous pouvez vous concentrer sur votre domaine d’expertise, le Machine Learning, plutôt que sur l’infrastructure et l’automatisation.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 11d57031405939e37afade9a9452c5d3a8b9e1e4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483295"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Didacticiel : Créer un pipeline Azure Machine Learning pour le scoring par lots

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans ce tutoriel, vous utilisez un pipeline Azure Machine Learning pour exécuter un travail de scoring par lots. L’exemple utilise le modèle Tensorflow de réseau neuronal convolutif [Inception-V3](https://arxiv.org/abs/1512.00567) préentraîné pour classifier les images sans étiquette. Après avoir créé et publié un pipeline, vous configurez un point de terminaison REST qui vous permet de déclencher ce pipeline à partir de n’importe quelle bibliothèque HTTP sur n’importe quelle plateforme.

Les pipelines Machine Learning optimisent votre workflow à divers niveaux : vitesse, portabilité et réutilisation. Ainsi, vous pouvez vous concentrer sur votre domaine d’expertise, le Machine Learning, plutôt que sur l’infrastructure et l’automatisation. [En savoir plus sur les pipelines Machine Learning](concept-ml-pipelines.md).

Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer l’espace de travail et télécharger des exemples de données
> * Créer des objets de données pour extraire et sortir des données
> * Télécharger, préparer et inscrire le modèle dans votre espace de travail
> * Provisionner des cibles de calcul et créer un script de scoring
> * Créer, exécuter et publier un pipeline
> * Activer un point de terminaison REST pour le pipeline

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore d’espace de travail Azure Machine Learning ou de machine virtuelle de notebook, effectuez la [Partie 1 du tutoriel d’installation](tutorial-1st-experiment-sdk-setup.md).
* Une fois le tutoriel d’installation effectué, utilisez le même serveur de notebooks pour ouvrir le notebook *tutorials/tutorial-pipeline-batch-scoring-classification.ipynb*.

Si vous souhaitez suivre le tutoriel d’installation dans votre propre [environnement local](how-to-configure-environment.md#local), vous pouvez y accéder sur [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Exécutez `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` pour vous procurer les packages requis.

## <a name="configure-workspace-and-create-a-datastore"></a>Configurer l’espace de travail et créer un magasin de données

Créez un objet d’espace de travail à partir de l’espace de travail Azure Machine Learning existant.

- Un [espace de travail](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) est une classe qui accepte les informations concernant vos abonnements et ressources Azure. L’espace de travail crée également une ressource cloud qui vous permet de superviser et de suivre les exécutions de votre modèle. 
- `Workspace.from_config()` lit le fichier `config.json`, puis charge les informations d’authentification dans un objet nommé `ws`. L’objet `ws` est utilisé dans le code tout au long de ce tutoriel.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Créer une banque de données pour des exemples d’images

Pour le compte `pipelinedata`, récupérez l’exemple de données publiques d’évaluation ImageNet à partir du conteneur d’objets blob public `sampledata`. Appelez `register_azure_blob_container()` pour rendre les données disponibles dans l’espace de travail sous le nom `images_datastore`. Définissez ensuite le magasin de données par défaut de l’espace de travail en tant que magasin de données de sortie. Utilisez le magasin de données de sortie pour effectuer un scoring de la sortie dans le pipeline.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Créer des objets de données

Quand vous créez un pipeline, un objet `DataReference` lit les données dans le magasin de données de l’espace de travail. Un objet `PipelineData` transfère les données intermédiaires entre les étapes du pipeline.

> [!Important]
> L’exemple de scoring par lots de ce tutoriel utilise une seule étape de pipeline. Dans les cas d’usage qui comportent plusieurs étapes, le flux classique inclut les étapes suivantes :
>
> 1. Utilisez des objets `DataReference` en tant qu’*entrées* pour extraire les données brutes, exécutez certaines transformations, puis effectuez la *sortie* d’un objet `PipelineData`.
>
> 2. Utilisez l’*objet de sortie* `PipelineData` de l’étape précédente en tant qu’*objet d’entrée*. Répétez cette opération pour les étapes suivantes.

Dans ce scénario, vous créez des objets `DataReference` qui correspondent aux répertoires de magasin de données pour les images d’entrée et les étiquettes de classification (valeurs de test y). Vous créez également un objet `PipelineData` pour les données de sortie du scoring par lots.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Télécharger et inscrire le modèle

Téléchargez le modèle Tensorflow préentraîné afin de l’utiliser pour le scoring par lots dans un pipeline. Commencez par créer un répertoire local où stocker le modèle. Téléchargez et extrayez ensuite le modèle.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Inscrivez ensuite le modèle auprès de votre espace de travail pour pouvoir le récupérer facilement dans le processus de pipeline. Dans la fonction statique `register()`, le paramètre `model_name` représente la clé à utiliser pour localiser votre modèle dans le kit SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Créer et attacher la cible de calcul distante

Vous ne pouvez pas exécuter les pipelines Machine Learning localement, donc exécutez-les sur des ressources cloud ou des *cibles de calcul distantes*. Une cible de calcul distante est un environnement Compute virtuel réutilisable où vous exécutez des expériences et des workflows Machine Learning. 

Exécutez le code suivant pour créer une cible [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) ayant un GPU, puis attachez-la à votre espace de travail. Pour plus d’informations sur les cibles de calcul, consultez l’[article conceptuel](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Écrire un script de scoring

Pour effectuer le scoring, créez un script de scoring par lots nommé `batch_scoring.py` dans le répertoire actif. Le script accepte les images d’entrée, applique le modèle de classification, puis génère les prédictions dans un fichier de résultats.

Le script `batch_scoring.py` accepte les paramètres suivants, lesquels sont passés à partir de l’étape de pipeline que vous allez créer plus loin dans ce tutoriel :

- `--model_name`: Nom du modèle utilisé.
- `--label_dir`: Répertoire qui contient le fichier `labels.txt`.
- `--dataset_path`: Répertoire qui contient les images d’entrée.
- `--output_dir`: Répertoire de sortie du fichier `results-label.txt`, une fois que le script a exécuté le modèle sur les données.
- `--batch_size`: Taille de lot utilisée pour l’exécution du modèle.

L’infrastructure de pipeline utilise la classe `ArgumentParser` pour passer les paramètres aux étapes de pipeline. Par exemple, dans le code suivant, le premier argument `--model_name` reçoit l’identificateur de propriété `model_name`. Dans la fonction `main()`, `Model.get_model_path(args.model_name)` permet d’accéder à cette propriété.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Le pipeline de ce tutoriel comporte une seule étape et écrit la sortie dans un fichier. Pour les pipelines multiétapes, `ArgumentParser` vous permet également de définir un répertoire où écrire les données de sortie à entrer dans les étapes suivantes. Pour obtenir un exemple de passage de données entre plusieurs étapes de pipeline à l’aide du modèle de conception `ArgumentParser`, consultez le [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Créer et exécuter le pipeline

Avant d’exécuter le pipeline, créez un objet qui définit l’environnement Python et crée les dépendances nécessaires à votre script `batch_scoring.py`. La dépendance principale nécessaire est Tensorflow. Toutefois, vous devez également installer `azureml-defaults` à partir du kit SDK pour les processus en arrière-plan. Créez un objet `RunConfiguration` à l’aide des dépendances. Spécifiez également la prise en charge de Docker et Docker-GPU.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Paramétrer le pipeline

Définissez un paramètre personnalisé pour que le pipeline contrôle la taille du lot. Une fois le pipeline publié et exposé via un point de terminaison REST, tous les paramètres configurés sont également exposés. Vous pouvez spécifier des paramètres personnalisés dans la charge utile JSON quand vous réexécutez le pipeline via une requête HTTP.

Créez un objet `PipelineParameter` pour activer ce comportement ainsi que pour définir un nom et une valeur par défaut.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Créer l’étape du pipeline

Une étape de pipeline est un objet qui encapsule tout ce dont vous avez besoin pour exécuter un pipeline, notamment :

* Paramètres d’environnement et de dépendance
* Ressource de calcul où exécuter le pipeline
* Données d’entrée et de sortie ainsi que tous les paramètres personnalisés
* Référence à un script ou une logique de kit SDK à exécuter au cours de l’étape

Plusieurs classes héritent de la classe parente [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). Vous pouvez choisir des classes pour utiliser des frameworks ou des piles spécifiques afin de créer une étape. Dans cet exemple, vous utilisez la classe [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) pour définir votre logique d’étape à l’aide d’un script Python personnalisé. Si un argument de votre script est une entrée ou une sortie de l’étape, il doit être défini *à la fois* dans le tableau `arguments` *ainsi que* dans le paramètre `input` ou `output`, respectivement. 

Dans les scénarios qui comportent plusieurs étapes, une référence d’objet dans le tableau `outputs` devient disponible en tant qu’*entrée* pour une étape de pipeline suivante.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Pour obtenir la liste de toutes les classes utilisables dans les différents types d’étape, consultez les informations relatives au [package d’étapes](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Exécuter le pipeline

À présent, exécutez le pipeline. Commencez par créer un objet `Pipeline` en utilisant votre référence d’espace de travail et l’étape de pipeline que vous avez créée. Le paramètre `steps` est un tableau d’étapes. Dans le cas présent, le scoring par lots ne comporte qu’une seule étape. Pour créer des pipelines qui comportent plusieurs étapes, placez ces dernières dans l’ordre dans ce tableau.

Utilisez ensuite la fonction `Experiment.submit()` pour soumettre l’exécution du pipeline. Vous spécifiez également le paramètre personnalisé `param_batch_size`. La fonction `wait_for_completion` génère des journaux durant le processus de création du pipeline. Vous pouvez utiliser les journaux pour voir la progression actuelle.

> [!IMPORTANT]
> La première exécution du pipeline prend environ *15 minutes*. Toutes les dépendances doivent être téléchargées, une image Docker est créée, et l’environnement Python est provisionné et créé. La réexécution du pipeline prend beaucoup moins de temps, car ces ressources sont réutilisées au lieu d’être créées. Toutefois, le temps total d’exécution du pipeline dépend de la charge de travail de vos scripts et des processus qui s’exécutent à chaque étape du pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Télécharger et passer en revue la sortie

Exécutez le code suivant pour télécharger le fichier de sortie créé à partir du script `batch_scoring.py`. Examinez ensuite les résultats du scoring.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Nom de fichier</th>
      <th>Prédiction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102.JPEG</td>
      <td>Rhodesian Ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103.JPEG</td>
      <td>trépied</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104.JPEG</td>
      <td>clavier de machine à écrire</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105.JPEG</td>
      <td>Silky Terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106.JPEG</td>
      <td>nœud Windsor</td>
    </tr>
    <tr>
      <td>5\.</td>
      <td>ILSVRC2012_val_00000107.JPEG</td>
      <td>moissonneur</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108.JPEG</td>
      <td>violon</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109.JPEG</td>
      <td>haut-parleur</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110.JPEG</td>
      <td>tablier</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111.JPEG</td>
      <td>homard américain</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publier et exécuter à partir d’un point de terminaison REST

Exécutez le code suivant pour publier le pipeline sur votre espace de travail. Dans votre espace de travail dans Azure Machine Learning Studio, vous pouvez voir les métadonnées du pipeline, notamment l’historique des exécutions et leurs durées. Vous pouvez également exécuter le pipeline manuellement à partir du studio.

La publication du pipeline active un point de terminaison REST qui vous permet de réexécuter le pipeline à partir de n’importe quelle bibliothèque HTTP sur n’importe quelle plateforme.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Pour exécuter le pipeline à partir du point de terminaison REST, vous avez besoin d’un en-tête d’authentification de type porteur OAuth2. L’exemple suivant utilise l’authentification interactive (à des fins d’illustration). Toutefois, dans la plupart des scénarios de production qui nécessitent une authentification automatisée ou sans assistance, utilisez l’authentification du principal de service, comme [indiqué dans ce notebook](https://aka.ms/pl-restep-auth).

Pour permettre l’authentification du principal de service, créez une *inscription d’application* dans *Azure Active Directory*. Commencez par générer un secret client, puis accordez au principal de service un *accès en fonction du rôle* à votre espace de travail Machine Learning. Utilisez la classe [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) pour gérer votre flux d’authentification. 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) et `ServicePrincipalAuthentication` héritent de `AbstractAuthentication`. Dans les deux cas, utilisez la fonction [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) de la même manière pour extraire l’en-tête :

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Obtenez l’URL REST de la propriété `endpoint` de l’objet de pipeline publié. Vous pouvez également trouver l’URL REST dans votre espace de travail dans Azure Machine Learning Studio. 

Créez une requête HTTP POST au point de terminaison. Spécifiez votre en-tête d’authentification dans la requête. Ajoutez un objet de charge utile JSON ayant le nom de l’expérience et le paramètre de taille de lot. Comme indiqué plus tôt dans le tutoriel, `param_batch_size` est passé à votre script `batch_scoring.py`, car vous l’avez défini en tant qu’objet `PipelineParameter` dans la configuration de l’étape.

Exécutez la requête pour déclencher l’exécution. Incluez le code permettant d’accéder à la clé `Id` à partir du dictionnaire de réponses pour obtenir la valeur de l’ID d’exécution.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Utilisez l’ID d’exécution pour superviser l’état de la nouvelle exécution. La nouvelle exécution prend à nouveau 10 à 15 minutes pour s’achever. 

La nouvelle exécution va ressembler au pipeline que vous avez exécuté dans le tutoriel. Vous pouvez choisir de ne pas voir la sortie complète.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Sautez cette section si vous prévoyez de suivre d’autres tutoriels Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Arrêter la machine virtuelle Notebook

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Tout supprimer

Si vous n’avez pas l’intention d’utiliser les ressources que vous avez créées, supprimez-les pour éviter des frais :

1. Dans le portail Azure, dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, sélectionnez le groupe de ressources que vous avez créé.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources. Ensuite, sélectionnez **Supprimer**.

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel sur les pipelines Machine Learning, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Création d’un pipeline avec des dépendances d’environnement à exécuter sur une ressource de calcul GPU distante
> * Création d’un script de scoring pour exécuter des prédictions par lots à l’aide d’un modèle Tensorflow préentraîné
> * Publication d’un pipeline à exécuter à partir d’un point de terminaison REST

Pour obtenir des exemples supplémentaires de création de pipelines à l’aide du kit SDK Machine Learning, consultez le [dépôt de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
