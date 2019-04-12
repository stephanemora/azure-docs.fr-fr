---
title: Interprétabilité de modèles
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le SDK de ce problème d’Azure Machine Learning pour expliquer la raison pour laquelle votre modèle élabore des prédictions. Il peut être utilisé au cours de formation ou d’inférence pour comprendre la façon dont votre modèle élabore des prédictions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: fbcafb61ecd69f58bb3c14d1b15f36f1b21f2833
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494438"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Problème d’apprentissage Azure SDK

Dans cet article, vous allez apprendre à expliquer pourquoi votre modèle a fait les prédictions il effectuées à l’aide du SDK de ce problème d’Azure Machine Learning. Il est important de pouvoir expliquer votre modèle pour les raisons suivantes :

* Les clients et les parties prenantes souhaitez savoir **si ils peuvent compter sur les prédictions votre modèle élabore**.
* En tant qu’un scientifique des données, vous souhaitez comprendre **comment interroger le modèle pour mieux les connaître**. Vous avez également besoin d’outils pour prendre des décisions avisées sur **comment améliorer votre modèle**.
* En tant que société, vous devez comprendre **le comportement du modèle avec des variables d’entrée distributions** et **le modèle de comportement lors de l’analyse d’entrée spécifique**.

Ce problème d’apprentissage machine est important en deux phases de cycle de développement d’apprentissage : **formation** temps et **inférence** temps :

* Au cours de **formation**: Les concepteurs de modèles et les évaluateurs requièrent les outils de ce problème pour expliquer la sortie d’un modèle aux parties prenantes pour consolider la confiance. Ils doivent également des informations sur le modèle afin qu’ils peuvent déboguer le modèle et prendre des décisions sur le comportement de la correspondance entre leurs objectifs. Enfin, dont ils ont besoin pour vous assurer que le modèle n’est pas influencé.
* Au cours de **inférence**: Prédictions doivent être être expliquées aux personnes qui utilisent votre modèle. Par exemple, pourquoi le modèle refuser un prêt immobilier ou prédire qu’un portefeuille comporte un risque plus élevé ?

Le SDK de ce problème d’Azure Machine Learning intègre des technologies développées par Microsoft et éprouvé des bibliothèques tierces (par exemple, photos et citron vert). Le Kit de développement logiciel crée une API commune entre les bibliothèques intégrés et intègre des services Azure Machine Learning. À l’aide de ce SDK, vous pouvez expliquer les modèles d’apprentissage automatique **globalement sur toutes les données**, ou **localement sur un point de données spécifique** à l’aide de technologies de pointe de manière facile à utiliser et évolutive.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Ce problème d’apprentissage Machine Azure peuvent être appliqué pour comprendre le comportement global ou des prévisions spécifiques du modèle. La première est appelée une explication globale, et ce dernier est appelé une explication locale.

Les méthodes de ce problème de Machine Learning Azure peuvent être également classés selon que la méthode est indépendante du modèle ou un modèle spécifique. Certaines méthodes ciblent certains types de modèles. Par exemple, explicatif d’arborescence d’es de forme s’applique uniquement aux modèles basés sur l’arborescence. Certaines méthodes traitent le modèle comme une boîte noire, telles qu’explicatif imiter ou explicatif de noyau de photos. SDK de ce problème d’Azure Machine Learning s’appuie sur ces différentes approches basées sur les jeux de données, les types de modèles et les cas d’utilisation.

Ce problème d’apprentissage Machine Azure retourne un ensemble d’informations sur la façon dont un modèle effectue sa prédiction. Les informations incluent des éléments tels que :

* Importance de fonctionnalité relative global/local
* Relation de fonctionnalité et de prédiction global/local

## <a name="architecture"></a>Architecture

SDK de ce problème d’Azure Machine Learning est structuré en deux packages Python :

