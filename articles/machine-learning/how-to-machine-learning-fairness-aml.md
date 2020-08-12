---
title: Évaluer l’impartialité des modèles Machine Learning dans Python (version préliminaire)
titleSuffix: Azure Machine Learning
description: Découvrez comment évaluer l’impartialité de vos modèles dans Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 107b1103b5662decd8da0937ac84e0e8bab3f8eb
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760768"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Utiliser Azure Machine Learning avec le package open source Fairlearn pour évaluer l’impartialité des modèles Machine Learning (version préliminaire)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ce guide pratique explique comment utiliser le package Python open source [Fairlearn](https://fairlearn.github.io/) avec Azure Machine Learning pour accomplir les tâches suivantes :

* Évaluer l’impartialité des prédictions de votre modèle. Pour en savoir plus sur l’impartialité de l’apprentissage automatique, consultez l’article [Impartialité dans les modèles d’apprentissage automatique](concept-fairness-ml.md).
* Charger, répertorier et télécharger des informations d’évaluation de l’impartialité vers/à partir de Azure Machine Learning Studio.
* Afficher un tableau de bord d’évaluation de l’impartialité dans Azure Machine Learning Studio pour interagir avec les informations d’impartialité de votre modèle.

>[!NOTE]
> L’évaluation de l’impartialité n’est pas un exercice purement technique. **Ce package peut vous aider à évaluer l’impartialité d’un modèle Machine Learning, mais vous seul pouvez le configurer et prendre des décisions quant à la façon dont il fonctionne.**  Bien que ce package aide à identifier des métriques quantitatives pour évaluer l’impartialité, les développeurs de modèles d’apprentissage automatique doivent également effectuer une analyse qualitative pour évaluer l’impartialité de leurs propres modèles.

## <a name="azure-machine-learning-fairness-sdk"></a>Kit de développement logiciel (SDK) pour l’impartialité d’Azure Machine Learning 

