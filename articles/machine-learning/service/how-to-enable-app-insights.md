---
title: Activer Application Insights pour le service Azure Machine Learning en production
description: Découvrir comment définir Application Insights pour le service Azure Machine Learning qui a été déployé dans Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114564"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Superviser vos modèles Azure Machine Learning en production avec Application Insights

Dans cet article, vous allez pouvoir apprendre à configurer **Application Insights** pour votre service **Azure Machine Learning**. Une fois Application Insights activé, voici ce que vous pouvez superviser :
* Taux de demande, temps de réponse et taux d’échec
* Taux de dépendance, temps de réponse et taux d’échec
* Exceptions

Découvrez plus en détail Application Insights [ici](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Prérequis
* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Un espace de travail Azure Machine Learning, un répertoire local contenant vos scripts, et le SDK Azure Machine Learning pour Python. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).
* Un modèle Machine Learning entraîné à déployer sur Azure Kubernetes Service (AKS). Si vous n’en avez pas, consultez le tutoriel [Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md).
* Un [cluster AKS](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Activer et désactiver sur le portail

Vous pouvez activer et désactiver Application Insights sur le portail Azure.

### <a name="enable"></a>Activer

1. Sur le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.

1. Accédez à vos déploiements, puis sélectionnez le service dans lequel vous souhaitez activer Application Insights.

   [![Déploiements](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Cliquez sur **Modifier**, puis accédez à **Paramètres avancés**.

   [![Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. Dans **Paramètres avancés**, sélectionnez **Activer les diagnostics Application Insights**.

   [![Modifier](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications. 

### <a name="disable"></a>Désactiver
Pour désactiver Application Insights sur le portail Azure, effectuez les étapes suivantes :

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
1. Accédez à votre espace de travail.
1. Choisissez successivement **Déploiements**, **Sélectionner un service**, puis **Modifier**.

   [![Modifier](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. Dans **Paramètres avancés**, désélectionnez l’option **Activer les diagnostics AppInsights**. 

   [![décocher](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Sélectionnez **Mettre à jour** au bas de l’écran pour appliquer les modifications. 

## <a name="enable--disable-from-the-sdk"></a>Activer et désactiver à partir du kit SDK

### <a name="update-a-deployed-service"></a>Mettre à jour un service déployé
1. Identifiez le service dans votre espace de travail (ws = nom de votre espace de travail).

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Mettez à jour votre service et activez Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Journaliser des traces personnalisées dans votre service
Si vous voulez journaliser des traces personnalisées, vous devez suivre le [processus de déploiement standard pour AKS](how-to-deploy-to-aks.md), puis :

1. Mettez à jour le fichier de scoring en ajoutant des instructions print.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Mettez à jour la configuration d’aks.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Générez une image et déployez-la.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Désactiver le suivi dans Python

Pour désactiver Application Insights, utilisez le code suivant :

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Évaluer les données
Les données de votre service sont stockées dans votre compte Application Insights, dans le même groupe de ressources où se trouve votre espace de travail du service Azure Machine Learning.
Pour l’afficher :
1. Accédez à votre groupe de ressources sur le [portail Azure](https://portal.azure.com), puis cliquez sur votre ressource Application Insights. 
2. L’onglet **Vue d’ensemble** présente l’ensemble des métriques de base de votre service.

   [![Vue d’ensemble](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Pour explorer vos traces personnalisées, cliquez sur **Analytique**.
4. Dans la section schéma, cliquez sur **traces**, puis **exécutez** votre requête. Les données doivent s’afficher en dessous dans un tableau et doivent correspondre à vos appels personnalisés dans votre fichier de scoring. 

   [![Traces personnalisées](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Cliquez [ici](../../application-insights/app-insights-overview.md) pour en savoir plus sur l’utilisation d’Application Insights.
    

## <a name="example-notebook"></a>Exemple de notebook

Le notebook [00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) illustre les concepts présentés dans cet article.  Consultez ce bloc-notes :
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez aussi collecter des données sur vos modèles en production. Lisez l’article [Collecter des données pour des modèles en production](how-to-enable-data-collection.md). 
