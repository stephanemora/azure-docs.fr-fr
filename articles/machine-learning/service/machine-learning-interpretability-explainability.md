---
title: Interprétabilité de modèles
titleSuffix: Azure Machine Learning service
description: Découvrez comment expliquer pourquoi votre modèle élabore des prédictions à l’aide du Kit de développement logiciel Azure Machine Learning. Il peut être utilisé au cours de formation et l’inférence pour comprendre la façon dont votre modèle élabore des prédictions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 05/30/2019
ms.openlocfilehash: 94309a019800b560cf6731d84cea324932e3f357
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398548"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Problème de modèle avec le service Azure Machine Learning

Dans cet article, vous allez apprendre à expliquer pourquoi votre modèle a fait les prédictions c’était le cas avec les différents packages de ce problème du SDK Python Azure Machine Learning.

À l’aide des classes et des méthodes dans le Kit de développement, vous pouvez obtenir :
+ Valeurs importance pour les fonctions brutes et l’ingénierie des fonctionnalités
+ Problème sur les jeux de données réels à grande échelle, au cours de formation et l’inférence.
+ Pour vous aider dans la découverte de modèles de données et des explications au moment de l’apprentissage des visualisations interactives

Pendant la phase de formation du cycle de développement, les concepteurs de modèles et les évaluateurs peuvent utiliser sortie de ce problème d’un modèle pour vérifier des hypothèses et consolider la confiance avec les parties prenantes.  Ils utilisent également les informations sur le modèle pour le débogage, le comportement du modèle de validation correspond à leurs objectifs et pour rechercher les biais.

Dans l’apprentissage, **fonctionnalités** sont les champs de données utilisés pour prédire un point de données cible. Par exemple, pour prédire le risque de crédit, les champs de données pour l’âge, taille des comptes et l’âge de compte peuvent servir. Dans ce cas, âge, taille des comptes et l’âge de compte sont **fonctionnalités**. Importance de fonctionnalité indique la façon dont chaque champ de données affectées les prédictions du modèle. Par exemple, âge sont fortement utilisables dans la prédiction tandis que l’âge et la taille de compte n’affectent considérablement la précision de prédiction. Ce processus permet aux chercheurs de données expliquer des prédictions qui en résulte, que ceux-ci aient une visibilité sur les points de données les plus importants dans le modèle.

À l’aide de ces outils, vous pouvez expliquer les modèles d’apprentissage automatique **globalement sur toutes les données**, ou **localement sur des points de données spécifique** à l’aide de technologies de pointe de manière facile à utiliser et évolutive.

Les classes de ce problème sont accessibles via plusieurs packages de kit de développement logiciel. Découvrez comment [installer des packages de kit de développement logiciel pour Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), le package principal, qui contient des fonctionnalités prises en charge par Microsoft.

* `azureml.contrib.explain.model`, version préliminaire et les fonctionnalités expérimentales que vous pouvez essayer.

* `azureml.train.automl.automlexplainer` package pour l’interprétation des modèles d’apprentissage automatique.

> [!IMPORTANT]
> Contenu dans le `contrib` espace de noms n’est pas entièrement pris en charge. Les fonctionnalités expérimentales devenant matures, elles seront déplacés progressivement l’espace de noms principal.

## <a name="how-to-interpret-your-model"></a>Comment interpréter votre modèle

Vous pouvez appliquer des classes de ce problème et des méthodes pour comprendre le comportement du modèle global ou des prédictions spécifiques. La première est appelée une explication globale, et ce dernier est appelé une explication locale.

Les méthodes peuvent être également classés selon que la méthode est indépendante du modèle ou un modèle spécifique. Certaines méthodes ciblent certains types de modèles. Par exemple, explicatif d’arborescence d’es de forme s’applique uniquement aux modèles basés sur l’arborescence. Certaines méthodes traitent le modèle comme une boîte noire, telles qu’explicatif imiter ou explicatif de noyau de photos. Le `explain` package tire parti de ces approches différentes selon les jeux de données, les types de modèles et les cas d’utilisation.

La sortie est un ensemble d’informations sur la façon dont un modèle donné effectue sa prédiction, tel que :
* Importance de fonctionnalité relative global/local

* Relation de fonctionnalité et de prédiction global/local

### <a name="explainers"></a>Explainers

Il existe deux ensembles d’explainers : Explainers directs et Explainers Meta dans le Kit de développement.

