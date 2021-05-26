---
title: MLflow Tracking pour les expériences ML
titleSuffix: Azure Machine Learning
description: Configurez MLflow avec Azure Machine Learning pour enregistrer les métriques et les artefacts de modèles de ML, et déployez vos modèles ML en tant que service web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 05/25/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 783be7d595022ba08d7896540683635dbc59ade4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378836"
---
# <a name="track-ml-models-with-mlflow-and-azure-machine-learning"></a>Suivre des modèles ML avec MLflow et Azure Machine Learning

Cet article explique comment activer l’URI de suivi et l’API de journalisation de MLflow, collectivement appelées [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), pour connecter Azure Machine Learning en tant que serveur principal de vos expériences MLflow. 

Les fonctionnalités prises en charge sont les suivantes : 

+ Suivre et journaliser les métriques et les artefacts des expériences dans votre [espace de travail Azure Machine Learning](./concept-azure-machine-learning-architecture.md#workspace). Si vous utilisez déjà MLflow Tracking pour vos expériences, l’espace de travail fournit un emplacement centralisé, sécurisé et évolutif pour stocker les métriques et les modèles d’entraînement.

+ [Soumettre des tâches d’entraînement avec des projets MLflow en utilisant la prise en charge back-end Azure Machine Learning (préversion)](how-to-train-mlflow-projects.md). Vous pouvez envoyer des tâches en local en utilisant le suivi Azure Machine Learning ou migrer vos exécutions vers le cloud, par exemple, via une [capacité de calcul Azure Machine Learning](how-to-create-attach-compute-cluster.md)de calcul.

+ Suivre et gérer des modèles dans MLflow et un registre de modèles Azure Machine Learning.

[MLflow](https://www.mlflow.org) est une bibliothèque open source permettant de gérer le cycle de vie de vos expériences de Machine Learning. MLFlow Tracking est un composant de MLflow qui journalise et suit vos artefacts de modèle et métriques d’exécution d’apprentissage, quel que soit l’environnement de votre expérience, localement sur votre ordinateur, sur une cible de calcul distante, sur une machine virtuelle ou sur un [cluster Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

Le diagramme suivant montre qu’avec MLflow Tracking, vous suivez les métriques d’exécution d’une expérience et vous stockez les artefacts du modèle dans votre espace de travail Azure Machine Learning.

![diagramme de mlflow avec azure machine learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux scientifiques des données et aux développeurs qui veulent superviser le processus d’entraînement du modèle. Si vous êtes un administrateur intéressé par la supervision de l’utilisation de ressources et d’événements d’Azure Machine Learning, comme des quotas, des cycles de formation accomplis ou des déploiements de modèles effectués, voir [Supervision d’Azure Machine Learning](monitor-azure-machine-learning.md).

> [!NOTE] 
> Vous pouvez utiliser le [client MLflow Skinny](https://github.com/mlflow/mlflow/blob/master/README_SKINNY.rst), un package MLflow léger sans dépendances de stockage SQL, serveur, interface utilisateur ni science des données. Ce client est recommandé pour les utilisateurs qui ont principalement besoin des fonctionnalités de suivi et de journalisation et qui ne souhaitent pas importer la suite complète des fonctionnalités MLflow qui comprend des déploiements. 

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparer les clients MLflow et Azure Machine Learning

 Le tableau suivant récapitule les différents clients pouvant utiliser Azure Machine Learning et leurs fonctionnalités respectives.

 MLflow Tracking offre des fonctionnalités de stockage de journalisation de métriques et d’artefact qui ne sont autrement disponibles que via le [Kit de développement logiciel (SDK) Python Azure Machine Learning](/python/api/overview/azure/ml/intro).

| Fonctionnalité | Suivi et déploiement MLflow | SDK Python Azure Machine Learning |  Interface CLI Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gérer l'espace de travail |   | ✓ | ✓ | ✓ |
| Utiliser des magasins de données  |   | ✓ | ✓ | |
| Journaliser les métriques      | ✓ | ✓ |   | |
| Charger les artefacts | ✓ | ✓ |   | |
| Afficher les mesures     | ✓ | ✓ | ✓ | ✓ |
| Gérer le calcul   |   | ✓ | ✓ | ✓ |
| Déployer des modèles    | ✓ | ✓ | ✓ | ✓ |
|Surveiller les performances d'un modèle||✓|  |   |
| Détecter une dérive de données |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Prérequis

* Installez le package `azureml-mlflow`. 
    * Ce package intègre automatiquement `azureml-core` du [Kit de développement logiciel (SDK) Python Azure Machine Learning](/python/api/overview/azure/ml/install), qui fournit la connectivité nécessaire pour que MLflow accède à votre espace de travail.
* [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
    * Découvrez les [autorisations d’accès nécessaires pour effectuer vos opérations MLflow avec votre espace de travail](how-to-assign-roles.md#mlflow-operations).

## <a name="track-local-runs"></a>Suivre les exécutions locales

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions locales dans votre espace de travail Azure Machine Learning.

Importez les classes `mlflow` et [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) pour accéder à l’URI de suivi de MLflow et configurer votre espace de travail.

Dans le code suivant, la méthode `get_mlflow_tracking_uri()` attribue un adresse URI de suivi unique à l’espace de travail, `ws`, et `set_tracking_uri()` pointe le L’URI de suivi de MLflow sur cette adresse.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L’URI de suivi est valide pendant au maximum une heure. Si vous redémarrez votre script après un certain temps d’inactivité, utilisez l’API get_mlflow_tracking_uri pour obtenir un nouvel URI.

Définissez le nom de l’expérience MLflow avec `set_experiment()` et commencez votre cycle d’apprentissage avec `start_run()`. Utilisez ensuite `log_metric()` à activer l’API de journalisation de MLflow et commencer la journalisation des métriques de votre cycle d’apprentissage.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Suivre les exécutions à distance

Les exécutions à distance vous permettent d’effectuer l’apprentissage de vos modèles sur des calculs plus puissants, tels que des machines virtuelles activées par GPU ou des clusters de Capacité de calcul Machine Learning. Pour en savoir plus sur les différentes options de calcul, consultez [Utiliser des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).

MLflow Tracking avec Azure Machine Learning vous permet de stocker les métriques et artefacts consignés à partir de vos exécutions distantes dans votre espace de travail Azure Machine Learning. Toute exécution contenant du code MLflow Tracking aura des métriques journalisées automatiquement dans l’espace de travail. 

L’exemple d’environnement conda suivant comprend `mlflow` et `azureml-mlflow` en tant que packages pip. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - mlflow
    - numpy
```

Dans votre script, configurez votre environnement de calcul et d’apprentissage avec la classe [`Environment`](/python/api/azureml-core/azureml.core.environment.environment). Construisez ensuite [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) avec votre calcul à distance en tant que cible de calcul.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Avec cette configuration du cycle de calcul et d’apprentissage, utilisez la méthode `Experiment.submit()` pour soumettre un cycle. Cette méthode définit automatiquement l’URI du suivi MLflow et dirige la journalisation de MLflow vers votre espace de travail.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Afficher les métriques et les artefacts dans votre espace de travail

Les métriques et artefacts provenant de la journalisation de MLflow sont conservés dans votre espace de travail. Pour les voir à tout moment, accédez à votre espace de travail, puis recherchez l’expérience par son nom dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com).  Ou exécutez le code ci-dessous. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Gérer les modèles 

Enregistrez et suivez vos modèles avec le [registre de modèles Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) qui prend en charge le registre de modèles MLflow. Les modèles Azure Machine Learning sont alignés avec le schéma de modèle MLflow, ce qui facilite l’exportation et l’importation de ces modèles dans différents flux de travail. Les métadonnées associées à MLflow, telles que l’ID d’exécution, sont également marquées avec le modèle inscrit pour la traçabilité. Les utilisateurs peuvent soumettre des exécutions de formation, enregistrer, mais aussi déployer des modèles produits à partir de MLflow. 

Si vous souhaitez déployer et inscrire votre modèle prêt pour la production en une seule étape, consultez [déployer et inscrire des modèles MLflow](how-to-deploy-mlflow-models.md).

Pour inscrire et afficher un modèle à partir d’une exécution, procédez comme suit :

1. Une fois l’exécution terminée, appelez la méthode `register_model()`.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Affichez le modèle inscrit dans votre espace de travail avec [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    Dans l’exemple suivant, le modèle inscrit, `my-model` a des métadonnées de MLflow Tracking marquées. 

    ![register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Sélectionnez l’onglet **Artefacts** pour afficher tous les fichiers de modèle qui s’alignent sur le schéma de modèle MLflow (conda.yaml, MLmodel, model.pkl).

    ![model-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Sélectionnez MLmodel pour afficher le fichier MLmodel généré par l’exécution.

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas d’utiliser les métriques et artefacts enregistrés dans votre espace de travail, la possibilité de les supprimer individuellement est actuellement indisponible. Au lieu de cela, supprimez le groupe de ressources contenant le compte de stockage et l’espace de travail afin d’éviter des frais supplémentaires :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche.

   ![Supprimer dans le portail Azure](./media/how-to-use-mlflow/delete-resources.png)

1. À partir de la liste, sélectionnez le groupe de ressources créé.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez le nom du groupe de ressources. Puis sélectionnez **Supprimer**.

## <a name="example-notebooks"></a>Exemples de notebooks

Les [notebooks MLflow avec Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) illustrent et développent les concepts abordés dans cet article.

> [!NOTE]
> Vous trouverez un référentiel communautaire d’exemples utilisant mlflow sur https://github.com/Azure/azureml-examples.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec MLflow](how-to-deploy-mlflow-models.md).
* Surveillez la [dérive des données](./how-to-enable-data-collection.md) de vos modèles de production.
* [Suivez les exécutions d’Azure Databricks avec MLflow](how-to-use-mlflow-azure-databricks.md).
* [Gérez vos modèles](concept-model-management-and-deployment.md).
