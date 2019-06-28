---
title: Configurer Azure Application Insights pour superviser des modèles ML
titleSuffix: Azure Machine Learning service
description: Superviser les services web déployés avec le service Azure Machine Learning dans Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 2e481a388d8cbd6baf66b95c74449396b2e70f7d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60820229"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Superviser vos modèles Azure Machine Learning avec Application Insights

Dans cet article, vous allez apprendre à configurer Application Insights pour votre service Azure Machine Learning. Application Insights vous permet de superviser :
* Les taux de demande, les temps de réponse et les taux d’échec
* Les taux de dépendance, les temps de réponse et les taux d’échec
* Les exceptions.

[En savoir plus sur Application Insights](../../azure-monitor/app/app-insights-overview.md) 


## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

* Un espace de travail Azure Machine Learning, un répertoire local contenant vos scripts, et le SDK Azure Machine Learning pour Python. Pour savoir comment obtenir ces prérequis, consultez [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).
* Un modèle de machine learning entraîné à déployer sur Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI). Si vous n’en avez pas, consultez le tutoriel [Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).


## <a name="use-sdk-to-configure"></a>Utiliser le SDK pour la configuration 

### <a name="update-a-deployed-service"></a>Mettre à jour un service déployé
1. Recherchez le service dans votre espace de travail. La valeur de `ws` correspond au nom de votre espace de travail.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Mettez à jour votre service et activez Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Journaliser des traces personnalisées dans votre service
Si vous voulez journaliser des traces personnalisées, suivez le processus de déploiement standard pour AKS ou ACI dans le document [Comment et où déployer](how-to-deploy-and-where.md). Puis procédez comme suit :

1. Mettez à jour le fichier de scoring en ajoutant des instructions print.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Mettez à jour la configuration du service.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Générez une image et déployez-la sur [AKS](how-to-deploy-to-aks.md) ou [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Désactiver le suivi dans Python

Pour désactiver Application Insights, utilisez le code suivant :

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>Utiliser le portail pour la configuration

Vous pouvez activer et désactiver Application Insights dans le portail Azure.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.

1. Sous l’onglet **Déploiements**, sélectionnez le service dans lequel vous souhaitez activer Application Insights.

   [![Liste des services sous l’onglet Déploiements](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Sélectionnez **Modifier**.

   [![Bouton Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Dans **Paramètres avancés**, cochez la case **Activer les diagnostics AppInsights**.

   [![Case à cocher pour activer les diagnostics](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications. 

### <a name="disable"></a>Désactiver
1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.
1. Sélectionnez **Déploiements**, sélectionnez le service, puis sélectionnez **Modifier**.

   [![Utiliser le bouton Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Dans **Paramètres avancés**, décochez la case **Activer les diagnostics AppInsights**. 

   [![Case à décocher pour désactiver les diagnostics](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications. 
 

## <a name="evaluate-data"></a>Évaluer les données
Les données de votre service sont stockées dans votre compte Application Insights, dans le même groupe de ressources que votre service Azure Machine Learning.
Pour l’afficher :
1. Accédez à votre espace de travail Machine Learning dans le [portail Azure](https://portal.azure.com) et cliquez sur le lien Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Sélectionnez l’onglet **Vue d’ensemble** qui présente l’ensemble des métriques de base de votre service.

   [![Vue d’ensemble](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Pour explorer vos traces personnalisées, sélectionnez **Analytique**.
4. Dans la section du schéma, sélectionnez **Traces**. Ensuite, sélectionnez **Exécuter** pour exécuter votre requête. Les données doivent s’afficher dans un tableau et doivent correspondre aux appels personnalisés de votre fichier de scoring. 

   [![Traces personnalisées](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Pour plus d’informations sur l’utilisation d’Application Insights, consultez [Présentation d’Application Insights](../../azure-monitor/app/app-insights-overview.md).
    

## <a name="example-notebook"></a>Exemple de bloc-notes

Le notebook [how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustre les concepts présentés dans cet article. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez aussi collecter des données sur vos modèles en production. Lisez l’article [Collecter des données pour des modèles en production](how-to-enable-data-collection.md). 

Lisez également [Azure Monitor pour conteneurs](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json).
