---
title: Superviser et collecter des données à partir des points de terminaison de service web Machine Learning
titleSuffix: Azure Machine Learning
description: Superviser les services web déployés avec Azure Machine Learning dans Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: f4bb399e24b2c480023eba6bf5b71990f6d39c8c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978371"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Superviser et collecter des données à partir des points de terminaison de service web Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à collecter des données à partir de points de terminaison de service web et à superviser les modèles déployés sur ces points de terminaison dans Azure Kubernetes service (AKS) ou Azure Container Instances (ACI) en activant Azure Application Insights. En plus de la collecte des données d’entrée et de réponse d’un point de terminaison, vous pouvez superviser les éléments suivants :

* Taux de demande, temps de réponse et taux d’échec
* Taux de dépendance, temps de réponse et taux d’échec
* Exceptions

[Découvrez plus en détail Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui

* Un espace de travail Azure Machine Learning, un répertoire local contenant vos scripts, et le SDK Azure Machine Learning pour Python. Pour savoir comment obtenir ces prérequis, consultez [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md)
* Un modèle de machine learning entraîné à déployer sur Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI). Si vous n’en avez pas, consultez le tutoriel [Effectuer l’apprentissage d’un modèle de classification d’images](tutorial-train-models-with-aml.md).

## <a name="web-service-input-and-response-data"></a>Données d’entrée et de réponse du service web

L’entrée et la réponse au service (correspondant aux entrées du modèle ML et à sa prédiction) sont journalisées dans les suivis Azure Application Insights sous le message `"model_data_collection"`. Vous pouvez interroger Azure Application Insights directement pour accéder à ces données, ou configurer une [exportation continue](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) vers un compte de stockage pour une conservation plus longue ou un traitement ultérieur. Les données de modèle peuvent ensuite être utilisées dans le service Azure Machine Learning pour configurer l’étiquetage, le réentraînement, l’explicabilité, l’analyse des données ou toute autre utilisation. 

## <a name="use-the-azure-portal-to-configure"></a>Utiliser le portail Azure pour la configuration

Vous pouvez activer et désactiver Azure Application Insights dans le portail Azure. 

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre espace de travail

1. Sous l’onglet **Déploiements**, sélectionnez le service dans lequel vous souhaitez activer Azure Application Insights

   [![Liste des services sous l’onglet Déploiements](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Sélectionnez **Modifier**

   [![Bouton Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Dans **Paramètres avancés**, cochez la case **Activer les diagnostics AppInsights**

   [![Case à cocher pour activer les diagnostics](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications

### <a name="disable"></a>Désactiver

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez votre espace de travail
1. Sélectionnez **Déploiements**, sélectionnez le service, puis sélectionnez **Modifier**

   [![Utiliser le bouton Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Dans **Paramètres avancés**, décochez la case **Activer les diagnostics AppInsights**

   [![Case à décocher pour désactiver les diagnostics](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications
 
## <a name="use-python-sdk-to-configure"></a>Utiliser le kit SDK Python pour la configuration 

### <a name="update-a-deployed-service"></a>Mettre à jour un service déployé

1. Recherchez le service dans votre espace de travail. La valeur de `ws` correspond au nom de votre espace de travail

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Mettez à jour votre service et activez Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Journaliser des traces personnalisées dans votre service

Si vous voulez journaliser des traces personnalisées, suivez le processus de déploiement standard pour AKS ou ACI dans le document [Comment et où déployer](how-to-deploy-and-where.md). Puis procédez comme suit :

1. Mettez à jour le fichier de scoring en ajoutant des instructions print
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Mettez à jour la configuration du service
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Générez une image et déployez-la sur [AKS](how-to-deploy-to-aks.md) ou [ACI](how-to-deploy-to-aci.md)

### <a name="disable-tracking-in-python"></a>Désactiver le suivi dans Python

Pour désactiver Azure Application Insights, utilisez le code suivant :

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Évaluer les données
Les données de votre service sont stockées dans votre compte Azure Application Insights, dans le même groupe de ressources qu’Azure Machine Learning.
Pour l’afficher :

1. Accédez à votre espace de travail Azure Machine Learning dans [Azure Machine Learning Studio](https://ml.azure.com) et cliquez sur le lien Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Sélectionnez l’onglet **Vue d’ensemble** qui présente l’ensemble des métriques de base de votre service

   [![Vue d’ensemble](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Pour examiner les charges utiles d’entrée et de réponse du service web, sélectionnez **Analytics**
1. Dans la section de schéma, sélectionnez **Traces** et filtrez les traces avec le message `"model_data_collection"`. Dans les dimensions personnalisées, vous pouvez voir les entrées, les prédictions et d’autres informations pertinentes

   [![Données du modèle](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Pour explorer vos traces personnalisées, sélectionnez **Analytique**
4. Dans la section du schéma, sélectionnez **Traces**. Ensuite, sélectionnez **Exécuter** pour exécuter votre requête. Les données doivent s’afficher dans un tableau et doivent correspondre aux appels personnalisés de votre fichier de scoring

   [![Traces personnalisées](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Pour plus d’informations sur l’utilisation d’Azure Application Insights, consultez [Présentation d’Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exporter des données pour un traitement ultérieur et une plus longue conservation

Vous pouvez utiliser l’[exportation continue](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) d’Azure Application Insights pour envoyer des messages à un compte de stockage pris en charge, lorsqu’une conservation plus longue peut être définie. Les messages `"model_data_collection"` sont stockés au format JSON et peuvent être facilement analysés pour extraire des données de modèle. Vous pouvez utiliser Azure Data Factory, les pipelines Azure ML ou d’autres outils de traitement de données pour transformer les données en fonction des besoins. Une fois les données transformées, vous pouvez les inscrire auprès de l’espace de travail d’Azure Machine Learning en tant que jeu de données. Pour cela, consultez [How to create and register datasets](how-to-create-register-datasets.md) (Comment créer et inscrire des jeux de données).

   [![Exportation continue](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Exemple de bloc-notes

Le notebook [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustre les concepts présentés dans cet article. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Déployer un modèle sur un cluster Azure Kubernetes Service](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) ou [Déployer un modèle sur Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) pour déployer vos modèles sur des points de terminaison de service web et permettre à Azure Application Insights de tirer parti de la supervision des points de terminaison et de la collecte de données
* Consultez [MLOps : Déployer, gérer et superviser des modèles avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) pour en savoir plus sur l’exploitation des données collectées à partir des modèles en production. Ces données peuvent vous aider à améliorer continuellement votre processus de Machine Learning
