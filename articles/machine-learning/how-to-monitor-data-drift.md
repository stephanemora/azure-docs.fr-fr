---
title: Détecter une dérive de données sur des déploiements Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Détectez une dérive de données (préversion) sur des modèles Azure Kubernetes Service déployés dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 0f56ab853983ebf9b3e27f38ae1737c0c2bce4ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430294"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Détecter une dérive de données (préversion) sur des modèles déployés sur Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dans cet article, vous apprenez à surveiller la dérive de données entre le jeu de données d’apprentissage et les données d’inférence d’un modèle déployé. Dans le contexte du Machine Learning, des modèles Machine Learning entraînés peuvent subir une dégradation des performances de prédiction à cause de la dérive. Avec Azure Machine Learning, vous pouvez surveiller la dérive de données. Le service peut alors vous envoyer une alerte par e-mail lorsqu’une dérive est détectée.

## <a name="what-is-data-drift"></a>Qu’est-ce qu’une dérive de données ?

Dans le contexte de l’apprentissage automatique, la dérive de données est la modification des données d’entrée du modèle qui entraîne une dégradation des performances du modèle. C’est l’une des principales raisons pour lesquelles la précision du modèle se dégrade au fil du temps, de sorte que la surveillance de la dérive des données permet de détecter les problèmes de performance du modèle. 

## <a name="what-can-i-monitor"></a>Que puis-je surveiller ?

Avec Azure Machine Learning, vous pouvez surveiller les entrées d’un modèle déployé sur AKS et comparer ces données au jeu de données de formations du modèle. À intervalles réguliers, un [instantané et un profil](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) sont effectués sur les données d’inférence, puis celles-ci sont calculées par rapport au jeu de données de référence pour produire une analyse de dérive de données qui : 

+ Mesure l’ampleur de la dérive de données, appelée coefficient de dérive.
+ Mesure la contribution de la dérive de données par caractéristique, indiquant les caractéristiques ayant provoqué une dérive de données.
+ Mesure des métriques de distance. Les métriques Wasserstein et de distance d’énergie sont actuellement calculées.
+ Mesure les distributions de fonctionnalités. L’estimation de la densité du noyau et les histogrammes actuellement.
+ Envoi par e-mail d’alertes de dérive de données.