Le Kit de développement logiciel (SDK) pour l’impartialité d’Azure Machine Learning, `azureml-contrib-fairness`, intègre le package Python open source, [Fairlearn](http://fairlearn.github.io), dans Azure Machine Learning. Pour en savoir plus sur l’intégration de Fairlearn dans Azure Machine Learning, consultez ces [exemples de bloc-notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Pour plus d’informations sur Fairlearn, consultez l’[exemple de guide](https://fairlearn.github.io/auto_examples/notebooks/index.html) et des [exemples de bloc-notes](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Utilisez les commandes suivantes pour installer les packages `azureml-contrib-fairness` et `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Charger les informations d’impartialité pour un modèle unique

L’exemple suivant montre comment utiliser le package d’impartialité pour télécharger des informations sur l’impartialité du modèle dans Azure Machine Learning et afficher le tableau de bord d’évaluation de l’impartialité dans Azure Machine Learning Studio.

1. Effectuez l’apprentissage d’un exemple de modèle dans un bloc-notes Jupyter. 

    Pour le jeu de données, nous utilisons le jeu de données bien connu Adult Census que nous chargeons à l’aide de `shap` (pour des raisons pratiques). Pour les besoins de cet exemple, nous traitons ce jeu de données comme un problème de décision de prêt et supposons que l’étiquette indique si chaque individu a remboursé un prêt dans le passé. Nous allons utiliser ces données pour faire l’apprentissage d’un modèle capable de prédire si des individus a priori inconnus peuvent ou non rembourser un emprunt. L’hypothèse est que les prédictions du modèle permettent de déterminer si un prêt doit être proposé à un individu.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Connectez-vous à Azure Machine Learning et inscrivez votre modèle.
   
    Le tableau de bord d’impartialité peut s’intégrer avec des modèles inscrits ou non inscrits. Inscrivez votre modèle dans Azure Machine Learning en procédant comme suit :
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    ```

3. Précalculez les métriques d’impartialité.

    Créez un dictionnaire de tableau de bord à l’aide du package `metrics` de Fairlearn. La méthode `_create_group_metric_set` utilise des arguments similaires à ceux du constructeur de tableau de bord, à ceci près que les caractéristiques sensibles sont passées en tant que dictionnaire (pour s’assurer que les noms sont disponibles). Nous devons également spécifier le type de prédiction (en l’occurrence, classification binaire) lors de l’appel de cette méthode.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Chargez les métriques d’impartialité précalculées.
    
    À présent, importez le package `azureml.contrib.fairness` pour effectuer le chargement :

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Créez une expérience, une exécution, puis chargez le tableau de bord :
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Vérifiez le tableau de bord d’impartialité d’Azure Machine Learning Studio.

    Si vous suivez les étapes précédentes (chargement des informations d’impartialité générées dans Azure Machine Learning), vous pouvez afficher le tableau de bord d’impartialité dans [Azure Machine Learning Studio](https://ml.azure.com). Ce tableau de bord est le même que le tableau de bord de visualisation fourni dans Fairlearn, ce qui vous permet d’analyser les disparités entre les sous-groupes sensibles de votre fonctionnalité (par exemple, mâle ou femelle).
    Suivez un de ces parcours pour accéder au tableau de bord de visualisation dans Azure Machine Learning Studio :

    * **Volet Expériences (préversion)**
    1. Sélectionnez **Expériences** dans le volet gauche pour afficher la liste des expériences que vous avez exécutées sur Azure Machine Learning.
    1. Sélectionnez une expérience particulière pour afficher toutes les exécutions de cette expérience.
    1. Sélectionnez une exécution, puis l’onglet **Impartialité** pour afficher le tableau de bord de visualisation des explications.


    [![Tableau de bord Impartialité](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Volet Modèles**
    1. Si vous avez inscrit votre modèle d’origine en suivant les étapes précédentes, vous pouvez sélectionner **Modèles** dans le volet gauche pour l’afficher.
    1. Sélectionnez un modèle, puis l’onglet **Impartialité** pour afficher le tableau de bord de visualisation des explications.

    Pour en savoir plus sur le tableau de bord de visualisation et son contenu, consultez le [Guide de l’utilisateur](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard) de Fairlearn.

## <a name="upload-fairness-insights-for-multiple-models"></a>Charger des informations d’impartialité pour plusieurs modèles

Si vous souhaitez comparer plusieurs modèles et voir comment leurs évaluations d’impartialité diffèrent, vous pouvez passer plusieurs modèles au tableau de bord de visualisation et examiner leurs compromis en matière de performances.

1. Effectuez l’apprentissage de votre modèles.
    
    En plus du modèle de régression logistique précédent, nous créons maintenant un deuxième classifieur, basé sur un estimateur de machine à vecteurs de support, puis chargeons un dictionnaire de tableau de bord d’impartialité à l’aide du package `metrics` de Fairlearn. Notez qu’ici nous ignorons les étapes de chargement et de prétraitement des données, puis passez directement à l’étape de formation du modèle.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Inscrivez vos modèles.

    Ensuite, inscrivez les deux modèles auprès d’Azure Machine Learning. Pour des raisons pratiques, dans les appels de méthode suivants, stockez les résultats dans un dictionnaire qui mappe l’`id` du modèle inscrit (une chaîne au format `name:version`) au modèle de prédiction :

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Chargez le tableau de bord Fairlearn localement.

    Avant de charger les informations d’impartialité dans Azure Machine Learning, vous pouvez examiner ces prédictions dans un tableau de bord Fairlearn appelé localement. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Précalculez les métriques d’impartialité.

    Créez un dictionnaire de tableau de bord à l’aide du package `metrics` de Fairlearn.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Chargez les métriques d’impartialité précalculées.
    
    À présent, importez le package `azureml.contrib.fairness` pour effectuer le chargement :

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Créez une expérience, une exécution, puis chargez le tableau de bord :
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Comme dans la section précédente, vous pouvez suivre l’un des chemins décrits ci-dessus (via **Expériences** ou **Modèles**) dans Azure Machine Learning Studio pour accéder au tableau de bord de visualisation et comparer les deux modèles en termes d’impartialité et de performances.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Charger des informations d’impartialité non atténuées et atténuées

Vous pouvez utiliser les algorithmes d’atténuation de [Fairlearn](https://fairlearn.github.io/user_guide/mitigation.html), comparer leurs modèles atténués générés au modèle non atténué d’origine, et parcourir les compromis entre performances et impartialité parmi les modèles comparés.

Pour voir un exemple illustrant l’utilisation de l’algorithme d’atténuation [Grid Search](https://fairlearn.github.io/user_guide/mitigation.html#grid-search) (qui crée une collection de modèles atténués avec différents compromis d’impartialité et de performances), consultez cet [exemple de bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Le chargement de plusieurs informations sur l’impartialité du modèle dans une seule exécution permet de comparer les modèles sur le plan de l’impartialité et des performances. Vous pouvez cliquer sur les modèles affichés dans le graphique de comparaison de modèles pour afficher les informations détaillées sur l’impartialité d’un modèle particulier.


[![Tableau de bord Fairlearn pour la comparaison de modèles](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’impartialité des modèles](concept-fairness-ml.md)

[Consultez les exemples de blocs-notes d’impartialité d’Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
