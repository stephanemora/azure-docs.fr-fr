---
title: Exécuter des prédictions par lots sur de grandes quantités de données
titleSuffix: Azure Machine Learning service
description: Découvrez comment effectuer des prédictions par lots de façon asynchrone sur de grandes quantités de données à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1e403ac0d2fbe9572a44fb3cde9d25e4df9b3db4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818500"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Exécuter des prédictions par lots sur de grands jeux de données avec Azure Machine Learning service

Dans cet article, apprendez à effectuer des prévisions sur de grandes quantités de données de façon asynchrone à l’aide d’Azure Machine Learning service.

La prédiction par lots (ou le scoring par lots) fournit une inférence économique avec un débit inégalé pour les applications asynchrones. Les pipelines de prédiction par lots peuvent être mis à l’échelle pour effectuer l’inférence sur plusieurs téraoctets de données de production. La prédiction par lots est optimisée pour les débits élevés et les prédictions autonomes (fire and forget) pour un vaste ensemble de données.

>[!TIP]
> Si votre système requiert un traitement à faible latence (pour traiter un seul document ou un petit ensemble de documents rapidement), utilisez le [scoring en temps réel](how-to-consume-web-service.md) plutôt que la prédiction par lots.

Au cours des prochaines étapes, vous allez créer un [pipeline Machine Learning](concept-ml-pipelines.md) pour inscrire un modèle de vision par ordinateur préformé ([Inception-V3](https://arxiv.org/abs/1512.00567)). Puis utiliser le modèle préformé pour effectuer le scoring par lots sur les images disponibles dans votre compte Stockage Blob Azure. Les images utilisées pour le scoring sont des images sans étiquette issues du jeu de données [ImageNet](http://image-net.org/).

## <a name="prerequisites"></a>Conditions préalables

- Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

- Configurez votre environnement de développement pour installer le SDK Azure Machine Learning. Pour obtenir davantage d’informations, consultez la page [Configurer un environnement de développement pour Azure Machine Learning](how-to-configure-environment.md).

- Créez un espace de travail Azure Machine Learning qui contiendra toutes les ressources de votre pipeline. Vous pouvez utiliser le code suivant. Pour obtenir davantage d’options, consultez la page [Créer un fichier de configuration d’espace de travail](how-to-configure-environment.md#workspace).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Configurer des ressources Machine Learning

Les étapes suivantes vous permettent de configurer les ressources dont vous avez besoin pour exécuter un pipeline :

- Accédez au magasin de données qui dispose déjà du modèle préformé, des étiquettes d’entrée et des images pour le scoring (ces paramètres sont déjà configurés).
- Configurez un magasin de données pour stocker vos sorties.
- Configurez les objets  `DataReference`  afin qu’ils pointent vers les données des magasins de données précédents.
- Configurez les machines ou les clusters de calcul où les étapes du pipeline seront exécutées.

### <a name="access-the-datastores"></a>Accéder aux magasins de données

Tout d’abord, accédez au magasin de données qui dispose du modèle, des étiquettes et des images.

Vous allez utiliser un conteneur de blobs public nommé *sampledata* dans le compte *pipelinedata* qui contient des images issues de l’ensemble d’évaluation ImageNet. Le nom du magasin de données pour ce conteneur public correspond à *images_datastore*. Enregistrez ce magasin de données avec votre espace de travail :

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Ensuite, poursuivez la configuration afin d’utiliser le magasin de données par défaut pour les sorties.

Lorsque vous créez votre espace de travail, des éléments de [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  et [Stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)  sont joints à l’espace de travail par défaut. Azure Files représente le magasin de données par défaut pour un espace de travail, mais vous pouvez également utiliser le Stockage Blob en tant que magasin de données. Pour plus d’informations, consultez [Options de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Configurer les références de données

À présent, référencez les données dans votre pipeline en tant qu’entrées pour les étapes du pipeline.

Dans un pipeline, une source de données est représentée par un objet [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) . L’objet  `DataReference`  pointe vers les données qui se trouvent dans un magasin de données ou qui sont accessibles par ce biais. Vous avez besoin des objets `DataReference`  pour le répertoire utilisé pour les images d’entrée, le répertoire de stockage du modèle préformé, le répertoire dédié aux étiquettes, ainsi que le répertoire de sortie.

```python
input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")
                           
model_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")                          
                         
label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")                          
                         
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Configurer la cible de calcul

Dans Azure Machine Learning, le *calcul* (ou la *cible de calcul*) fait référence aux machines ou aux clusters qui effectuent les étapes de calcul de votre pipeline Machine Learning. Par exemple, vous pouvez créer un `Azure Machine Learning compute`.

```python
compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, 
                        compute_name, 
                        provisioning_config)
    
    compute_target.wait_for_completion(
                     show_output=True, 
                     min_node_count=None, 
                     timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Préparer le modèle

Avant d’utiliser le modèle préformé, vous devez télécharger le modèle et l’inscrire auprès de votre espace de travail.

### <a name="download-the-pretrained-model"></a>Télécharger le modèle préformé

Téléchargez le modèle de vision par ordinateur préformé (InceptionV3) à l’adresse <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Extrayez-le ensuite dans le sous-dossier `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Inscrire le modèle

Voici comment enregistrer le modèle :

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>Écrire votre script de scoring

>[!Warning]
>Le code suivant constitue uniquement un échantillon du contenu du fichier [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) utilisé par l’[exemple de bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Vous devez créer votre propre script de scoring pour votre scénario.

Le script `batch_score.py` récupère les images d’entrée dans  *dataset_path*, les modèles préformés dans  *model_dir* et envoie les sorties *results-label.txt*  vers  *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here
  
def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

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
            
        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Générer et exécuter le pipeline de scoring par lots

Vous avez tout ce dont vous avez besoin pour créer le pipeline ; il suffit de l’assembler.

### <a name="prepare-the-run-environment"></a>Préparer l’environnement d’exécution

Spécifiez les dépendances conda pour votre script. Vous aurez besoin de cet objet lorsque vous créerez l’étape du pipeline.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Spécifier le paramètre pour votre pipeline

Créez un paramètre de pipeline en utilisant un objet  [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)  avec une valeur par défaut.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Créer l’étape du pipeline

Créez l’étape du pipeline à l’aide du script, de la configuration de l’environnement et des paramètres. Spécifiez la cible de calcul que vous avez déjà jointe à votre espace de travail en tant que cible pour l’exécution du script. Utilisez [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) pour créer l’étape du pipeline.

```python
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Exécuter le pipeline

Maintenant, exécutez le pipeline et examinez la sortie qu'il produit. La sortie fait état d’un score correspondant à chaque image d’entrée.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publier le pipeline

Une fois que vous êtes satisfait du résultat de l’exécution, publiez le pipeline afin de l’exécuter ultérieurement avec différentes valeurs d’entrée. Lorsque vous publiez un pipeline, vous obtenez un point de terminaison REST. Celui-ci accepte l’appel du pipeline avec l’ensemble des paramètres que vous avez déjà intégrés à l’aide de [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Réexécuter le pipeline à l’aide du point de terminaison REST

Pour réexécuter le pipeline, vous avez besoin d’un jeton d’en-tête d’authentification Azure Active Directory comme décrit dans la [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Étapes suivantes

Pour examiner cette opération de bout en bout, essayez le bloc-notes de scoring par lots dans [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

