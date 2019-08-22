---
title: Guide pratique pour déployer des modèles sur des machines virtuelles Notebook
titleSuffix: Azure Machine Learning service
description: Découvrez comment déployer vos modèles Azure Machine Learning Service en tant que service web en utilisant des machines virtuelles Notebook.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: d6b26dfe1eb8ea65dd7c751a148c599123b0f6db
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947900"
---
# <a name="deploy-a-model-to-notebook-vms"></a>Déployer un modèle sur des machines virtuelles Notebook

Découvrez comment utiliser Azure Machine Learning Service pour déployer un modèle en tant que service web sur votre machine virtuelle Notebook. Utilisez les machines virtuelles Notebook si l’une des conditions suivantes est vraie :

- Vous avez besoin de déployer et de valider rapidement votre modèle.
- Vous testez un modèle en cours de développement.

> [!TIP]
> Le déploiement d’un modèle d’un notebook Jupyter sur une machine virtuelle Notebook vers un service web sur la même machine virtuelle est un _déploiement local_. Dans ce cas, l’ordinateur « local » est la machine virtuelle Notebook. Pour plus d’informations sur les déploiement, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail Azure Machine Learning Service avec une machine virtuelle Notebook en cours d’exécution. Pour plus d’informations, consultez [Configuration de l’environnement et de l’espace de travail](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Déployer sur des machines virtuelles Notebook

Un exemple de notebook illustrant les déploiements locaux est inclus sur votre machine virtuelle Notebook. Utilisez les étapes suivantes pour charger le notebook et déployer le modèle en tant que service web sur la machine virtuelle :

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez vos machines virtuelles Notebook Azure Machine Learning.

1. Ouvrez le sous-répertoire `samples-*`, puis ouvrez `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Une fois ouvert, exécutez le notebook.

    ![Capture d’écran du service local en cours d’exécution sur le notebook](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Le notebook affiche l’URL et le port sur lequel le service s’exécute. Par exemple : `https://localhost:6789`. Vous pouvez également exécuter la cellule contenant `print('Local service port: {}'.format(local_service.port))` pour afficher ce port.

    ![Capture d’écran du port du service local en cours d’exécution](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Pour tester le service à partir de la machine virtuelle Notebook, utilisez l’URL `https://localhost:<local_service.port>`. Pour effectuer un test à partir d’un client distant, obtenez l’URL publique du service en cours d’exécution sur la machine virtuelle Notebook. L’URL publique peut être déterminée à l’aide de la formule suivante : `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Par exemple : `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>Testez le service

Pour soumettre des exemples de données au service en cours d’exécution, utilisez le code suivant. Remplacez la valeur de `service_url` par l’URL de l’étape précédente :

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)