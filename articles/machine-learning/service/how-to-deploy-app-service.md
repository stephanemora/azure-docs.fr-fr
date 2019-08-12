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
ms.openlocfilehash: 11e8b26c81d3a8f672abbeafc153df73d063cb6e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610660"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Déployer des modèles Machine Learning sur Azure App Service (préversion)

Découvrez comment déployer un modèle à partir d’Azure Machine Learning service en tant qu’application web dans Azure App Service.

> [!IMPORTANT]
> Bien qu’Azure Machine Learning service et Azure App Service soient mis à la disposition générale, la possibilité de déployer un modèle du service Machine Learning vers App Service est en préversion.

Avec Azure Machine Learning service, vous pouvez créer des images Docker à partir de modèles Machine Learning entraînés. Cette image contient un service web qui reçoit des données, les soumet au modèle, puis retourne la réponse. Azure App Service peut être utilisé pour déployer l’image et fournit les fonctionnalités suivantes :

* [Prise en charge SSL](/azure/app-service/app-service-web-ssl-cert-load) pour les communications sécurisées entre les clients et le service.
* [Scale-out](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) vers plusieurs instances sans nécessité de redéploiement.
* [Authentification avancée](/azure/app-service/configure-authentication-provider-aad) pour une sécurité améliorée.

Pour plus d’informations sur les fonctionnalités fournies par Azure App Service, consultez la [présentation d’App Service](/azure/app-service/overview).

## <a name="prerequisites"></a>Prérequis

* Un espace de travail de service Microsoft Azure Machine Learning. Pour plus d’informations, consultez l’article [Créer un espace de travail](setup-create-workspace.md).
* Un modèle Machine Learning entraîné inscrit dans votre espace de travail. Si vous n’avez pas de modèle, utilisez le [tutoriel de classification d’image : entraîner un modèle](tutorial-train-models-with-aml.md) pour entraîner et inscrire un modèle.
* Image Docker créée à partir du modèle. Si vous n’avez pas d’image, utilisez la [classification d’image : déployer un modèle](tutorial-deploy-models-with-aml.md) pour en créer une.

## <a name="deploy-image-as-a-web-app"></a>Déployer une image en tant qu’application web

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail Azure Machine Learning. Dans la section __Présentation__ , utilisez le lien __Registre__ pour accéder à l’instance Azure Container Registry de l’espace de travail.

    ![Capture d’écran de la présentation de l’espace de travail](media/how-to-deploy-app-service/workspace-overview.png)

2. Dans Azure Container Registry, sélectionnez __Dépôts__ , puis sélectionnez le __nom de l’image__ que vous souhaitez déployer. Pour la version que vous souhaitez déployer, sélectionnez l’entrée __...__ , puis __Déployer sur l’application web__ .

    ![Capture d’écran du déploiement d’ACR vers une application web](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Pour créer l’application web, spécifiez un nom de site, un abonnement, un groupe de ressources, puis sélectionnez le plan/emplacement App Service. Pour finir, sélectionnez __Créer__ .

    ![Capture d’écran de la boîte de dialogue Nouvelle application web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Utiliser l’application web

Dans le [portail Azure](https://portal.azure.com), sélectionnez l’application web créée à l’étape précédente. Dans la section __Présentation__ , copiez l’ __. Cette valeur est l’ __ du service.

![Capture d’écran de la présentation de l’application web](media/how-to-deploy-app-service/web-app-overview.png)

Le service web qui transmet les demandes au modèle se trouve à l’emplacement `{baseurl}/score`. Par exemple : `https://mywebapp.azurewebsites.net/score`. L’extrait de code Python suivant montre comment envoyer des données à l’URL et afficher la réponse :

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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