---
title: Interprétabilité des modèles en Machine Learning automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment obtenir des explications sur la façon dont votre modèle de Machine Learning automatisé détermine l’importance d’une caractéristique et effectue des prédictions lors de l’utilisation du Kit de développement logiciel (SDK) Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 5bde67913bf5e23345974f52dd6a9a22e2dd4865
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158448"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Interprétabilité des modèles en Machine Learning automatisé

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à activer les fonctionnalités d’interprétabilité pour le Machine Learning(ML) automatisé à l’aide d’Azure Machine Learning service. Le ML automatisé vous aide à comprendre l’importance des caractéristiques aussi bien brutes que traitées. Pour utiliser l’interprétabilité des modèles, définissez `model_explainability=True` dans l’objet `AutoMLConfig`.  

Dans cet article, vous apprendrez comment :

- Effectuer une interprétabilité pendant la formation pour le meilleur modèle ou n’importe quel modèle.
- Activer les visualisations pour vous aider à voir les modèles dans les données et les explications.
- Implémenter une interprétabilité pendant l’inférence ou le scoring.

## <a name="prerequisites"></a>Prérequis

- Fonctionnalités d’interprétabilité. Exécutez `pip install azureml-interpret azureml-contrib-interpret` pour obtenir les packages nécessaires.
- Des connaissances en matière de génération d’expériences de ML automatisé. Pour plus d’informations sur la façon d’utiliser le Kit de développement logiciel (SDK) Azure Machine Learning, suivez ce [tutoriel sur les modèles de régression](tutorial-auto-train-models.md) ou consultez la rubrique [Configurer des expériences de ML automatisé](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interprétabilité pendant l’entraînement pour le meilleur modèle

Récupérez l’explication à partir de `best_run`, qui inclut des explications pour les caractéristiques traitées et les caractéristiques brutes.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Télécharger l’importance des caractéristiques d’ingénierie à partir de la boutique d’artefacts

Vous pouvez utiliser `ExplanationClient` pour télécharger des explications de caractéristiques traitées à partir du magasin d’artefacts de `best_run`. Pour obtenir l’explication des caractéristiques brutes, définissez la valeur `raw=True`.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interprétabilité pendant l’entraînement de n’importe quel modèle 

Lorsque vous calculez des explications de modèle et que vous les visualisez, vous n’êtes pas limité à une explication de modèle existante pour un modèle de ML automatisé. Vous pouvez également obtenir une explication de votre modèle avec des données de test différentes. Les étapes de cette section vous montrent comment calculer et visualiser l’importance des caractéristiques traitées et brutes en fonction de vos données de test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Récupérer tout autre modèle AutoML à partir de l’entraînement

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>Configurer les explications de modèle

Utilisez `automl_setup_model_explanations` pour obtenir des explications relatives aux caractéristiques brutes et traitées. Le `fitted_model` peut générer les éléments suivants :

- Données proposées à partir d’exemples formés ou test
- Listes de noms de caractéristiques traitées et brutes
- Classes accessibles dans votre colonne étiquetée dans des scénarios de classification

Le `automl_explainer_setup_obj` contient toutes les structures de la liste ci-dessus.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initialiser l’explicatif d’imitation pour l’importance des caractéristiques

Afin de générer une explication pour les modèles AutoML, utilisez la classe `MimicWrapper`. Vous pouvez initialiser le MimicWrapper avec les paramètres suivants :

- L’objet de configuration explicatif
- Votre espace de travail
- Un modèle LightGBM, qui agit comme un substitut au modèle de ML automatisé `fitted_model`

MimicWrapper prend également l’objet `automl_run` où les explications de caractéristiques brutes et traitées seront chargées.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Utiliser MimicExplainer pour le calcul et la visualisation de l’importance des caractéristiques traitées

Vous pouvez appeler a méthode `explain()` dans MimicWrapper avec les exemples de tests transformés pour obtenir l’importance des caractéristiques traitées générées. Vous pouvez également utiliser `ExplanationDashboard` pour consulter la visualisation du tableau de bord des valeurs de l’importance des caractéristiques traitées générées par des caractériseurs de ML automatisé.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Utiliser l’explicatif d’imitation pour le calcul et la visualisation de l’importance des caractéristiques brutes

Vous pouvez à nouveau appeler la méthode `explain()` dans MimicWrapper avec les exemples de tests transformés et le paramètre `get_raw=True` pour obtenir l’importance des caractéristiques brutes. Vous pouvez également utiliser `ExplanationDashboard` pour consulter la visualisation du tableau de bord des valeurs de l’importance des caractéristiques brutes.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interprétabilité pendant l’inférence

Dans cette section, vous allez apprendre à rendre un modèle de ML automatisé opérationnel avec l’explicatif qui a été utilisé pour calculer les explications dans la section précédente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Inscrire le modèle et l’explicatif de scoring

Utilisez `TreeScoringExplainer` pour créer l’explicatif de scoring, qui calculera les valeurs d’importance des caractéristiques brutes et traitées au moment de l’inférence. Vous initialisez l’explicatif de scoring avec la valeur `feature_map` qui a été calculée précédemment. L’explicatif du scoring utilise la valeur `feature_map` pour retourner l’importance des caractéristiques brutes.

Enregistrez l’explicatif de scoring, puis inscrivez le modèle et l’explicatif de scoring auprès du service Gestion des modèles ML. Exécutez le code suivant :

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Créer les dépendances Conda pour la configuration du service

Ensuite, créez les dépendances d’environnement nécessaires dans le conteneur du modèle déployé.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Déployer le service

Déployez le service à l’aide du fichier Conda et du fichier de scoring des étapes précédentes.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inférer à l’aide de données de test

Inférez à l’aide de données de test pour voir la valeur prédite du modèle de ML automatisé. Affichez l’importance des caractéristiques traitées et brutes pour les valeurs prédites respectivement.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualiser pour découvrir des modèles dans les données et les explications au moment de la formation

Vous pouvez visualiser le graphique d’importance d’une caractéristique dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com). Une fois votre exécution de ML automatisée terminée, sélectionnez **Afficher les détails du modèle** pour afficher une exécution spécifique. Sélectionnez l’onglet **Explications** pour voir le tableau de bord de visualisation des explications.

[![Architecture d’interprétabilité de Machine Learning](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon dont vous pouvez activer les explications des modèles et l’importance d’une caractéristique dans des domaines du Kit de développement logiciel (SDK) Azure Machine Learning autres que le Machine Learning automatisé, consultez l’[article concept sur l’interprétabilité](how-to-machine-learning-interpretability.md).
