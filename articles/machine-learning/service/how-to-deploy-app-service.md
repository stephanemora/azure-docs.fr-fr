---
title: Déployer des modèles ML sur Azure App Service (préversion)
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser Azure Machine Learning service pour déployer un modèle sur une application web dans Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897460"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Déployer des modèles Machine Learning sur Azure App Service (préversion)

Découvrez comment déployer un modèle à partir d’Azure Machine Learning service en tant qu’application web dans Azure App Service.

> [!IMPORTANT]
> Bien qu’Azure Machine Learning service et Azure App Service soient mis à la disposition générale, la possibilité de déployer un modèle du service Machine Learning vers App Service est en préversion.

Avec Azure Machine Learning service, vous pouvez créer des images Docker à partir de modèles Machine Learning entraînés. Cette image contient un service web qui reçoit des données, les soumet au modèle, puis retourne la réponse. Azure App Service peut être utilisé pour déployer l’image et fournit les fonctionnalités suivantes :

* [Authentification](/azure/app-service/configure-authentication-provider-aad) avancée pour une sécurité améliorée. Les méthodes d’authentification incluent Azure Active Directory et l’authentification multifacteur.
* [Effectuer une mise à l’échelle automatique](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) sans nécessité de redéploiement.
* [Prise en charge SSL](/azure/app-service/app-service-web-ssl-cert-load) pour les communications sécurisées entre les clients et le service.

Pour plus d’informations sur les fonctionnalités fournies par Azure App Service, consultez la [présentation d’App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Si vous avez besoin de la possibilité de consigner les données de scoring utilisées avec votre modèle déployé, ou les résultats de scoring, il est préférable d’effectuer le déploiement vers Azure Kubernetes Service. Pour plus d’informations, consultez [Collecter des données relatives à vos modèles de production](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Prérequis

* Un espace de travail de service Microsoft Azure Machine Learning. Pour plus d’informations, consultez l’article [Créer un espace de travail](how-to-manage-workspace.md).
* Un modèle Machine Learning entraîné inscrit dans votre espace de travail. Si vous n’avez pas de modèle, utilisez le [tutoriel de classification d’image : entraîner un modèle](tutorial-train-models-with-aml.md) pour entraîner et inscrire un modèle.

    > [!IMPORTANT]
    > Les extraits de code de cet article partent du principe que vous avez défini les variables suivantes :
    >
    > * `ws` - Votre espace de travail Azure Machine Learning.
    > * `model` - Modèle inscrit qui sera déployé.
    > * `inference_config` - Configuration d’inférence pour le modèle.
    >
    > Pour plus d’informations sur la définition de ces variables, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Préparation du déploiement

Avant le déploiement, vous devez définir ce qui est nécessaire pour exécuter le modèle en tant que service web. La liste suivante décrit les éléments de base nécessaires pour un déploiement :

* __Script d’entrée__. Ce script accepte les requêtes, évalue la requête à l’aide du modèle et renvoie les résultats.

    > [!IMPORTANT]
    > Le script d’entrée est spécifique à votre modèle. Il doit comprendre le format des données de la requête entrante, le format des données attendues par votre modèle et le format des données renvoyées aux clients.
    >
    > Si le format des données de la requête n'est pas utilisable par votre modèle, le script peut les convertir à un format acceptable. Il peut également transformer la réponse avant de la renvoyer au client.

    > [!IMPORTANT]
    > Le Kit de développement logiciel (SDK) Azure Machine Learning n’offre aucun moyen pour le service web d’accéder à votre magasin de données ou à vos jeux de données. Si vous avez besoin du modèle déployé pour accéder aux données stockées en dehors du déploiement, comme dans un compte de stockage Azure, vous devez développer une solution de code personnalisée à l’aide du Kit de développement logiciel (SDK) approprié. Exemple : [Kit de développement logiciel (SDK) Stockage Azure pour Python](https://github.com/Azure/azure-storage-python).
    >
    > Une autre solution possible pour votre scénario consiste à utiliser les [prédictions par lots](how-to-run-batch-predictions.md), qui donnent accès aux magasins de travail lors du scoring.

    Pour plus d’informations sur les scripts d’entrée, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

* **Dépendances**, comme les scripts d’assistance ou les packages Python/Conda nécessaires à l’exécution du script d’entrée ou du modèle

Ces entités sont encapsulées dans une __configuration d'inférence__. La configuration d’inférence référence le script d’entrée et d’autres dépendances.

> [!IMPORTANT]
> Lors de la création d’une configuration d’inférence à utiliser avec Azure App Service, vous devez utiliser un objet [Environnement](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py). L’exemple suivant illustre la création d’un objet d’environnement et son utilisation avec une configuration d’inférence :
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Pour plus d’informations sur les environnements , consultez [Créer et gérer des environnements pour la formation et le déploiement](how-to-use-environments.md).

Pour plus d’informations sur la configuration de l’inférence, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Lors du déploiement vers Azure App Service, il n’est pas nécessaire de créer une __configuration de déploiement__.

## <a name="create-the-image"></a>Création de l’image

Pour créer l’image du Docker qui est déployée sur Azure App Service, utilisez [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). L’extrait de code suivant montre comment construire une nouvelle image à partir de la configuration du modèle et de l’inférence :

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Si la condition est `show_output=True`, la sortie du processus de génération Docker s’affiche. Une fois le processus terminé, l’image a été créée dans le registre Azure Container Registry pour votre espace de travail.

## <a name="deploy-image-as-a-web-app"></a>Déployer une image en tant qu’application web

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail Azure Machine Learning. Dans la section __Présentation__, utilisez le lien __Registre__ pour accéder à l’instance Azure Container Registry de l’espace de travail.

    [![Capture d’écran de la présentation de l’espace de travail](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. Dans Azure Container Registry, sélectionnez __Dépôts__, puis sélectionnez le __nom de l’image__ que vous souhaitez déployer. Pour la version que vous souhaitez déployer, sélectionnez l’entrée __...__ , puis __Déployer sur l’application web__.

    [![Capture d’écran du déploiement d’ACR vers une application web](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. Pour créer l’application web, spécifiez un nom de site, un abonnement, un groupe de ressources, puis sélectionnez le plan/emplacement App Service. Pour finir, sélectionnez __Créer__.

    ![Capture d’écran de la boîte de dialogue Nouvelle application web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Utiliser l’application web

Dans le [portail Azure](https://portal.azure.com), sélectionnez l’application web créée à l’étape précédente. Dans la section __Présentation__, copiez l’__URL__. Cette valeur est l’__URL de base__ du service.

[![Capture d’écran de la présentation de l’application web](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

Le service web qui transmet les demandes au modèle se trouve à l’emplacement `{baseurl}/score`. Par exemple : `https://mywebapp.azurewebsites.net/score`. L’extrait de code Python suivant montre comment envoyer des données à l’URL et afficher la réponse :

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la configuration de votre application web, consultez la documentation [App Service sur Linux](/azure/app-service/containers/).
* Pour plus d’informations sur la mise à l’échelle, consultez [Bien démarrer avec la mise à l’échelle automatique dans Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Pour plus d’informations sur la prise en charge de SSL, consultez [Utiliser un certificat SSL dans Azure App Service](/azure/app-service/app-service-web-ssl-cert-load).
* Pour plus d’informations sur l’authentification, consultez [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](/azure/app-service/configure-authentication-provider-aad).
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)