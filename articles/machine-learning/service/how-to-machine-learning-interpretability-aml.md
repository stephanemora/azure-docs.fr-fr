---
title: Interprétabilité des modèles pour les exécutions locales et distantes
titleSuffix: Azure Machine Learning
description: Découvrez comment expliquer pourquoi votre modèle élabore des prédictions à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning. Il peut être utilisé pendant l’entraînement et l’inférence pour comprendre comment un modèle détermine l’importance des caractéristiques et effectue des prédictions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511042"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interprétabilité des modèles pour les exécutions locales et distantes

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à expliquer pourquoi votre modèle a élaboré ses prédictions avec le package d’interprétabilité du SDK Python Azure Machine Learning. Vous allez apprendre à effectuer les tâches suivantes :

* Interpréter des modèles Machine Learning entraînés à la fois localement et sur des ressources de calcul distantes
* Stocker des explications locales et globales sur l’historique des exécutions Azure
* Afficher des visualisations d’interprétabilité dans [Azure Machine Learning Studio](https://ml.azure.com)
* Déployer un explicatif de scoring avec votre modèle

Pour en savoir plus sur l’interprétabilité des modèles, consultez l’[article de présentation du concept](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Interprétabilité locale

L’exemple suivant montre comment utiliser le package interpret localement sans contacter les services Azure. Exécutez `pip install azureml-interpret` pour obtenir le package d’interprétabilité.

1. Entraînez un exemple de modèle dans un notebook Jupyter local.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Appelez l’explicatif localement : Pour initialiser un objet d’explicatif, vous devez transmettre votre modèle et des données de formation au constructeur de l’explicatif. Vous pouvez également transmettre des noms de fonctionnalités et des noms de classes de sortie (en cas de classification) qui seront utilisés pour que vos descriptions et visualisations soient plus explicites encore. Voici comment instancier un objet d’explicatif localement à l’aide de `TabularExplainer`, de `MimicExplainer` et de `PFIExplainer`. `TabularExplainer` appelle un des trois explicatifs SHAP sous-jacents (`TreeExplainer`, `DeepExplainer` ou `KernelExplainer`), et sélectionne automatiquement le plus approprié à votre cas d’usage. Vous pouvez toutefois appeler directement chacun de ses trois explicatifs sous-jacents.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   or

    ```python
    from interpret.ext.blackbox import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Valeurs d’importance des caractéristiques générale (globale)

Obtenez les valeurs d’importance de fonctionnalité globale.
    
```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Valeurs d’importance des caractéristiques au niveau de l’instance (locale)

Obtenez les valeurs d’importance de fonctionnalité locale : utilisez les appels de fonction suivants pour expliquer une instance individuelle ou un groupe d’instances. Notez que PFIExplainer ne prend pas en charge les explications locales.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interprétabilité des exécutions distantes

Cet exemple montre comment utiliser la classe `ExplanationClient` pour l’activation de l’interprétabilité des modèles pour les exécutions distantes. Le concept est similaire à celui présenté dans la section précédente, mais vous utilisez `ExplanationClient` dans l’exécution à distance pour charger le contexte d’interprétabilité. Vous pouvez alors télécharger le contexte ultérieurement dans un environnement local. Utilisez `pip install azureml-contrib-interpret` pour obtenir le package nécessaire.

1. Créez un script d’entraînement dans un notebook Jupyter local (par exemple, train_explain.py).

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Suivez les instructions relatives à la [configuration de cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md#amlcompute) pour savoir comment configurer une capacité de calcul Azure Machine Learning comme votre cible de calcul et soumettre votre exécution d’entraînement. Vous pouvez également consulter les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation).

1. Téléchargez l’explication dans votre Jupyter Notebook local.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Transformations de fonctionnalité brute

Si vous le souhaitez, vous pouvez passer votre pipeline de transformation de caractéristique à l’explicatif (dans train_explain.py) pour recevoir des explications en termes de caractéristiques brutes avant la transformation (plutôt que des caractéristiques traitées). Si vous l’ignorez, l’explicatif fournit des explications en termes de fonctionnalités conçues.

Le format de transformations prises en charge est le même que celui décrit dans [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). En règle générale, toutes les transformations sont prises en charge tant qu’elles s’appliquent à une colonne unique et qu’elles constituent ainsi clairement des transformations un à plusieurs. 

Expliquez des caractéristiques brutes à l’aide de `sklearn.compose.ColumnTransformer` ou d’une liste de tuples de transformateur adaptés. Le code ci-dessous utilise `sklearn.compose.ColumnTransformer`. 


```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Si vous voulez exécuter l’exemple avec la liste de tuples de transformateur adaptés, utilisez le code suivant.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Visualisations

Une fois que vous avez téléchargé les explications dans votre notebook Jupyter local, vous pouvez utiliser le tableau de bord de visualisation pour comprendre et interpréter votre modèle.

### <a name="global-visualizations"></a>Visualisations globales

Les tracés suivants fournissent une vue globale du modèle formé, ainsi que ses prédictions et explications.

|Tracé|Description|
|----|-----------|
|Exploration des données| Vue d’ensemble du jeu de données avec des valeurs de prédiction.|
|Importance globale|Montre les K premières fonctionnalités importantes (configurables) globalement. Ce graphique est utile pour comprendre le comportement global du modèle sous-jacent.|
|Exploration d’explication|Montre comment une fonctionnalité est responsable d’une modification dans les valeurs de prédiction du modèle (ou de la probabilité de valeurs de prédiction). Elle montre également comment deux caractéristiques interagissent pour avoir un impact sur les prédictions.|
|Importance du résumé| Utilise des valeurs d’importance de fonctionnalité locale signées sur tous les points de données pour montrer la distribution de l’impact de chaque fonctionnalité sur la valeur de prédiction.|

[![Tableau de bord de visualisation - Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualisations locales

Cliquez sur n’importe quel point de données à tout moment dans les tracés précédents pour charger le tracé d’importance des caractéristiques locale de ce point de données.

|Tracé|Description|
|----|-----------|
|Importance locale|Montre les K premières fonctionnalités importantes (configurables) globalement. Ce graphique est utile pour comprendre le comportement local du modèle sous-jacent sur un point de données spécifique.|
|Exploration de perturbation|Vous permet de modifier des valeurs de fonctionnalités du point de données sélectionné et observer comment ces modifications affectent la valeur de prédiction.|
|Attente conditionnelle individuelle (ICE)| Vous permet de modifier une valeur de fonctionnalité entre une valeur minimale et une valeur maximale pour voir comment la prédiction du point de données change lorsqu’une fonctionnalité change.|

[![Tableau de bord de visualisation - Importance de fonctionnalité locale](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Tableau de bord de visualisation - Perturbation de fonctionnalité](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Tableau de bord de visualisation - Tracés ICE](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Notez que vous des extensions de widget du tableau de bord de visualisation doivent être activées avant de démarrer le noyau Jupyter.

* Notebooks Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Labs Jupyter

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Pour charger le tableau de bord de visualisation, utilisez le code suivant.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisation dans Azure Machine Learning Studio

En suivant les étapes décrites dans la section relative à l’[interprétabilité à distance](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs), vous pouvez vérifier le tableau de bord de visualisation dans [Azure Machine Learning Studio](https://ml.azure.com). Le tableau de bord affiché dans Azure Machine Learning Studio est une version plus simple du tableau de bord de visualisation expliqué ci-dessus. Il ne prend en charge que les deux onglets suivants.

|Tracé|Description|
|----|-----------|
|Importance globale|Montre les K premières fonctionnalités importantes (configurables) globalement. Ce graphique est utile pour comprendre le comportement global du modèle sous-jacent.|
|Importance du résumé| Utilise des valeurs d’importance de fonctionnalité locale signées sur tous les points de données pour montrer la distribution de l’impact de chaque fonctionnalité sur la valeur de prédiction.|

Si les explications globales et locales sont toutes les deux disponibles, les deux onglets sont renseignés avec des données. Si seule l’explication globale est disponible, le deuxième onglet est désactivé.

Pour accéder au tableau de bord de visualisation dans Azure Machine Learning Studio, vous pouvez suivre l’un des parcours suivants :

1. Onglet Expériences (préversion) : Si vous cliquez sur l’onglet « Expériences », vous voyez une liste d’expériences que vous avez exécutées sur le service Azure Machine Learning. Dans cette liste, vous pouvez sélectionner une expérience particulière à rediriger vers une page contenant toutes les exécutions sous le nom de l’expérience sélectionnée. Si vous cliquez sur chaque exécution et sur son onglet « Explications », vous voyez le tableau de bord de visualisation des explications.


[![Tableau de bord de visualisation - Importance de fonctionnalité locale](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Onglet Modèles : Si vous avez inscrit votre modèle d’origine à l’aide des étapes décrites dans [Déployer des modèles avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), votre modèle s’affiche dans la liste de l’onglet « Modèles ». Si vous cliquez sur chaque modèle et sur son onglet « Explications », vous voyez le tableau de bord de visualisation des explications.

## <a name="interpretability-at-inference-time"></a>Interprétabilité au moment de l’inférence

L’explicatif peut être déployé avec le modèle d’origine et peut être utilisé au moment de l’inférence pour fournir les informations d’explication locale. Nous proposons également des explicatifs de scoring plus légers pour améliorer les performances de l’interprétabilité au moment de l’inférence. Le processus de déploiement d’un explicatif de scoring plus léger est similaire au déploiement d’un modèle et comprend les étapes suivantes :


1. Créer un objet d’explication (par exemple, à l’aide de TabularExplainer) :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Créer un explicatif de scoring à l’aide de l’objet d’explication :

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurez et inscrivez une image qui utilise le modèle d’explicatif de scoring.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. [Facultatif] Récupérer l’explicatif de scoring sur le cloud et tester les explications

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Déployer l’image sur une cible de calcul :

   1. Créer un fichier de scoring (avant cette étape, suivez les étapes de [Déployer des modèles avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) pour inscrire votre modèle de prédiction d’origine)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Définissez la configuration de déploiement (cette configuration dépend des exigences de votre modèle. L’exemple suivant définit une configuration qui utilise un seul cœur d’UC et 1 Go de mémoire)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Créer un fichier avec des dépendances d’environnement

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Créer un fichier dockerfile personnalisé avec g++ installé

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Déployer l’image créée (durée estimée : 5 minutes)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. test du déploiement

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Nettoyer : Pour supprimer un service web déployé, utilisez `service.delete()`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’interprétabilité des modèles, consultez l’[article conceptuel](how-to-machine-learning-interpretability.md).
