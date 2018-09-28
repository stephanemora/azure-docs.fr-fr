---
title: Déployer des modèles sur Kubernetes à partir du service Azure Machine Learning | Microsoft Docs
description: Découvrez comment déployer un modèle à partir du service Azure Machine Learning sur Azure Kubernetes Service. Le modèle est déployé en tant que service web. Azure Kubernetes Service est adapté aux charges de travail de production à grande échelle.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: f74521f77420fcfc60e99dd3d70574d5e94cf084
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967742"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Comment déployer des modèles à partir du service Azure Machine Learning sur Azure Kubernetes Service

Pour les scénarios de production à grande échelle, vous pouvez déployer votre modèle dans Azure Kubernetes Service (AKS). Azure Machine Learning peut utiliser un cluster AKS existant ou un nouveau cluster créé au cours du déploiement. Le modèle est déployé vers ASK comme un service web.

Avec un déploiement vers AKS, votre service web bénéficie d’une mise à l’échelle automatique, de la journalisation, de la collecte des données relatives aux modèles, ainsi que d’une rapidité de réponse.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Un espace de travail Azure Machine Learning, un répertoire local contenant vos scripts, et le SDK Azure Machine Learning pour Python. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).

- Modèle Machine Learning entraîné. Si vous n’en avez pas, consultez le tutoriel [Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).

## <a name="initialize-the-workspace"></a>Initialiser l’espace de travail

Pour initialiser l’espace de travail, chargez le fichier `config.json` qui contient les informations de votre espace de travail.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Inscrire le modèle

Pour inscrire un modèle existant, spécifiez son chemin, sa description et ses étiquettes.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Créer une image Docker

Azure Kubernetes Service utilise des images Docker. Pour créer l’image, effectuez les étapes suivantes :

1. Pour configurer l’image, vous devez créer un script de scoring et un fichier d’environnement. Pour obtenir un exemple de création de fichiers de script et d’environnement, consultez les sections suivantes de l’exemple de classification d’images :

    * [Créer un script de scoring (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

    * [Créer un fichier d’environnement (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   L’exemple suivant utilise ces fichiers pour configurer l’image :

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Pour créer l’image, utilisez le modèle et la configuration de l’image. Cette opération prend environ cinq minutes :

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Créer le cluster AKS

L’extrait de code suivant montre comment créer le cluster AKS. Ce processus prend environ 20 minutes :

> [!IMPORTANT]
> La création du cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Une fois créé, vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez créer un cluster lors du prochain déploiement.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Attacher le cluster AKS existant (facultatif)

Si votre abonnement Azure comprend déjà un cluster AKS, vous pouvez l’utiliser pour déployer votre image. L’extrait de code suivant montre comment attacher un cluster à votre espace de travail. 

> [!IMPORTANT]
> Seule la version 1.8.7 d’AKS est prise en charge.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_provisioning(True)
```

## <a name="deploy-your-web-service"></a>Déployer votre service web

L’extrait de code suivant montre comment déployer l’image sur le cluster AKS :

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Si des erreurs se produisent au cours du déploiement, utilisez `aks_service.get_logs()` pour afficher les journaux du service AKS. Les informations journalisées peuvent indiquer la cause de l’erreur.

## <a name="test-the-web-service"></a>Test du service web

Utilisez `aks_service.run()` pour tester le service web. L’extrait de code suivant montre comment passer des données au service et afficher la prédiction :

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Nettoyage

Pour supprimer le service, l’image et le modèle, utilisez l’extrait de code suivant :

```python
aks_service.delete()
image.delete()
model.delete()
```
