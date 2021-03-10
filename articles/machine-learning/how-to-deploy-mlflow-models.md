---
title: Déployer des modèles MLflow en tant que services web
titleSuffix: Azure Machine Learning
description: Configurez MLflow avec Azure Machine Learning pour déployer vos modèles ML en tant que service web Azure.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c45b819f9fc02fae40c2bf7fc5c2247c8c0a6147
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517478"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>Déployer des modèles MLflow en tant que services web Azure (préversion)

Cet article explique comment déployer votre modèle [MLflow](https://www.mlflow.org) en tant que service web Azure afin d’exploiter les capacités d’Azure Machine Learning en matière de gestion de modèles et de détection des dérives de données et de les appliquer à vos modèles de production.

Azure Machine Learning propose des configurations de déploiement pour les composants suivants :
* Azure Container Instance (ACI), qui est un choix approprié pour un déploiement de développement-test rapide.
* Azure Kubernetes Service (AKS), qui est recommandé pour les déploiements dans des environnements de production scalables.
> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux chercheurs de données et aux développeurs qui souhaitent déployer leur modèle MLflow sur un point de terminaison de service web Azure Machine Learning. Si vous êtes un administrateur intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, voir [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).
## <a name="mlflow-with-azure-machine-learning-deployment"></a>Déploiement de MLflow avec Azure Machine Learning

MLFlow est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. Son intégration à Azure Machine Learning vous permet d’étendre cette gestion au-delà de la formation du modèle et jusqu’à la phase de déploiement de votre modèle de production.

Le diagramme suivant montre qu’avec l’API de déploiement MLflow et Azure Machine Learning, vous pouvez déployer des modèles créés à l’aide d’infrastructures populaires, telles que PyTorch, Tensorflow, scikit-learn, etc., en tant que services web Azure et les gérer dans votre espace de travail. 

![ déployer des modèles mlflow avec azure machine learning](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> En tant que bibliothèque open source, MLflow change fréquemment. Ainsi, les fonctionnalités mises à disposition via l’intégration d’Azure Machine Learning et de MLflow doivent être considérées comme une préversion et non entièrement prises en charge par Microsoft.

## <a name="prerequisites"></a>Prérequis

* Un modèle Machine Learning. Si vous n’avez pas de modèle formé, recherchez l’exemple de notebook qui correspond le mieux à votre scénario de calcul dans [ce référentiel](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) et suivez ses instructions. 
* [Configurez l’URI de suivi MLflow pour connecter Azure Machine Learning](how-to-use-mlflow.md#track-local-runs).
* Installez le package `azureml-mlflow`. 
    * Ce package intègre automatiquement `azureml-core` du [Kit de développement logiciel (SDK) Python Azure Machine Learning](/python/api/overview/azure/ml/install), qui fournit la connectivité nécessaire pour que MLflow accède à votre espace de travail.
* Découvrez les [autorisations d’accès nécessaires pour effectuer vos opérations MLflow avec votre espace de travail](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-azure-container-instance-aci"></a>Déployer sur Azure Container Instances (ACI)

Pour déployer votre modèle MLflow sur un service web Azure Machine Learning, votre modèle doit être configuré avec l’[URI de suivi MLflow pour se connecter à Azure Machine Learning](how-to-use-mlflow.md). 

Configurez votre configuration de déploiement avec la méthode [deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-). Vous pouvez également ajouter des balises et des descriptions pour faciliter le suivi de votre service web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Ensuite, inscrivez et déployez le modèle rapidement avec la méthode [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) MLflow sur Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-azure-kubernetes-service-aks"></a>Déployer sur Azure Kubernetes Service (AKS)

Pour déployer votre modèle MLflow sur un service web Azure Machine Learning, votre modèle doit être configuré avec l’[URI de suivi MLflow pour se connecter à Azure Machine Learning](how-to-use-mlflow.md). 

Pour déployer sur AKS, commencez par créer un cluster AKS. Créez un cluster AKS avec la méthode [ComputeTarget.create()](/python/api/azureml-core/azureml.core.computetarget#create-workspace--name--provisioning-configuration-). La création d’un cluster peut prendre de 20 à 25 minutes.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configurez votre configuration de déploiement avec la méthode [deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-). Vous pouvez également ajouter des balises et des descriptions pour faciliter le suivi de votre service web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Ensuite, inscrivez et déployez le modèle rapidement avec la méthode [deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) MLflow sur Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Le déploiement du service peut prendre plusieurs minutes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas d’utiliser votre service web déployé, utilisez `service.delete()` pour le supprimer de votre notebook.  Pour plus d’informations, consultez la documentation sur [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

## <a name="example-notebooks"></a>Exemples de notebooks

Les [notebooks MLflow avec Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) illustrent et développent les concepts abordés dans cet article.

> [!NOTE]
> Vous trouverez un référentiel communautaire d’exemples utilisant mlflow sur https://github.com/Azure/azureml-examples.

## <a name="next-steps"></a>Étapes suivantes

* [Gérez vos modèles](concept-model-management-and-deployment.md).
* Surveillez la [dérive des données](./how-to-enable-data-collection.md) de vos modèles de production.
* [Suivez les exécutions d’Azure Databricks avec MLflow](how-to-use-mlflow-azure-databricks.md).