> [!Note]
> Ce service est en préversion et limité en termes d’options de configuration. Consultez notre [documentation d’API](https://docs.microsoft.com/python/api/azureml-datadrift/) et nos [Notes de publication](azure-machine-learning-release-notes.md) pour plus d’informations et des mises à jour. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Supervision de la dérive des données dans Azure Machine Learning

Avec Azure Machine Learning, la dérive de données est supervisée par le biais de jeux de données ou de déploiements. Pour superviser la dérive des données, un jeu de données de référence (généralement le jeu de données d’entraînement d’un modèle) est spécifié. Un deuxième jeu de données (généralement les données d’entrée du modèle qui ont été collectées à partir d’un déploiement) est testé en le comparant avec le jeu de données de référence. Les deux jeux de données sont profilés et sont utilisés comme entrée pour le service de supervision de la dérive des données. Un modèle Machine Learning est entraîné pour détecter les différences entre les deux jeux de données. Les performances du modèle sont converties pour établir un coefficient dérive mesurant l’étendue de la dérive entre les deux jeux de données. Grâce à l’[interprétabilité des modèles](how-to-machine-learning-interpretability.md), les fonctionnalités qui contribuent au coefficient de dérive sont calculées. De nombreuses informations (profil du jeu de données, données statistiques, etc.) relatives à chaque fonctionnalité sont suivies. 

## <a name="prerequisites"></a>Conditions préalables requises

- Un abonnement Azure. Si vous n’en avez pas, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

- Kit SDK Azure Machine Learning pour Python installé. Suivez les instructions fournies dans le [kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) pour :

    - Créer un environnement Miniconda
    - Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python

- Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

- Un [fichier de configuration](how-to-configure-environment.md#workspace) d’espace de travail.

- Installez le Kit de développement logiciel (SDK)à l’aide de la commande suivante :

    ```shell
    pip install azureml-datadrift
    ```

- Créez un [jeu de données](how-to-create-register-datasets.md) à partir de données d’apprentissage de votre modèle.

- Spécifiez le jeu de données d’apprentissage lors de l’[inscription](concept-model-management-and-deployment.md) du modèle. L’exemple suivant montre comment utiliser le paramètre `datasets` pour spécifier le jeu de données d’apprentissage :

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Activez la collecte de données de modèle](how-to-enable-data-collection.md) pour collecter des données à partir du déploiement AKS du modèle et vérifiez que des données sont collectées dans le conteneur d’objets blob `modeldata`.

## <a name="configure-data-drift"></a>Configurer la dérive de données
Pour configurer la dérive des données de votre expérience, importez les dépendances comme dans l’exemple Python suivant. 

Cet exemple montre la configuration de l’objet [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) :

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Soumettre une exécution DataDriftDetector

Avec l’objet `DataDriftDetector` configuré, vous pouvez soumettre une [exécution de dérive de données](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) à une date donnée pour le modèle. Dans le cadre de l’exécution, activez les alertes du DataDriftDetector en définissant le paramètre `drift_threshold`. Si le coefficient [datadrift_coefficient](#visualize-drift-metrics) est supérieur au `drift_threshold` donné, un email est envoyé.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualiser les métriques de dérive

<a name="metrics"></a>

Après avoir soumis votre exécution de DataDriftDetector, vous pouvez voir les mesures de dérive qui sont sauvegardées dans chaque itération d’exécution pour une tâche de dérive de données :


|Métrique|Description|
--|--|
wasserstein_distance|Distance statistique définie pour la distribution numérique unidimensionnelle.|
energy_distance|Distance statistique définie pour la distribution numérique unidimensionnelle.|
datadrift_coefficient|Calculé de la même façon que le coefficient de corrélation de Matthew, mais ce résultat est un nombre réel allant de 0 à 1. Dans le contexte d’une dérive, 0 indique qu’il n’y a aucune dérive et 1 indique une dérive maximale.|
datadrift_contribution|Importance des caractéristiques des fonctionnalités contribuant à la dérive.|

Il existe plusieurs manières d’afficher les métriques de la dérive :

* Utilisez le `RunDetails`[widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Utilisez la fonction [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) sur tout objet d’exécution `datadrift`.
* Affichez les métriques dans la section **Modèles** de votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com).

L’exemple Python suivant montre comment représenter les métriques de dérive de données pertinentes. Vous pouvez utiliser les métriques retournées pour générer des visualisations personnalisées :

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Consulter une dérive de données détectée par Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Planifier des analyses de dérive de données 

Lorsque vous activez la détection de dérive de données, un DataDriftDetector est exécuté à la fréquence spécifiée et planifiée. Si le coefficient datadrift_coefficient atteint le `drift_threshold` donné, un email est envoyé avec chaque exécution planifiée. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

La configuration du détecteur de dérive de données peut être consultée sous **Modèles** dans l’onglet **Détails** de votre espace de travail sous [Azure Machine Learning Studio](https://ml.azure.com).

[![Dérive des données Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Consulter les résultats dans Azure Machine Learning Studio

Pour afficher les résultats dans votre espace de travail sous [Azure Machine Learning Studio](https://ml.azure.com), accédez à la page du modèle. Dans l’onglet de détails du modèle, la configuration de la dérive de données s’affiche. Vous pouvez désormais visualiser les métriques de dérive de données sous l’onglet **Dérive des données**. 

[![Dérive des données Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Réception d’alertes dérive

En définissant le seuil d’alerte du coefficient de dérive et en fournissant une adresse e-mail, une alerte par e-mail [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) est envoyée automatiquement chaque fois que le coefficient de dérive est supérieur au seuil. 

Pour que vous puissiez configurer des alertes et actions personnalisées, toutes les métriques de dérive de données sont stockées dans la ressource [Application Insights](how-to-enable-app-insights.md) qui a été créée avec l’espace de travail d’Azure Machine Learning. Vous pouvez suivre le lien contenu dans l’alerte par e-mail pour accéder à la requête Application Insights.

![Alerte par e-mail de dérive de données](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Recycler votre modèle après la dérive

Lorsque la dérive des données a un impact négatif sur les performances de votre modèle déployé, il est temps de recycler le modèle. Pour ce faire, procédez comme suit :

* Étudiez les données recueillies et préparer les données pour former le nouveau modèle.
* Fractionnez-le en données d’apprentissage/de test.
* Formez à nouveau le modèle à l’aide des nouvelles données.
* Évaluez les performances du modèle qui vient d’être généré.
* Déployez un nouveau modèle si les performances sont meilleures que celles du modèle de production.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple complet de l’utilisation de la dérive de données, consultez le [notebook de dérive de données Azure ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Ce Jupyter Notebook montre comment utiliser un [jeu de données ouvert Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) pour former un modèle afin de prédire la météo, le déployer sur AKS et surveiller la dérive de données. 

* Détectez une dérive de données avec des [superviseurs de jeu de données](how-to-monitor-datasets.md).

* Nous apprécierons toutes vos questions, commentaires ou suggestions dans notre processus de disponibilité générale de la dérive de données. Utilisez le bouton de commentaires sur le produit ci-dessous ! 
