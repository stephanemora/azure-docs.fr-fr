---
title: Package FPGA pour l’accélération matérielle dans Azure Machine Learning
description: Découvrez les packages python disponibles pour les utilisateurs d’Azure Machine Learning.
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: e680ef34be1d5dae2942c432de5e81fe620bbdc4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832976"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Package Accélération matérielle pour Azure Machine Learning

Le package Accélération matérielle pour Azure Machine Learning est une extension pip-installable de Python pour Azure Machine Learning, qui permet aux scientifiques de données et aux développeurs IA de rapidement :

+ Caractériser des images avec une version quantifiée de ResNet 50

+ Former des classifieurs en fonction de ces caractéristiques

+ Déployer des modèles sur des [FPGA (field programmable gate arrays)](concept-accelerate-with-fpgas.md) dans Azure pour une inférence à très faible latence

## <a name="prerequisites"></a>Prérequis

1. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

1. Vous devez créer un compte de gestion des modèles Azure Machine Learning. Pour obtenir plus d’informations sur la création de ce compte, consultez le [Guide de démarrage rapide d’Azure Machine Learning et l’installation de Workbench](../service/quickstart-installation.md). 

1. Le package doit être installé. 

 
## <a name="how-to-install-the-package"></a>Comment installer le package

1. Téléchargez et installez la toute dernière version de [Git](https://git-scm.com/downloads).

2. Installez [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

3. Pour télécharger un environnement Anaconda préconfiguré, utilisez la commande suivante à partir de l’invite de commandes Git :

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. Pour créer l’environnement, ouvrez une **invite de commandes Anaconda** et utilisez la commande suivante :

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. Pour activer l’environnement, utilisez la commande suivante :

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Exemple de code

Cet exemple de code vous guide tout au long de l’utilisation du kit SDK pour déployer un modèle sur un FPGA.

1. Importez le package :
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Prétraitez l’image :
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Caractérisez les images :
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Créez un classifieur :
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Créez la définition de service :
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Préparez le modèle pour qu’il s’exécute sur un FPGA :
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Déployez le modèle pour qu’il s’exécute sur un FPGA :
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Créez le client :
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Appelez l’API :
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Signaler un problème

Utilisez le [forum](https://aka.ms/aml-forum) pour signaler tout problème rencontré avec ce package.

## <a name="next-steps"></a>Étapes suivantes

[Déployer un modèle en tant que service web sur un FPGA](how-to-deploy-fpga-web-service.md)