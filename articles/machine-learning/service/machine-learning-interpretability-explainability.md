---
title: Interprétabilité de modèles
titleSuffix: Azure Machine Learning
description: Découvrez comment expliquer pourquoi votre modèle élabore des prédictions à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning. Il peut être utilisé pendant la formation et l’inférence pour comprendre comment un modèle élabore des prédictions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 6b825e61542dabc92baf482ede6c93edc486e059
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002353"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>Interprétabilité de modèles avec Azure Machine Learning

Dans cet article, vous allez apprendre à expliquer pourquoi votre modèle a élaboré ses prédictions avec les différents packages d’interprétabilité du Kit de développement logiciel (SDK) Python Azure Machine Learning.

Grâce aux classes et aux méthodes dans le Kit de développement logiciel (SDK), vous pouvez obtenir les informations suivantes :
+ Valeurs importantes pour les fonctionnalités brutes et conçues
+ Interprétabilité sur des jeux de données réels à grande échelle, pendant la formation et l’inférence.
+ Visualisations interactives pour vous aider dans la découverte de modèles de données et d’explications au moment de la formation

Pendant la phase de formation du cycle de développement, les concepteurs et évaluateurs de modèles peuvent utiliser la sortie d’interprétabilité d’un modèle pour vérifier des hypothèses et consolider la confiance avec les parties prenantes.  Ils utilisent également les informations sur le modèle pour déboguer, valider que le comportement du modèle correspond à leurs objectifs et pour rechercher une alternative.

Dans Machine Learning, les **fonctionnalités** sont les champs de données utilisés pour prédire un point de données cible. Par exemple, pour prédire le risque de crédit, les champs de données d’âge, de taille du compte et d’âge de compte peuvent servir. Dans ce cas, l’âge, la taille du compte et l’âge du compte sont des **fonctionnalités**. L’importance d’une fonctionnalité vous indique dans quelle mesure chaque champ de données a contribué aux prédictions du modèle. Par exemple, l’âge peut être fortement utilisé dans la prédiction tandis que la taille et l’âge du compte n’affectent pas significativement la précision de la prédiction. Ce processus permet aux scientifiques des données d’expliquer les prédictions obtenues de sorte que les parties prenantes aient une visibilité sur les points de données les plus importants dans le modèle.

À l’aide de ces outils, vous pouvez expliquer les modèles Machine Learning **globalement sur toutes les données**, ou **localement sur un point de données spécifique** à l’aide de technologies de pointe, de manière simple et évolutive.

Les classes d’interprétabilité sont accessibles via plusieurs packages de Kit de développement logiciel (SDK). En savoir plus sur l’[installation de packages de Kit de développement logiciel (SDK) pour Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), le package principal, qui contient des fonctionnalités prises en charge par Microsoft.

* `azureml.contrib.explain.model`, fonctionnalités en préversion et expérimentales que vous pouvez essayer.

* `azureml.train.automl.automlexplainer`, package pour l’interprétation des modèles de Machine learning automatisé.

> [!IMPORTANT]
> Le contenu de l’espace de noms `contrib` n’est pas entièrement pris en charge. À mesure que les fonctionnalités expérimentales arriveront à échéance, elles seront progressivement déplacées vers l’espace de noms principal.

## <a name="how-to-interpret-your-model"></a>Comment interpréter votre modèle

Vous pouvez appliquer les classes et méthodes d’interprétabilité pour comprendre le comportement global du modèle ou des prédictions spécifiques. La première est appelée une explication globale, et la dernière une explication locale.

Les méthodes peuvent être également classées selon que la méthode est indépendante ou spécifique du modèle. Certaines méthodes ciblent certains types de modèles. Par exemple, l’explicatif d’arborescence de SHAP s’applique uniquement aux modèles basés sur une arborescence. Certaines méthodes traitent le modèle comme une boîte noire, telles que l’explicatif d’imitation ou l’explicatif de noyau de SHAP. Le package `explain` utilise ces approches différentes en fonction des jeux de données, des types de modèles et des cas d’usage.

La sortie est un ensemble d’informations sur la façon dont un modèle donné élabore sa prédiction, telle que :
* Importance de fonctionnalité relative globale/locale

* Relation de fonctionnalité et prédiction globale/locale

### <a name="explainers"></a>Explicatifs

Il existe deux ensembles d’explicatifs : Explicatifs directs et explicatifs méta dans le Kit de développement logiciel (SDK).

