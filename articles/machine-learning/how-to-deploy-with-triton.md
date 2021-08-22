---
title: Modèle de hautes performances avec Triton (préversion)
titleSuffix: Azure Machine Learning
description: Apprenez à déployer votre modèle à l’aide du logiciel NVIDIA Triton Inference Server dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 05/17/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 894b95b1fb00402f9cfed2614639b29ac5412f4b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446428"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Haute performance avec un serveur Triton Inference (préversion) 

Découvrez comment utiliser [le serveur Triton Inference NVIDIA](https://aka.ms/nvidia-triton-docs) pour améliorer les performances du service Web utilisé pour l’inférence de modèle.

Déployer un modèle pour une inférence peut se faire comme un service Web. Par exemple, un déploiement vers Azure Container Instances et Azure Kubernetes Service. Par défaut, Azure Machine Learning utilise une infrastructure Web à thread unique, *à usage général* pour les déploiements de service Web.

Triton est une infrastructure *optimisée pour l’inférence*. Elle offre une meilleure utilisation des GPU et une inférence plus économique. Côté serveur, elle traite par lots les requêtes entrantes et envoie ces lots pour l’inférence. Le traitement par lot mieux fait appel aux ressources GPU et constitue un élément clé des performances de Triton.

> [!IMPORTANT]
> L’utilisation de Triton pour le déploiement à partir de Azure Machine Learning est actuellement en __préversion__. Les fonctions en préversion peuvent ne pas être couvertes par le service client. Pour plus d’informations, consulter [Conditions d’utilisation supplémentaires des Préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Les extraits de code de ce document sont fournis à titre d’illustration et n’affichent pas une solution complète. Pour obtenir un exemple de code fonctionnel, consultez les [exemples de bout en bout de Triton dans Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> Le [serveur d’inférence NVIDIA Triton](https://aka.ms/nvidia-triton-docs) est un logiciel tiers Open source intégré à Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).
* Vous savez [comment et où déployer un modèle](how-to-deploy-and-where.md) avec Azure Machine Learning.
* Le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/) **ou** [Azure CLI](/cli/azure/) et [l’extension de Machine Learning](reference-azure-machine-learning-cli.md).
* Une installation fonctionnelle de Docker pour le test local. Pour plus d’informations sur l’installation et la validation de Docker, consultez [Orientation et installation](https://docs.docker.com/get-started/) dans la documentation.

## <a name="architectural-overview"></a>Vue d’ensemble de l’architecture

Avant de tenter d’utiliser Triton pour votre propre modèle, il est important de comprendre comment il fonctionne avec Azure Machine Learning et comment il est comparé à un déploiement par défaut.

**Déploiement par défaut sans Triton**

* Plusieurs rôles de travail [Gunicorn](https://gunicorn.org/) démarrent pour gérer simultanément les requêtes entrantes.
* Ces rôles de travail gèrent le prétraitement, l’appel du modèle et le post-traitement. 
* Les clients utilisent l’__URI de scoring Azure ML__. Par exemple : `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagramme d’architecture de déploiement normal, non Triton":::

**Déploiement direct avec Triton**

* Les demandes sont dirigées directement vers le serveur Triton.
* Triton traite les requêtes par lots pour optimiser l’utilisation du GPU.
* Le client utilise l’__URI Triton__ pour effectuer des requêtes. Par exemple : `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Déploiement de Inferenceconfig avec Triton uniquement, sans aucun intergiciel Python":::


## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Déploiement de Triton sans le pré-traitement ni le post-traitement de Python

Tout d’abord, suivez les étapes ci-dessous pour vérifier que le serveur d’inférence Triton peut servir votre modèle.

### <a name="optional-define-a-model-config-file"></a>(Facultati) Définir un fichier config de modèle

Le fichier config de modèle indique à Triton le nombre d’entrées attendues et les dimensions de ces entrées. Pour plus d’informations sur la création du fichier config, consultez [Configuration de modèle](https://aka.ms/nvidia-triton-docs) dans la documentation NVIDIA.

> [!TIP]
> Nous utilisons l’option `--strict-model-config=false` lors du démarrage du serveur Triton Inference, ce qui signifie que vous n’avez pas besoin de fournir un fichier `config.pbtxt` pour les modèles ONNX ou TensorFlow.
> 
> Pour plus d’informations sur la création du fichier config, consultez [Configuration de modèle](https://aka.ms/nvidia-triton-docs) dans la documentation NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Utilisez la structure de répertoire appropriée

Lors de l’inscription d’un modèle avec Azure Machine Learning, vous pouvez inscrire des fichiers individuels ou une structure de répertoires. Pour utiliser Triton, l’inscription du modèle doit être pour une structure de répertoires qui contient un répertoire nommé `triton`. La structure générale de ce répertoire est la suivante :

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Cette structure de répertoire est un référentiel de modèles Triton et est nécessaire pour que votre ou vos modèles fonctionnent avec Triton. Pour plus d’informations, consultez [Triton Model Repositories](https://aka.ms/nvidia-triton-docs) dans la documentation NVIDIA.

### <a name="register-your-triton-model"></a>Inscrire votre modèle Triton

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Pour plus d’informations sur `az ml model register`, consultez la [documentation de référence](/cli/azure/ml/model).

Lors de l’inscription du modèle dans Azure Machine Learning, la valeur du paramètre `--model-path  -p` doit être le nom du dossier parent du référentiel de modèles Triton.
Dans l'exemple ci-dessus, `--model-path` est « models ».

La valeur du paramètre `--name  -n`, `my_triton_models` dans l’exemple, est le nom du modèle connu pour l’espace de travail Azure Machine Learning. 

# <a name="python"></a>[Python](#tab/python)


[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=register-model)]

Pour plus d’informations, consultez la documentation sur la [classe Model](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Déployer votre modèle

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Si vous disposez d’un cluster Azure Kubernetes Service avec GPU, appelé « aks-gpu » et créé avec Azure Machine Learning, vous pouvez utiliser la commande suivante pour déployer votre modèle.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=deploy-webservice)]

---

[Pour plus d’informations sur le déploiement de modèles, consultez cette documentation](how-to-deploy-and-where.md).

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

### <a name="call-into-your-deployed-model"></a>Appeler votre modèle déployé

Tout d’abord, récupérez l’URI de scoring et les jetons du porteur.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=get-keys)]

---

Ensuite, vérifiez que votre service est en cours d’exécution avec : 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Cette commande renvoie des informations similaires aux suivantes. Notez le `200 OK`. Cet état indique que le serveur Web est en cours d’exécution.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=query-service)]

---


Après avoir effectué un contrôle d’intégrité, vous pouvez créer un client et envoyer des données à Triton pour l’inférence. Pour plus d’informations sur la création d’un client, consultez les [exemples de clients](https://aka.ms/nvidia-client-examples) dans la documentation NVIDIA. Il existe également des exemples [Python dans le GitHub Triton](https://aka.ms/nvidia-triton-docs).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous prévoyez de continuer à utiliser l’espace de travail Azure Machine Learning, mais que vous souhaitez supprimer le service déployé, utilisez les options suivantes :


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

[!notebook-python[] (~/Azureml-examples-main/python-sdk/experimental/deploy-triton/1.bidaf-ncd-local.ipynb?name=delete-service)]

---
## <a name="troubleshoot"></a>Dépanner

* [Résoudre les problèmes d’échec de déploiement](how-to-troubleshoot-deployment.md), découvrez comment dépanner et résoudre les erreurs courantes que vous pouvez rencontrer lors du déploiement d’un modèle.

* Si les journaux de déploiement indiquent que le **démarrage de TritonServer a échoué**, consultez la [documentation open source de Nvidia.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Étapes suivantes

* [Consulter des exemples de bout-en-bout de Triton dans Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Consulter [les exemples client Triton](https://aka.ms/nvidia-client-examples)
* Lire la [documentation du serveur Triton Inference](https://aka.ms/nvidia-triton-docs)
* [Déployer dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Mettre à jour un service web](how-to-deploy-update-web-service.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
