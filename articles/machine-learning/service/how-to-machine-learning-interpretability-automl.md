---
title: Interprétabilité des modèles dans le machine learning automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment expliquer pourquoi votre modèle ML automatisé élabore des prédictions à l’aide du SDK Azure Machine Learning. Il peut être utilisé pendant l’entraînement et l’inférence pour comprendre comment un modèle détermine l’importance des caractéristiques et effectue des prédictions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511050"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Interprétabilité des modèles pour les modèles ML automatisé

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans ce guide pratique, vous allez apprendre à activer la caractéristique d’interprétabilité dans le machine learning automatisé à l’aide du service Azure Machine Learning. Le ML automatisé vous permet de comprendre l’importance des caractéristiques aussi bien brutes que traitées. Pour utiliser l’interprétabilité des modèles, définissez `model_explainability=True` dans l’objet `AutoMLConfig`.  

Dans cet article, vous apprenez à effectuer les tâches suivantes :

* Interprétabilité pendant l’entraînement pour le meilleur modèle ou n’importe quel modèle
* Activation des visualisations pour vous aider dans la découverte de modèles dans les données et les explications
* Interprétabilité pendant l’inférence ou le scoring

## <a name="prerequisites"></a>Prérequis

* Exécutez `pip install azureml-interpret azureml-contrib-interpret` pour obtenir les packages nécessaires pour les caractéristiques d’interprétabilité.
* Cet article suppose des connaissances en matière de génération d’expériences de machine learning automatisé. Pour savoir comment utiliser le ML automatisé dans le SDK, consultez le [tutoriel](tutorial-auto-train-models.md) ou le [guide pratique](how-to-configure-auto-train.md).
 
## <a name="interpretability-during-training-for-the-best-model"></a>Interprétabilité pendant l’entraînement pour le meilleur modèle 

Récupérez l’explication à partir de `best_run`, qui inclut des explications pour les caractéristiques traitées et les caractéristiques brutes. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Télécharger l’importance des caractéristiques d’ingénierie à partir de la boutique d’artefacts

Vous pouvez utiliser `ExplanationClient` pour télécharger des explications de caractéristiques d’ingénierie à partir du magasin d’artefacts de best_run. Pour obtenir l’explication des caractéristiques brutes, définissez la valeur `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interprétabilité pendant l’entraînement de n’importe quel modèle 

Dans cette section, vous allez apprendre à calculer des explications de modèle et à les visualiser. En plus de la récupération d’une explication de modèle existante pour un modèle ML automatisé, vous pouvez également expliquer votre modèle avec d’autres données de test. Les étapes suivantes vous permettent de calculer et de visualiser l’importance des caractéristiques traitées et l’importance des caractéristiques brutes basées sur vos données de test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Récupérer tout autre modèle AutoML à partir de l’entraînement

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Configurer les explications de modèle

fitted_model peut générer les éléments suivants, qui seront utilisés pour obtenir les explications de caractéristiques traitées et brutes à l’aide d’automl_setup_model_explanations :

* Données caractérisées à partir d’exemples d’entraînement/de test
* Regrouper des listes de noms de caractéristiques traitées et brutes
* Rechercher les classes présentes dans votre colonne étiquetée dans des scénarios de classification

automl_explainer_setup_obj contient toutes les structures de la liste ci-dessus.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initialiser l’explicatif d’imitation pour l’importance des caractéristiques

Pour expliquer les modèles AutoML, utilisez la classe `MimicWrapper`. MimicWrapper peut être initialisé avec des paramètres pour l’objet de configuration d’explicatif, votre espace de travail et un modèle LightGBM qui sert de modèle de substitution pour expliquer le modèle ML automatisé (fitted_model ici). MimicWrapper prend également l’objet automl_run où les explications de caractéristiques brutes et traitées seront chargées.

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

La méthode explain() dans MimicWrapper peut être appelée avec les exemples de tests transformés pour obtenir l’importance des caractéristiques traitées générées. Vous pouvez également utiliser ExplanationDashboard pour consulter la visualisation du tableau de bord des valeurs de l’importance des caractéristiques traitées générées par des caractériseurs de ML automatisé.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Utiliser l’explicatif d’imitation pour le calcul et la visualisation de l’importance des caractéristiques brutes

La méthode explain() dans MimicWrapper peut à nouveau être appelée avec les exemples de tests transformés et le paramètre `get_raw` défini sur True pour obtenir l’importance des caractéristiques brutes. Vous pouvez également utiliser ExplanationDashboard pour consulter la visualisation du tableau de bord des valeurs de l’importance des caractéristiques brutes.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interprétabilité pendant l’inférence

Dans cette section, vous allez apprendre à rendre un modèle ML automatisé opérationnel avec l’explicatif qui a été utilisé pour calculer les explications de la section précédente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Inscrire le modèle et l’explicatif de scoring

Utilisez `TreeScoringExplainer` pour créer l’explicatif de scoring, qui sera utilisé pour calculer les valeurs d’importance des caractéristiques brutes et traitées au moment de l’inférence. Notez que vous initialisez l’explicatif de scoring avec la valeur feature_map qui a été calculée précédemment. La valeur feature_map sera utilisée par l’explicatif de scoring pour retourner l’importance des caractéristiques brutes.

Dans le code ci-dessous, vous enregistrez l’explicatif de scoring. Vous inscrivez également le modèle et l’explicatif de scoring auprès du service Gestion des modèles.

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

Ensuite, vous créez les dépendances d’environnement nécessaires dans le conteneur du modèle déployé.

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

### <a name="inference-using-test-data"></a>Inférence à l’aide de données de test

Effectuez une inférence à l’aide de données de test pour voir la valeur prédite à partir du modèle ML automatisé, puis regardez l’importance des caractéristiques traitées et l’importance des caractéristiques brutes pour la valeur prédite.

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

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Visualisations pour vous aider dans la découverte de modèles dans les données et les explications au moment de l’entraînement

Vous pouvez également visualiser le graphique d’importance des caractéristiques dans votre espace de travail dans [Azure Machine Learning Studio](https://ml.azure.com). Une fois votre exécution de ML automatisé terminée, vous devez cliquer sur « Afficher les détails du modèle » pour accéder à une exécution spécifique. De là, vous cliquez sur l’onglet « Explications » pour voir le tableau de bord de visualisation des explications. 

[![Architecture d’interprétabilité de Machine Learning](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon dont les explications des modèles et l’importance des fonctionnalités peuvent être activés dans d’autres domaines du kit de développement logiciel (SDK) en dehors de l’apprentissage automatisé, consultez l’article [concept](how-to-machine-learning-interpretability.md) sur l’interprétabilité.