__Diriger explainers__ proviennent de bibliothèques intégrés. Le Kit de développement logiciel encapsule tous les explainers afin qu’ils exposent une API commune et le format de sortie. Si vous êtes plus à l’aise directement à l’aide de ces explainers, vous pouvez les appeler directement au lieu d’utiliser l’API commune et le format de sortie. Voici une liste des explainers directs disponibles dans le SDK :

* **Arborescence explicatif**: Explicatif d’arborescence de photos, qui se concentre sur le temps polynomiale rapide es de forme valeur algorithme d’estimation spécifique aux arbres et ensembles d’arbres de.
* **Explicatif approfondie**: Selon l’explication de photos, explicatif approfondie « est un algorithme d’approximation à haut débit pour les valeurs de photos dans les modèles d’apprentissage profond qui s’appuie sur une connexion avec DeepLIFT décrit dans le document de photos NIPS. Modèles de TensorFlow et de Keras à l’aide du serveur principal TensorFlow sont pris en charge (il existe également une prise en charge préliminaire pour PyTorch) ».
* **Noyau explicatif**: Explicatif de noyau de photos utilise une régression linéaire locale spécialement pondérée pour estimer les valeurs de photos pour n’importe quel modèle.
* **Imiter explicatif**: Explicatif imiter repose sur l’idée des modèles de substitution global. Un modèle de substitution global est un modèle intrinsèquement interprétable est formé pour estimer les prédictions d’un modèle de boîte noire aussi précisément que possible. Scientifique des données peuvent interpréter le modèle de substitution pour conclusions non fondées sur le modèle de boîte noire.
* **Citron vert explicatif** (`contrib`) : Selon citron vert, citron vert explicatif utilise l’algorithme de pointe Local interprétable indépendant du modèle des explications (citron vert) pour créer des modèles de substitution local. Contrairement aux modèles de substitution global citron vert se concentre sur la formation des modèles de substitution local pour expliquer les prédictions individuelles.
* **HENRI texte explicatif** (`contrib`) : HENRI texte explicatif utilise un réseau d’Attention hiérarchique pour obtenir des explications sur le modèle à partir des données de texte pour un modèle de texte donné boîte noire. Nous formons le modèle de substitution HAN sur des sorties prédite d’un modèle donné enseignant. Après une formation de manière globale sur le corpus de texte, nous avons ajouté une étape de réglage pour un document spécifique afin d’améliorer la précision des explications. HENRI utilise un RNN bidirectionnel avec deux couches d’attention, attention phrase et word. Une fois que le réseau de neurones profond est formé sur le modèle de l’enseignant et affiné sur un document spécifique, nous pouvons extraire les importances word les couches de votre attention. Nous avons trouvé HAN pour être plus précis que citron vert ou es de forme pour les données texte mais plus coûteux en termes d’ainsi le temps d’apprentissage. Toutefois, nous avons apporté des améliorations apportées à la durée d’apprentissage en donnant à l’utilisateur l’option pour initialiser le réseau avec GANT incorporations, bien qu’il soit toujours lent. La durée d’apprentissage peut améliorer considérablement le HAN en cours d’exécution sur une machine virtuelle de GPU Azure distante. L’implémentation de HENRI est décrite dans « Hiérarchique Attention réseaux pour la Classification de Document (Yang et al., 2016) » ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Explainers de META__ automatiquement de sélectionner un explicatif direct approprié et de générer les meilleures informations explication selon le modèle donné et les jeux de données. Les explainers meta tirer parti de toutes les bibliothèques (photos, citron vert, simulent, etc.) que nous avons intégré ou développé. Les explainers meta disponibles dans le Kit de développement logiciel sont les suivants :

* **Explicatif tabulaire**: Utilisé avec les jeux de données tabulaires.
* **Texte explicatif**: Utilisé avec les jeux de données de texte.

En outre à méta sélection des explainers directs, explainers de meta développement les bibliothèques sous-jacentes des fonctionnalités supplémentaires et améliorent la vitesse et l’évolutivité sur les explainers directs.

Actuellement `TabularExplainer` utilise la logique suivante pour appeler le Explainers directe :

1. Dans le cas d’un modèle basé sur l’arborescence, appliquer `TreeExplainer`; sinon,
2. Dans le cas d’un modèle de réseau de neurones profond, appliquer `DeepExplainer`; sinon,
3. Traiter comme un modèle de boîte noire et appliquer `KernelExplainer`