Les __explicatifs directs__ proviennent de bibliothèques intégrées. Le Kit de développement logiciel (SDK) inclut dans un wrapper tous les explicatifs de sorte qu’ils présentent une API et un format de sortie communs. Si l’utilisation de ces explicatifs vous convient plus, vous pouvez les appeler directement au lieu d’utiliser l’API et le format de sortie communs. Voici une liste des explicatifs directs disponibles dans le Kit de développement logiciel (SDK) :

* **Explicatif d’arborescence SHAP** : L’explicatif d’arborescence de SHAP cible l’algorithme d’estimation de valeur SHAP rapide de temps polynomial spécifique des arborescences et ensembles d’arborescences.
* **Explicatif approfondi SHAP** : Selon l’explication proposée par SHAP, l’explicatif approfondi « est un algorithme d’approximation à vitesse élevée de valeurs SHAP dans des modèles de Deep Learning qui s’appuie sur une connexion à DeepLIFT décrite dans le document NIPS de SHAP. Les modèles TensorFlow et Keras utilisant le serveur principal TensorFlow sont pris en charge (une prise en charge préliminaire de PyTorch est également possible) ».
* **Explicatif de noyau SHAP** : L’explicatif de noyau de SHAP utilise une régression linéaire locale spécialement pondérée pour estimer les valeurs SHAP pour n’importe quel modèle.
* **Explicatif d’imitation** : L’explicatif d’imitation repose sur l’idée de modèles de substitution globaux. Un modèle de substitution global est un modèle intrinsèquement interprétable qui est formé pour estimer les prédictions d’un modèle de boîte noire aussi précisément que possible. Un scientifique des données peut interpréter le modèle de substitution pour déduire des conclusions sur le modèle de boîte noire. Vous pouvez utiliser un des modèles interprétables suivants comme modèle de substitution : LightGBM (LinearExplainableModel), régression linéaire (LinearExplainableModel), modèle explicable Stochastic Gradient Descent (SGDExplainableModel) et arbre de décision (DecisionTreeExplainableModel).