* [azureml.EXPLAIN.Model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -le package principal, qui contient les fonctionnalités qui sont prises en charge par Microsoft.
* `azureml.contrib.explain.model` -Afficher un aperçu et les fonctionnalités expérimentales que vous pouvez essayer.

    > [!IMPORTANT]
    > Les choses dans contrib ne sont pas entièrement pris en charge. Les fonctionnalités expérimentales devenant matures, elles seront déplacés progressivement vers le package principal.

### <a name="explainers"></a>Explainers

Le SDK de ce problème d’Azure Machine Learning introduit deux ensembles d’explainers : Explainers directs et Meta Explainers.

__Diriger explainers__ proviennent de bibliothèques intégrés. Le Kit de développement logiciel encapsule tous les explainers afin qu’ils exposent une API commune et le format de sortie. Voici une liste des explainers directs disponibles dans le SDK :

> [!TIP]
> Si vous êtes plus à l’aise directement à l’aide de ces explainers, vous pouvez les appeler directement au lieu d’utiliser l’API commune et le format de sortie.

* **Arborescence explicatif**: Explicatif d’arborescence de photos, qui se concentre sur le temps polynomiale rapide es de forme valeur algorithme d’estimation spécifique aux arbres et ensembles d’arbres de.
* **Explicatif approfondie**: Selon l’explication de photos, explicatif approfondie « est un algorithme d’approximation à haut débit pour les valeurs de photos dans les modèles d’apprentissage profond qui s’appuie sur une connexion avec DeepLIFT décrit dans le document de photos NIPS. Modèles de TensorFlow et de Keras à l’aide du serveur principal TensorFlow sont pris en charge (il existe également une prise en charge préliminaire pour PyTorch) ».
* **Noyau explicatif**: Explicatif de noyau de photos utilise une régression linéaire locale spécialement pondérée pour estimer les valeurs de photos pour n’importe quel modèle.
* **Imiter explicatif**: Explicatif imiter repose sur l’idée des modèles de substitution global. Un modèle de substitution global est un modèle intrinsèquement interprétable est formé pour estimer les prédictions d’un modèle de boîte noire aussi précisément que possible. Scientifique des données peuvent interpréter le modèle de substitution pour conclusions non fondées sur le modèle de boîte noire.
* **Citron vert explicatif**: Selon citron vert, citron vert explicatif utilise l’algorithme de pointe Local interprétable indépendant du modèle des explications (citron vert) pour créer des modèles de substitution local. Contrairement aux modèles de substitution global citron vert se concentre sur la formation des modèles de substitution local pour expliquer les prédictions individuelles.
* **HENRI texte explicatif**: HENRI texte explicatif utilise un réseau d’Attention hiérarchique pour obtenir des explications sur le modèle à partir des données de texte pour un modèle de texte donné boîte noire. Nous formons le modèle de substitution HAN sur des sorties prédite d’un modèle donné enseignant. Après une formation de manière globale sur le corpus de texte, nous avons ajouté une étape de réglage pour un document spécifique afin d’améliorer la précision des explications. HENRI utilise un RNN bidirectionnel avec deux couches d’attention, attention phrase et word. Une fois que le réseau de neurones profond est formé sur le modèle de l’enseignant et affiné sur un document spécifique, nous pouvons extraire les importances word les couches de votre attention. Nous avons trouvé HAN pour être plus précis que citron vert ou es de forme pour les données texte mais plus coûteux en termes d’ainsi le temps d’apprentissage. Toutefois, nous avons apporté des améliorations apportées à la durée d’apprentissage en donnant à l’utilisateur l’option pour initialiser le réseau avec GANT incorporations, bien qu’il soit toujours lent. La durée d’apprentissage peut améliorer considérablement le HAN en cours d’exécution sur une machine virtuelle de GPU Azure distante. L’implémentation de HENRI est décrite dans « Hiérarchique Attention réseaux pour la Classification de Document (Yang et al., 2016) » ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

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

[![MArchitecture de ce problème d’apprentissage d’achine](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modèles pris en charge

Tous les modèles sont formés sur les jeux de données dans Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, ou `scipy.sparse.csr_matrix` format sont pris en charge par le SDK de ce problème d’apprentissage Machine.

Les fonctions d’explication acceptent à la fois des modèles et des pipelines en tant qu’entrée. Si un modèle est fourni, le modèle doit implémenter la fonction de prédiction `predict` ou `predict_proba` qui est conforme à la convention de Scikit. Si un pipeline (nom du script de pipeline) est fourni, la fonction explication suppose que le script de pipeline en cours d’exécution retourne une prédiction.

### <a name="local-and-remote-compute-target"></a>Cible de calcul locaux et distants

Le Kit de développement logiciel de Machine Learning ce problème est conçu pour fonctionner avec les deux cibles de calcul locaux et distants. Si vous exécutez localement, les fonctions du SDK contactera pas tous les services Azure. Vous pouvez exécuter à distance des explications sur Azure Machine Learning Compute et connectez-vous l’information sur l’explication des Services Azure Machine Learning exécuter l’historique. Une fois que ces informations sont enregistrées, les rapports et des visualisations à partir de l’explication sont disponibles sur le portail d’espace de travail Azure Machine Learning pour l’analyse de l’utilisateur.

## <a name="train-and-explain-locally"></a>Former et expliquez localement

1. Formation d’un modèle dans un bloc-notes Jupyter local. 

    ``` python
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

2. Appelez l’explicatif : Pour lancer un objet d’explicatif, vous devez transmettre le modèle, les données d’apprentissage, les fonctionnalités d’intérêt (facultatif) et les noms de classe de sortie (si classification) à l’explicatif. Voici comment instancier un objet d’explicatif en utilisant [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), et `LimeExplainer` localement. `TabularExplainer` Parmi les trois explainers sous appelle (`TreeExplainer`, `DeepExplainer`, ou `KernelExplainer`) et est automatiquement sélectionnons le plus approprié pour votre cas d’usage. Toutefois, vous pouvez appeler chacune de ses trois explainers sous-jacente directement.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    or
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Obtenir la fonctionnalité globale de valeurs d’importance.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Valeurs d’importance de fonctionnalité locale : utiliser les appels de fonction suivants pour expliquer une instance individuelle ou un groupe d’instances.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    or
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Former et expliquer à distance

Bien que vous pouvez effectuer l’apprentissage sur les différentes cibles de calcul pris en charge par le service Azure Machine Learning, l’exemple dans cette section montre comment effectuer cette opération à l’aide de AMLCompute.

1. Créer un script de formation dans un bloc-notes Jupyter local (par exemple, run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Suivez les instructions de [configurer des cibles de calcul pour l’apprentissage du modèle](how-to-set-up-training-targets.md#amlcompute) pour en savoir plus sur la façon de configurer un Azure Machine Learning Compute comme cible de calcul et soumettre votre série de formation.

3. Téléchargez l’explication dans votre bloc-notes Jupyter local. 
    > [!IMPORTANT]
    > Les choses dans contrib ne sont pas entièrement pris en charge. Les fonctionnalités expérimentales devenant matures, elles seront déplacés progressivement vers le package principal.

    ``` python
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

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une collection de bloc-notes Jupyter qui montrent les instructions ci-dessus, consultez le [des exemples de bloc-notes Azure Machine Learning est-il](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).