L’intelligence intégrée `TabularExplainer` deviendront plus complexes comme les autres bibliothèques sont intégrés dans le Kit de développement et de nous en savoir plus sur les avantages et inconvénients de chaque explicatif.

`TabularExplainer` a également fait des améliorations significatives de fonctionnalité et de performances sur le Explainers directe :

* **Synthèse du jeu de données d’initialisation**. Dans les cas où la vitesse d’explication est plus importante, nous résumer le jeu de données d’initialisation et générer un petit ensemble d’exemples représentatifs, ce qui accélère explication globale et locale.
* **Échantillonnage du jeu de données d’évaluation**. Si l’utilisateur passe dans un grand ensemble d’exemples d’évaluation, mais n’a pas besoin réellement d'entre eux doit être évaluée, le paramètre d’échantillonnage peut être défini sur true pour accélérer l’explication globale.

Le diagramme suivant montre la relation entre les deux ensembles de direct et explainers de métadonnées.

[![Architecture de ce problème d’apprentissage](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modèles pris en charge

Tous les modèles sont formés sur les jeux de données dans Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, ou `scipy.sparse.csr_matrix` format sont pris en charge par l’interopérabilité `explain` package du Kit de développement.

Les fonctions d’explication acceptent à la fois des modèles et des pipelines en tant qu’entrée. Si un modèle est fourni, le modèle doit implémenter la fonction de prédiction `predict` ou `predict_proba` qui est conforme à la convention de Scikit. Si un pipeline (nom du script de pipeline) est fourni, la fonction explication suppose que le script de pipeline en cours d’exécution retourne une prédiction.

### <a name="local-and-remote-compute-target"></a>Cible de calcul locaux et distants

Le `explain` package est conçu pour fonctionner avec les deux cibles de calcul locaux et distants. Si vous exécutez localement, les fonctions du SDK contactera pas tous les services Azure. Vous pouvez exécuter à distance des explications sur Azure Machine Learning Compute et connectez-vous l’information sur l’explication des Services Azure Machine Learning exécuter l’historique. Une fois que ces informations sont enregistrées, les rapports et des visualisations à partir de l’explication sont disponibles sur le portail d’espace de travail Azure Machine Learning pour l’analyse de l’utilisateur.

## <a name="interpretability-in-training"></a>Problème dans la formation

### <a name="train-and-explain-locally"></a>Former et expliquez localement

1. Formation d’un modèle dans un bloc-notes Jupyter local.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Appelez l’explicatif : Pour initialiser un objet d’explicatif, vous devez transmettre votre modèle et des données d’apprentissage au constructeur de l’explicatif. Vous pouvez également passer dans les noms de fonction et de sortie noms de classes (si classification) qui seront utilisés pour rendre vos descriptions et les visualisations plus informatif. Voici comment instancier un objet d’explicatif en utilisant [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) et [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) localement. `TabularExplainer` Parmi les trois explainers sous appelle (`TreeExplainer`, `DeepExplainer`, ou `KernelExplainer`) et est automatiquement sélectionnons le plus approprié pour votre cas d’usage. Toutefois, vous pouvez appeler chacune de ses trois explainers sous-jacente directement.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

    or

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Obtenir la fonctionnalité globale de valeurs d’importance.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Valeurs d’importance de fonctionnalité locale : utiliser les appels de fonction suivants pour expliquer une instance individuelle ou un groupe d’instances.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    or

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Former et expliquer à distance

Pendant que vous pouvez effectuer l’apprentissage sur les différentes cibles de calcul pris en charge par le service Azure Machine Learning, l’exemple dans cette section montre comment procéder à l’aide d’une cible de calcul Azure Machine Learning.

1. Créer un script de formation dans un bloc-notes Jupyter local (par exemple, run_explainer.py).

    ```python
    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # Train your model here

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Suivez les instructions de [configurer des cibles de calcul pour l’apprentissage du modèle](how-to-set-up-training-targets.md#amlcompute) pour en savoir plus sur la façon de configurer un Azure Machine Learning Compute comme cible de calcul et soumettre votre série de formation.

3. Téléchargez l’explication dans votre bloc-notes Jupyter local.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Visualisations

Utilisez le tableau de bord de visualisation pour comprendre et interpréter votre modèle :

### <a name="global-visualizations"></a>Visualisations globales

Les tracés suivantes fournissent une vue globale du modèle formé, ainsi que ses prédictions et d’explications.

|Plot|Description|
|----|-----------|
|Exploration des données| Vue d’ensemble du jeu de données ainsi que les valeurs de prédiction.|
|Importance globale|Montre les fonctionnalités importantes K (configurable K) supérieures dans le monde entier. Ce graphique est utile pour comprendre le comportement global du modèle sous-jacent.|
|Exploration d’explication|Montre comment une fonctionnalité est responsable de la modification dans les valeurs de prédiction du modèle (ou la probabilité de valeurs de prédiction). |
|Résumé| Utilise un valeurs importance de fonctionnalité local signé sur tous les points de données pour afficher la distribution de l’impact sur la valeur de prédiction de chaque fonctionnalité.|

[![Visualisation du tableau de bord Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualisations locales

Vous pouvez cliquer sur n’importe quel point de données individuelles à tout moment des tracés précédents pour charger le tracé d’importance de fonctionnalité local du point de données donné.

|Plot|Description|
|----|-----------|
|Importance local|Montre les fonctionnalités importantes K (configurable K) supérieures dans le monde entier. Ce graphique est utile pour comprendre le comportement local du modèle sous-jacent sur un point de données spécifique.|

[![Visualisation du tableau de bord Local](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

Pour charger le tableau de bord de visualisation, utilisez le code suivant :

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Transformations de caractéristiques brutes

Si vous le souhaitez, vous pouvez passer votre pipeline de transformation de fonctionnalité à l’explication de recevoir des explications en termes de fonctionnalités brutes avant la transformation (plutôt que des caractéristiques conçues). Si vous ignorez cette opération, l’explicatif fournit des explications en termes de fonctionnalités conçues.

Le format de transformations prises en charge est le même que celui décrit dans [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). En règle générale, toutes les transformations sont pris en charge tant qu’ils opèrent sur une colonne unique et sont par conséquent clairement un à plusieurs.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>Problème dans l’inférence

L’explicatif peut être déployé en même temps que le modèle d’origine et peut être utilisé à l’heure de calcul de score pour fournir les informations d’explication local. Le processus de déploiement d’une explication de la notation est similaire au déploiement d’un modèle et comprend les étapes suivantes :

1. Créez un objet explication :

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ```

1. Créer un calcul de score explicatif à l’aide de l’objet explication :

   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ```

1. Configurer et enregistrer une image qui utilise le modèle d’explicatif notation.

   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ```

1. [Facultatif] Récupérer l’explication de la notation de cloud et de tester les explications

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Déployer l’image sur une cible de calcul :

   1. Créer un fichier de score (avant cette étape, suivez les étapes de [déployer des modèles avec le service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) pour enregistrer votre modèle de prévision d’origine)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Définir la configuration de déploiement (cette configuration dépend de la configuration requise de votre modèle. L’exemple suivant définit une configuration qui utilise un seul cœur de processeur et 1 Go de mémoire)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "breastcancer",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for breast cancer data')
        ```

   1. Créez un fichier avec des dépendances de l’environnement

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"],
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Créer un fichier dockerfile personnalisé avec g ++ installé

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Déployer l’image créée (durée estimée : 5 minutes)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
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

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Nettoyage : Pour supprimer un service web déployé, utilisez `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>Problème dans ML automatisé

Apprentissage automatique contient des packages permettant d’interpréter l’importance de fonctionnalité dans les modèles formés en automatique. En outre, les scénarios de classification permettent de récupérer l’importance de fonctionnalité au niveau de la classe. Il existe deux méthodes pour activer ce comportement dans l’apprentissage automatique :

* Pour activer l’importance de fonctionnalité pour un modèle formé ensemble, utilisez le [ `explain_model()` ](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) (fonction).

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Pour activer l’importance de fonctionnalité pour chaque exécution avant la formation, définissez le `model_explainability` paramètre `True` dans le `AutoMLConfig` objet, ainsi que de fournir des données de validation. Utilisez ensuite le [ `retrieve_model_explanation()` ](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) (fonction).

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

Pour plus d’informations, consultez le [procédures](how-to-configure-auto-train.md#explain-the-model-interpretability) sur l’activation des fonctionnalités de ce problème dans l’apprentissage automatique.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une collection de bloc-notes Jupyter qui montrent les instructions ci-dessus, consultez le [des exemples de bloc-notes Azure Machine Learning est-il](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