* **Explicatif d’importance de fonctionnalité de permutation** : L’importance de fonctionnalité de permutation est une technique utilisée pour expliquer les modèles de classification et de régression qui s’inspirent du [document relatif aux forêts aléatoires de Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (consultez la section 10). À un niveau élevé, il fonctionne en permutant aléatoirement les données d’une fonctionnalité à la fois pour l’ensemble du jeu de données et en calculant dans quelle mesure la métrique de performance utile diminue. Plus la modification est importante, et plus la fonctionnalité l’est également.

* **Explicatif LIME** (`contrib`) : Selon LIME, l’explicatif LIME utilise l’algorithme de pointe d’explications indépendant du modèle interprétable local ou LIME pour Local interpretable model-agnostic explanations, pour créer des modèles de substitution locaux. Contrairement aux modèles de substitution globaux, LIME cible la formation de modèles de substitution locaux pour expliquer des prédictions individuelles.
* **Explicatif de texte HAN** (`contrib`) : L’explicatif de texte HAN utilise un Hierarchical Attention Network pour obtenir des explications sur le modèle à partir de données de texte d’un modèle de texte de boîte noire donné. Nous effectuons l’apprentissage du modèle de substitution HAN sur la base de sorties prédites d’un modèle enseignant donné. Après avoir effectué l’apprentissage global sur le corpus de texte, nous avons ajouté une étape de réglage pour un document spécifique afin d’améliorer la précision des explications. HAN utilise un RNN bidirectionnel avec deux couches d’attention, une sur la phrase et l’autre sur le mot. Une fois que le DNN est formé sur le modèle enseignant et réglé sur un document spécifique, nous pouvons extraire les importances des mots des couches d’attention. Nous avons constaté que HAN est plus précis que LIME OU SHAP pour les données texte, mais également plus coûteux en termes de temps de formation. Nous avons toutefois améliorer le temps de formation en permettant à l’utilisateur d’initialiser le réseau avec des intégrations de mots GloVe, même s’il est toujours lent. Le temps de formation peut être amélioré significativement en exécutant HAN sur une machine virtuelle GPU Azure distante. L’implémentation de HAN est décrite dans le document [« Hierarchical Attention Networks for Document Classification (Yang et al., 2016) »](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


Les __explicatifs méta__ sélectionnent automatiquement un explicatif direct approprié et génèrent les meilleures informations d’explication en fonction du modèle et des jeux de données. Les explicatifs méta utilisent toutes les bibliothèques (SHAP, LIME, Mimic, etc.) que nous avons intégrées ou développées. Voici une liste des explicatifs méta disponibles dans le Kit de développement logiciel (SDK) :

* **Explicatif tabulaire** : Utilisé avec les jeux de données tabulaires.
* **Explicatif de texte** : Utilisé avec les jeux de données de texte.
* **Explicatif d’image** : Utilisé avec les jeux de données d’image.

Outre la sélection globale des explicatifs directs, les explicatifs méta développent des fonctionnalités supplémentaires sur les bibliothèques sous-jacentes et améliorent la vitesse et l’extensibilité par rapport aux explicatifs directs.

`TabularExplainer` utilise actuellement la logique suivante pour appeler le explicatifs SHAP directs :

1. S’il s’agit d’un modèle basé sur une arborescence, appliquez SHAP `TreeExplainer`, sinon
2. S’il s’agit d’un modèle DNN, appliquez SHAP `DeepExplainer`, sinon
3. Traitez-le comme un modèle de boîte noire et appliquez SHAP `KernelExplainer`

L’intelligence intégrée à `TabularExplainer` se complexifie à mesure que d’autres bibliothèques sont intégrées dans le Kit de développement logiciel (SDK), et que nous découvrons les avantages et inconvénients de chaque explicatif.

`TabularExplainer` a également apporté des améliorations significatives de fonctionnalité et de performance par rapport aux explicatifs directs :

* **Récapitulatif du jeu de données d’initialisation**. Dans les cas où la vitesse d’explication est plus importante, nous résumons le jeu de données d’initialisation et générons un petit ensemble d’exemples représentatifs, ce qui accélère l’explication globale et locale.
* **Échantillonnage du jeu de données d’évaluation**. Si l’utilisateur transmet un grand ensemble d’exemples d’évaluation, mais que tous ne doivent pas nécessairement être évalués, le paramètre d’échantillonnage peut être défini sur true pour accélérer l’explication globale.

Le diagramme suivant illustre la structure actuelle d’explicatifs directs et méta.

[![Architecture d’interprétabilité de Machine Learning](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modèles pris en charge

Tous les modèles formés sur des jeux de données au format Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData` ou `scipy.sparse.csr_matrix` sont pris en charge par le package `explain` d’interprétabilité du Kit de développement logiciel (SDK).

Les fonctions d’explication acceptent les modèles et les pipelines en entrée. Si un modèle est fourni, il doit implémenter la fonction de prédiction `predict` ou `predict_proba` conforme à la convention Scikit. Si un pipeline (nom du script de pipeline) est fourni, la fonction d’explication suppose que le script de pipeline en cours d’exécution retourne une prédiction. Nous prenons en charge les modèles formés via les infrastructures de Deep Learning PyTorch, TensorFlow et Keras.

### <a name="local-and-remote-compute-target"></a>Cible de calcul locale et distante

Le package `explain` est conçu pour fonctionner avec les cibles de calcul locales et distantes. Si vous les exécutez localement, les fonctions du Kit de développement logiciel (SDK) ne contactent aucun service Azure. Vous pouvez exécuter l’explication à distance sur la capacité de calcul Azure Machine Learning et consigner les informations d’explication dans des services d’historique d’exécutions d’Azure Machine Learning. Lorsque ces informations sont consignées, les rapports et visualisations de l’explication sont disponibles sur le portail d’espace de travail Azure Machine Learning pour une analyse utilisateur.

## <a name="interpretability-in-training"></a>Interprétabilité pendant la formation

### <a name="train-and-explain-locally"></a>Former et expliquer localement

1. Formez votre modèle dans un Jupyter Notebook local.

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

2. Appelez l’explicatif : Pour initialiser un objet d’explicatif, vous devez transmettre votre modèle et des données de formation au constructeur de l’explicatif. Vous pouvez également transmettre des noms de fonctionnalités et des noms de classes de sortie (en cas de classification) qui seront utilisés pour que vos descriptions et visualisations soient plus explicites encore. Voici comment instancier un objet d’explicatif en utilisant [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) et [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) localement. `TabularExplainer` appelle un des trois explicatifs SHAP sous-jacents (`TreeExplainer`, `DeepExplainer` ou `KernelExplainer`), et sélectionne automatiquement le plus approprié à votre cas d’usage. Vous pouvez toutefois appeler directement chacun de ses trois explicatifs sous-jacents.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    or

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

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
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Obtenez les valeurs d’importance de fonctionnalité globale.

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

4. Obtenez les valeurs d’importance de fonctionnalité locale : utilisez les appels de fonction suivants pour expliquer une instance individuelle ou un groupe d’instances. Notez que PFIExplainer ne prend pas en charge les explications locales.

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

Bien que vous puissiez effectuer l’entraînement sur les différentes cibles de calcul prises en charge par Azure Machine Learning, l’exemple dans cette section montre comment procéder à l’aide d’une cible Capacité de calcul Azure Machine Learning.

1. Créez un script de formation dans un Jupyter Notebook local (par exemple, run_explainer.py).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

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

2. Suivez les instructions de [Configurer des cibles de calcul pour la formation des modèles](how-to-set-up-training-targets.md#amlcompute) pour en savoir plus sur la configuration d’une capacité de calcul Azure Machine Learning comme cible de calcul et la soumission de votre exécution de formation.

3. Téléchargez l’explication dans votre Jupyter Notebook local.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
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

## <a name="visualizations"></a>Visualisations

Utilisez le tableau de bord de visualisation pour comprendre et interpréter votre modèle :

### <a name="global-visualizations"></a>Visualisations globales

Les tracés suivants fournissent une vue globale du modèle formé, ainsi que ses prédictions et explications.

|Tracé|Description|
|----|-----------|
|Exploration des données| Vue d’ensemble du jeu de données avec des valeurs de prédiction.|
|Importance globale|Montre les K premières fonctionnalités importantes (configurables) globalement. Ce graphique est utile pour comprendre le comportement global du modèle sous-jacent.|
|Exploration d’explication|Montre comment une fonctionnalité est responsable d’une modification dans les valeurs de prédiction du modèle (ou de la probabilité de valeurs de prédiction). |
|Résumé| Utilise des valeurs d’importance de fonctionnalité locale signées sur tous les points de données pour montrer la distribution de l’impact de chaque fonctionnalité sur la valeur de prédiction.|

[![Tableau de bord de visualisation - Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualisations locales

Vous pouvez cliquer sur n’importe quel point de données à tout moment des tracés précédents pour charger le tracé d’importance de fonctionnalité locale de ce point de données.

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
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Labs Jupyter

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Pour charger le tableau de bord de visualisation, utilisez le code suivant :

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Transformations de fonctionnalité brute

Si vous le souhaitez, vous pouvez transmettre votre pipeline de transformation de fonctionnalité à l’explicatif pour recevoir des explications en termes de fonctionnalités brutes avant la transformation (plutôt que des fonctionnalités conçues). Si vous l’ignorez, l’explicatif fournit des explications en termes de fonctionnalités conçues.

Le format de transformations prises en charge est le même que celui décrit dans [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). En règle générale, toutes les transformations sont prises en charge tant qu’elles s’appliquent à une colonne unique et qu’elles constituent ainsi clairement des transformations un à plusieurs. 

Nous pouvons expliquer des fonctionnalités brutes à l’aide de `sklearn.compose.ColumnTransformer` ou d’une liste de tuples de transformateur adaptés. La cellule ci-dessous utilise `sklearn.compose.ColumnTransformer`. 

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

Si vous souhaitez exécuter l’exemple avec la liste de tuples de transformateur adaptés, utilisez le code suivant : 
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

## <a name="interpretability-at-inferencing-time"></a>Interprétabilité au moment de l’inférence

L’explicatif peut être déployé avec le modèle d’origine et peut être utilisé au moment du scoring pour fournir les informations d’explication locale. Nous proposons également des explicatifs de scoring plus légers pour que l’interprétabilité au moment de l’inférence soit plus performante. Le processus de déploiement d’un explicatif de scoring plus léger est similaire au déploiement d’un modèle et comprend les étapes suivantes :




1. Créer un objet d’explication (par exemple, à l’aide de TabularExplainer) :

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Créer un explicatif de scoring à l’aide de l’objet d’explication :

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

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
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

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

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

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

Pour obtenir une collection de notebooks Jupyter qui montrent les instructions ci-dessus, consultez les [exemples de notebooks d’interprétabilité d’Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
