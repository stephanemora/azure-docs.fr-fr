---
title: Nouveautés de la version
titleSuffix: Azure Machine Learning
description: Découvrez les dernières mises à jour d’Azure Machine Learning et les kits SDK Python de Machine Learning et de préparation de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 6f244fc057638bc94a94c150d9333435c0197a74
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249744"
---
# <a name="azure-machine-learning-release-notes"></a>Notes de publication d’Azure Machine Learning

Dans cet article, découvrez les versions d’Azure Machine Learning.  Pour obtenir le contenu complet de la référence SDK, consultez la page de référence du [**SDK principal pour Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) d’Azure Machine Learning.

Consultez la [liste des problèmes connus](resource-known-issues.md) pour en savoir plus sur les bogues connus et les solutions de contournement.

## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0"></a>SDK Azure Machine Learning pour Python v1.1.2rc0

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + Activation de l’inférence du mode batch (prise de plusieurs lignes en une fois) pour les modèles ONNX automl
    + Amélioration de la détection de la fréquence sur les jeux de données, des données manquantes ou contenant des points de données irréguliers
    + Ajout de la possibilité de supprimer des points de données qui ne respectent pas la fréquence dominante.
  + **azureml-automl-runtime**
    + Correction du problème qui provoquait la levée d’une erreur si le grain qui n’était pas présent dans le jeu d’entraînement apparaissait dans le jeu de test
    + Suppression de l’exigence liée à y_query lors de la notation du service de prévision
  + **azureml-contrib-mir**
    + Ajout d’une fonctionnalités dans la classe MirWebservice pour récupérer le jeton d’accès
  + **azureml-core**
    + Les services web AzureML déployés utilisent désormais par défaut la journalisation `INFO`. Vous pouvez contrôler cela en définissant la variable d’environnement `AZUREML_LOG_LEVEL` dans le service déployé.
    + Correction de l’itération sur `Dataset.get_all` pour retourner tous les jeux de données inscrits auprès de l’espace de travail.
    + Amélioration du message d’erreur quand un type non valide est passé à l’argument `path` des API de création de jeu de données.
    + Le SDK Python utilise le service de découverte pour utiliser le point de terminaison « api » au lieu de « pipelines ».
    + Basculement vers les nouvelles routes dans tous les appels du SDK
    + Modification du routage des appels à ModelManagementService vers une nouvelle structure unifiée
      + La méthode de mise à jour de l’espace de travail est désormais publiquement disponible.
      + Ajout du paramètre image_build_compute dans la méthode de mise à jour de l’espace de travail afin de permettre à l’utilisateur de mettre à jour le calcul pour la génération d’image
    +  Ajout de messages de dépréciation à l’ancien workflow de profilage Correction des limites de mémoire et de processeur du profilage
  + **azureml-interpret**
    + Mise à jour d’azureml-interpret vers interpret-community 0.6.*
  + **azureml-mlflow**
    + Ajout de la prise en charge des clouds souverains à azureml.mlflow
  + **azureml-pipeline-steps**
    + Déplacement de `AutoMLStep` vers `azureml-pipeline-steps package`. Dépréciation d’`AutoMLStep` dans `azureml-train-automl-runtime`.
  + **azureml-train-automl-client**
    + Résolution d’un problème où certains packages pouvaient être installés à des versions incorrectes sur des exécutions distantes.
  + **azureml-train-automl-runtime**
    + Résolution du problème de détection de fréquence dans les exécutions distantes
    + Déplacement de `AutoMLStep` vers `azureml-pipeline-steps package`. Dépréciation d’`AutoMLStep` dans `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Déplacement de `AutoMLStep` vers `azureml-pipeline-steps package`. Dépréciation d’`AutoMLStep` dans `azureml-train-automl-runtime`.

## <a name="2020-02-18"></a>18-02-2020

### <a name="azure-machine-learning-sdk-for-python-v111rc0"></a>SDK Azure Machine Learning pour Python v1.1.1rc0

+ **Résolutions de bogue et améliorations**
  + **azure-cli-ml**
    + Le profilage d’instance unique a été corrigé pour produire une recommandation et a été mis à disposition dans le kit SDK Core.
  + **azureml-automl-core**
    + La journalisation des erreurs a été améliorée.
  + **azureml-automl-runtime**
    + Correction du problème avec les prévisions lorsque le jeu de données contient des fragments réduits avec de longs laps de temps.
    + Correction du problème lorsque l’horizon maximal automatique est activé et que la colonne de date contient des dates sous forme de chaînes. Nous avons ajouté une conversion correcte et une erreur raisonnable si la conversion en date n’est pas possible
    + Utilisation de NumPy et SciPy en mode natif pour la sérialisation et la désérialisation des données intermédiaires pour FileCacheStore (utilisé pour les exécutions AutoML locales)
    + Correction d’un bogue où les exécutions enfants ayant échoué pouvaient rester bloquées dans l’état d’exécution.
  + **azureml-cli-common**
    + Le profilage d’instance unique a été corrigé pour produire une recommandation et a été mis à disposition dans le kit SDK Core.
  + **azureml-core**
    + Ajout de `--grant-workspace-msi-access` en tant que paramètre supplémentaire pour l’interface CLI de magasin de données pour l’inscription du conteneur d’objets blob Azure, ce qui vous permet d’inscrire un conteneur d’objets blob qui se trouve derrière un réseau virtuel
    + Le profilage d’instance unique a été corrigé pour produire une recommandation et a été mis à disposition dans le kit SDK Core.
    + Correction du problème dans aks.py _deploy
    + Valide l’intégrité des modèles en cours de chargement afin d’éviter les échecs de stockage en mode silencieux.
  + **azureml-interpret**
    + Ajout d’exceptions de style azureml à azureml-interpret
    + Correction de la sérialisation DeepScoringExplainer pour les modèles Keras
  + **azureml-pipeline-core**
    + Le notebook de scoring par lots du pipeline utilise désormais ParallelRunStep
  + **azureml-pipeline-steps**
    + Déplacement d’`AutoMLStep` dans le package `azureml-pipeline-steps`. Dépréciation d’`AutoMLStep` dans `azureml-train-automl-runtime`.
  + **azureml-contrib-pipeline-steps**
    + Paramètre facultatif side_inputs ajouté à ParallelRunStep. Ce paramètre peut être utilisé pour monter le dossier sur le conteneur. Les types actuellement pris en charge sont DataReference et PipelineData.
  + **azureml-tensorboard**
    + Mise à jour d’azureml-tensorboard pour prendre en charge TensorFlow 2.0
  + **azureml-train-automl-client**
    + Correction du problème de substitution FeaturizationConfig qui filtre la configuration de caractérisation personnalisée.
  + **azureml-train-automl-runtime**
    + Déplacement d’`AutoMLStep` dans le package `azureml-pipeline-steps`. Dépréciation d’`AutoMLStep` dans `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Prise en charge de la version 1.4 de PyTorch dans l’estimateur PyTorch
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0"></a>SDK Azure Machine Learning pour Python v1.1.0rc0

+ **Dernières modifications**
  + **Gestion sémantique de version 2.0.0**
    + Depuis la version 1.1, le SDK Python Azure Machine Learning a adopté la gestion sémantique de version 2.0.0. [En savoir plus ici](https://semver.org/). Toutes les versions ultérieures suivront le nouveau schéma de numérotation et le contrat de gestion sémantique de version. 
  
+ **Résolutions de bogue et améliorations**
  + **azureml-automl-runtime**
    + Augmentation de la vitesse de caractérisation.
    + Correction de la vérification de fréquence lors du scoring. Désormais, dans les tâches de prévision, nous n’exigeons plus une équivalence stricte de fréquence entre l’entraînement et le jeu de test.
  + **azureml-core**
    + L’utilisateur peut désormais spécifier une valeur pour la clé d’authentification lorsqu’il regénère les clés de services web.
  + **azureml-interpret**
    + Mise à jour d’azureml-interpret pour dépendre désormais d’interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Correction d’un bogue entraînant une réutilisation incorrecte des résultats PythonScriptStep malgré la modification de la liste d’arguments
  + **azureml-pipeline-steps**
    + Ajout d’un exemple de documentation pour le jeu de données sous la forme d’une entrée PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Les paramètres passés dans ParallelRunConfig peuvent désormais être remplacés en passant des paramètres de pipeline. De nouveaux paramètres de pipeline sont pris en charge : aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count et aml_process_count_per_node faisaient déjà partie de la précédente version).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.85

+ **Nouvelles fonctionnalités**
  + **azureml-core**
    + Obtenir l’utilisation des cœurs actuelle et la limite de quota pour les ressources AmlCompute dans un espace de travail et un abonnement donnés
  
  + **azureml-contrib-pipeline-steps**
    + Permettre à l’utilisateur de passer le jeu de données tabulaire en tant que résultat intermédiaire de l’étape précédente à parallelrunstep

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-runtime**
    + Suppression de l’exigence de colonne y_query dans la demande envoyée au service de prévision déployé. 
    + Le « y_query » a été supprimé de la section de la demande de service notebook Orange Juice de Dominick.
    + Correction du bogue empêchant les prévisions sur les modèles déployés opérant sur des jeux de données avec des colonnes de date et d’heure.
    + Ajout du coefficient de corrélation Matthews en tant que métrique de classification, pour la classification binaire et multiclasse.
  + **azureml-contrib-interpret**
    + Suppression des explications de texte d’azureml-contrib-interpret, car une explication du texte a été déplacée vers le référentiel interpret-text qui sera bientôt publié.
  + **azureml-core**
    + Jeu de données : les utilisations du jeu de données de fichier ne dépendent plus de l’installation de numpy et pandas dans l’environnement Python.
    + Modification de LocalWebservice.wait_for_deployment() pour vérifier l’état du conteneur Docker local avant d’essayer d’effectuer un test ping de son point de terminaison d’intégrité, ce qui réduit considérablement le temps nécessaire pour signaler un échec de déploiement.
    + Correction de l’initialisation d’une propriété interne utilisée dans LocalWebservice.reload() lors de la création de l’objet service à partir d’un déploiement existant à l’aide du constructeur LocalWebservice().
    + Modification du message d’erreur pour clarification.
    + Ajout d’une nouvelle méthode appelée get_access_token() à AksWebservice, qui retourne l’objet AksServiceAccessToken contenant un jeton d’accès, une actualisation après horodatage, une expiration sur horodatage et un type de jeton. 
    + Abandon de la méthode get_token() existante dans AksWebservice, car la nouvelle méthode retourne toutes les informations que cette méthode retourne.
    + Modification de la sortie de la commande az ml service get-access-token. Changement de nom de token en accessToken et de refreshBy en refreshAfter. Ajout des propriétés expiryOn et tokenType.
    + Correction de Fixed get_active_runs
  + **azureml-explain-model**
    + mise à jour de shap vers 0.33.0 et d’interpret-community vers 0.4.*
  + **azureml-interpret**
    + mise à jour de shap vers 0.33.0 et d’interpret-community vers 0.4.*
  + **azureml-train-automl-runtime**
    + Ajout du coefficient de corrélation Matthews en tant que métrique de classification, pour la classification binaire et multiclasse.
    + Abandon de l’indicateur de prétraitement du code et remplacement par caractérisation (caractérisation activée par défaut)

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.83

+ **Nouvelles fonctionnalités**
  + Jeu de données : ajoutez deux options `on_error` et `out_of_range_datetime` pour que `to_pandas_dataframe` échoue lorsque les données ont des valeurs d’erreur au lieu de les remplir avec `None`.
  + Espace de travail : Ajout de l’indicateur `hbi_workspace` pour les espaces de travail avec des données sensibles qui permettent un chiffrement supplémentaire et désactivent les diagnostics avancés sur les espaces de travail. Nous avons également ajouté la prise en charge de l’ajout de vos propres clés pour l’instance Cosmos DB associée, en spécifiant les paramètres `cmk_keyvault` et `resource_cmk_uri` lors de la création d’un espace de travail, ce qui crée une instance Cosmos DB dans votre abonnement lors de l’approvisionnement de votre espace de travail. [Apprenez-en davantage ici](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db).

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-runtime**
    + Correction d’une régression qui provoquait le déclenchement de TypeError lors de l’exécution d’AutoML sur les versions de Python inférieures à 3.5.4.
  + **azureml-core**
    + Correction du bogue dans `datastore.upload_files` selon lequel un chemin d’accès relatif ne commençant pas par `./` n’était pas utilisable.
    + Ajout de messages de dépréciation pour tous les chemins du code de la classe Image.
    + Correction de la construction de l’URL Gestion des modèles pour la région Mooncake.
    + Correction du problème selon lequel il n’était pas possible de packager les modèles qui utilisent source_dir pour Azure Functions.    
    + Ajout d’une option à [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) pour pouvoir envoyer (push) une image dans le registre de conteneurs de l’espace de travail AzureML.
    + Mise à jour du kit SDK de façon à utiliser la nouvelle bibliothèque de jetons sur Azure Synapse en compatibilité descendante.
  + **azureml-interpret**
    + Correction du bogue selon lequel None était retourné quand aucune explication n’était téléchargeable. Lève désormais une exception, ce qui correspond aux autres comportements.
  + **azureml-pipeline-steps**
    + Ajout de l’interdiction de passer des `DatasetConsumptionConfig` au paramètre `inputs` de `Estimator` lorsque `Estimator` est utilisé dans `EstimatorStep`.
  + **azureml-sdk**
    + Ajout du client AutoML au package azureml-sdk, ce qui permet d’envoyer les exécutions AutoML distantes sans installer le package AutoML complet.
  + **azureml-train-automl-client**
    + Correction de l’alignement sur la sortie de la console pour les exécutions AutoML.
    + Correction du bogue selon lequel une mauvaise version de pandas était parfois installée sur amlcompute distant.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.81

+ **Résolutions de bogue et améliorations**
  + **azureml-contrib-interpret**
    + Report de la dépendance Shap sur interpret-community à partir de azureml-interpret.
  + **azureml-core**
    + Ajout de la possibilité d’indiquer la cible de calcul comme paramètre des objets de configuration de déploiement correspondants. Il s’agit en particulier du nom de la cible de calcul sur laquelle le déploiement est effectué, et non de l’objet du kit SDK.
    + Ajout d’informations CreatedBy aux objets Model et Service, accessibles avec <var>.created_by.
    + Correction de ContainerImage.Run(), qui ne configurait pas correctement le port HTTP du conteneur Docker.
    + Possibilité de rendre `azureml-dataprep` facultatif pour la commande CLI `az ml dataset register`.
    + Correction du bogue selon lequel `TabularDataset.to_pandas_dataframe` revenait à tort sur un autre lecteur et imprimait un avertissement.
  + **azureml-explain-model**
    + Report de la dépendance Shap sur interpret-community à partir de azureml-interpret.
  + **azureml-pipeline-core**
    + Ajout d’une nouvelle étape de pipeline `NotebookRunnerStep`, consistant à exécuter un bloc-notes local.
    + Suppression des fonctions get_all dépréciées pour PublishedPipelines, Schedules et PipelineEndpoints.
  + **azureml-train-automl-client**
    + Début de la dépréciation de data_script comme entrée d’AutoML.


## <a name="2019-12-09"></a>09-12-2019

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>SDK Azure Machine Learning pour Python v1.0.79

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + Suppression de la journalisation de featurizationConfig
      + Journalisation modifiée de manière à journaliser uniquement "auto"/"off"/"customized"
  + **azureml-automl-runtime**
    + Ajout de la prise en charge de pandas.Series et de pandas.Categorical pour la détection du type de données de la colonne. Avant, seul numpy.ndarray était pris en charge
      + Ajout de modifications relatives au code permettant de gérer correctement les types de données de catégorie.
    + L’interface de la fonction Forecast a été améliorée : le paramètre y_pred est désormais facultatif. Les docstrings ont été améliorés.
  + **azureml-contrib-dataset**
    + Correction d’un bogue dans lequel les jeux de données étiquetés ne pouvaient pas être montés.
  + **azureml-core**
    + Résolution d’un bogue concernant `Environment.from_existing_conda_environment(name, conda_environment_name)`. L’utilisateur peut créer une instance d’environnement qui soit le réplica exact de l’environnement local
    + Remplacement des méthodes de jeux de données relatives à la série chronologique par `include_boundary=True` par défaut.
  + **azureml-train-automl-client**
    + Résolution d’un problème lors duquel les résultats de validation n’étaient pas affichés quand l’option d’affichage de la sortie était définie sur false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.76

+ **Dernières modifications**
  + Problèmes de mise à niveau d’Azureml-Train-AutoML
    + La mise à niveau vers azureml-train-automl>=1.0.76 à partir de azureml-train-automl<1.0.76 peut provoquer des installations partielles, entraînant l’échec de certaines importations automl. Pour résoudre ce risque, vous pouvez exécuter le script d’installation qui se trouve sur https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Ou, si vous utilisez pip directement, vous pouvez :
      + « pip install --upgrade azureml-train-automl »
      + « pip install --ignore-installed azureml-train-automl-client »
    + ou vous pouvez désinstaller l’ancienne version avant la mise à niveau
      + « pip uninstall azureml-train-automl »
      + « pip install azureml-train-automl »

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-runtime**
    + AutoML prend désormais en compte les deux classes true et false lors du calcul des métriques scalaires moyennes pour les tâches de classification binaires.
    + Déplacement du Machine Learning et du code de formation dans AzureML-AutoML-Core vers un nouveau package AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Lorsque vous appelez `to_pandas_dataframe` sur un jeu de données étiqueté avec l’option de téléchargement, vous pouvez choisir de remplacer les fichiers existants.
    + Lors de l’appel de `keep_columns` ou `drop_columns` qui entraîne la suppression d’une colonne TimeSeries, d’étiquette ou d’image, les fonctionnalités correspondantes seront également supprimées pour le jeu de données.
    + Correction d’un problème avec le chargeur pytorch pour la tâche de détection d’objets.
  + **azureml-contrib-interpret**
    + Suppression du widget de tableau de bord d’explication d’azureml-contrib-interpret, package modifié pour référencer le nouveau package dans interpret_community
    + Version mise à jour d’interpret-community vers 0.2.0
  + **azureml-core**
    + Amélioration des performances de `workspace.datasets`.
    + Ajout de la possibilité d’inscrire des magasins de données Azure SQL Database à l’aide de l’authentification par nom d’utilisateur et mot de passe
    + Correctif pour le chargement RunConfigurations à partir de chemins d’accès relatifs.
    + Lors de l’appel de `keep_columns` ou `drop_columns` qui entraîne la suppression d’une colonne TimeSeries, les fonctionnalités correspondantes seront également supprimées pour le jeu de données.
  + **azureml-interpret**
    + version mise à jour d’interpret-community à 0.2.0
  + **azureml-pipeline-steps**
    + Valeurs prises en charge documentées pour `runconfig_pipeline_params` pour les étapes de pipeline Machine Learning Azure.
  + **azureml-pipeline-core**
    + Ajout de l’option CLI pour télécharger la sortie au format json pour les commandes de pipeline.
  + **azureml-train-automl**
    + Fractionner AzureML-train-AutoML en 2 packages, un package client AzureML-Train-AutoML-Client et un package de formation ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Ajout d’un client léger pour l’envoi d’expériences AutoML sans avoir à installer de dépendances Machine Learning localement.
    + Journalisation fixe des décalages détectés automatiquement, des tailles de fenêtre dynamiques et des horizons maximum dans les exécutions distantes.
  + **azureml-train-automl-runtime**
    + Ajout d’un nouveau package AutoML pour isoler les composants de Machine Learning et d’exécution du client.
  + **azureml-contrib-train-rl**
    + Ajout de la prise en charge de l’apprentissage de renforcement dans le SDK.
    + Ajout de la prise en charge d’AmlWindowsCompute dans RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.74

  + **Fonctionnalités préliminaires**
    + **azureml-contrib-dataset**
      + Après l’importation de azureml-contrib-dataset, vous pouvez appeler `Dataset.Labeled.from_json_lines` au lieu de `._Labeled` pour créer un jeu de données étiqueté.
      + Lorsque vous appelez `to_pandas_dataframe` sur un jeu de données étiqueté avec l’option de téléchargement, vous pouvez choisir de remplacer les fichiers existants.
      + Lors de l’appel de `keep_columns` ou `drop_columns` qui entraîne la suppression d’une colonne TimeSeries, d’étiquette ou d’image, les fonctionnalités correspondantes seront également supprimées pour le jeu de données.
      + Résolution des problèmes liés au chargeur PyTorch lors de l’appel de `dataset.to_torchvision()`.

+ **Résolutions de bogue et améliorations**
  + **azure-cli-ml**
    + Ajout du profilage de modèle à l’interface CLI en préversion.
    + Corrige le changement cassant de Stockage Azure provoquant l’échec de l’interface CLI AzureML.
    + Ajout du type d’équilibrage de charge à MLC pour les types AKS
  + **azureml-automl-core**
    + Correction du problème de détection de l’horizon maximal sur la série chronologique, avec des valeurs manquantes et des niveaux de précision multiples.
    + Correction du problème lié aux échecs de génération des découpages de validation croisée.
    + Remplacez cette section par un message sous forme de balisage Markdown qui apparaîtra dans les notes de publication : -Amélioration de la gestion des formats réduits dans les jeux de données de prévision.
    + Correction du problème de masquage de certaines informations utilisateur lors de la journalisation. -Amélioration de la journalisation des erreurs lors des exécutions de prévisions.
    + Ajout de psutil en tant que dépendance Conda au fichier de déploiement yml généré automatiquement.
  + **azureml-contrib-mir**
    + Corrige le changement cassant de Stockage Azure provoquant l’échec de l’interface CLI AzureML.
  + **azureml-core**
    + Correction d’un bogue dans lequel les modèles déployés sur Azure Functions produisaient des erreurs de type 500.
    + Correction d’un problème dans lequel le fichier amlignore n’était pas appliqué sur les captures instantanées.
    + Ajout d’une nouvelle API amlcompute. get_active_runs qui renvoie un générateur pour l’exécution et les exécutions en file d’attente sur un amlcompute donné.
    + Ajout du type d’équilibrage de charge à MLC pour les types AKS.
    + Ajout du paramètre booléen append_prefix à download_files dans run.py et download_artifacts_from_prefix dans artifacts_client. Cet indicateur est utilisé pour aplatir sélectivement le chemin d’accès d’origine de sorte que seul le nom du fichier ou du dossier est ajouté au output_directory
    + Résolvez le problème de désérialisation pour `run_config.yml` avec l’utilisation du jeu de données.
    + Lors de l’appel de `keep_columns` ou `drop_columns` qui entraîne la suppression d’une colonne TimeSeries, les fonctionnalités correspondantes seront également supprimées pour le jeu de données.
  + **azureml-interpret**
    + Mise à jour d’interpret-community version sur 0.1.0.3
  + **azureml-train-automl**
    + Correction d’un problème où automl_step peut ne pas imprimer les problèmes de validation.
    + Correction de register_model pour qu’il aboutisse, même si les dépendances sont manquantes localement dans l’environnement du modèle.
    + Résolution d’un problème où certaines exécutions à distance n’étaient pas activées pour le docker.
    + Ajoutez la journalisation de l’exception qui provoque l’échec prématuré d’une exécution locale.
  + **azureml-train-core**
    + Envisagez l’exécution de resume_from dans le cadre du calcul de la meilleure exécution des enfants de paramétrage automatisé.
  + **azureml-pipeline-core**
    + Correction de la gestion des paramètres dans la construction d’arguments du pipeline.
    + Ajout de la description du pipeline et du paramètre de type d’étape YAML.
    + Nouveau format YAML pour l’étape de pipeline et ajout d’un avertissement de désapprobation pour l’ancien format.



## <a name="2019-11-04"></a>04-11-2019

### <a name="web-experience"></a>Expérience web

La page d’accueil de l’espace de travail collaboratif à l’adresse [https://ml.azure.com](https://ml.azure.com) a été améliorée et rebaptisée en tant que Azure Machine Learning Studio (préversion).

À partir de Studio, vous pouvez effectuer l’apprentissage, le test, le déploiement et la gestion de ressources Azure Machine Learning comme des jeux de données, des pipelines, des modèles, des points de terminaison, etc.

Accédez aux outils de création web suivants à partir de Studio :

| Outil basé sur le web | Description | Édition |
|-|-|-|
| Machines virtuelles de notebooks (préversion) | Station de travail cloud entièrement managée | De base & Entreprise |
| [Machine Learning automatisé](tutorial-first-experiment-automated-ml.md) (préversion) | Aucune expérience de code pour l’automatisation du développement de modèles Machine Learning | Entreprise |
| [Concepteur](concept-designer.md) (préversion) | Glissez-déplacez l’outil de modélisation Machine Learning précédemment appelé le concepteur | Entreprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Améliorations du concepteur Azure Machine Learning

+ Anciennement appelé l’interface visuelle 
+   11 nouveaux [modules](algorithm-module-reference/module-reference.md) notamment des utilitaires de recommandation, de classement et de formation, avec des fonctionnalités d’ingénierie, de validation croisée et de transformation des données.

### <a name="r-sdk"></a>SDK R 
 
Les scientifiques des données et les développeurs d’IA utilisent le [SDK Azure Machine Learning pour R](tutorial-1st-r-experiment.md) afin de générer et d’exécuter des workflows de machine learning avec Azure Machine Learning.

Le SDK Azure Machine Learning pour R utilise le package `reticulate` pour effectuer une liaison au SDK Python. En se liant directement à Python, le SDK pour R vous permet d’accéder aux objets et méthodes principaux implémentés dans le SDK Python à partir de tout environnement R que vous choisissez.

Les principales fonctionnalités du SDK sont les suivantes :

+   Gérez les ressources cloud pour la surveillance, la journalisation et l’organisation de vos expériences de Machine Learning.
+   Formez des modèles à l’aide de ressources cloud, y compris la formation du modèle avec accélération GPU.
+   Déployez vos modèles en tant que services web sur Azure Container Instances (ACI) et Azure Kubernetes Service (AKS).

Pour obtenir une documentation complète, consultez le [site web du package](https://azure.github.io/azureml-sdk-for-r).

### <a name="azure-machine-learning-integration-with-event-grid"></a>Intégration entre Azure Machine Learning et Event Grid 

Azure Machine Learning est désormais un fournisseur de ressources pour Event Grid. Il est possible de configurer des événements Machine Learning sur le Portail Azure ou avec Azure CLI. Les utilisateurs peuvent créer des événements pour la fin d’une exécution, l’inscription de modèles, le déploiement de modèles et la détection d’une dérive de données. Ces événements peuvent être routés vers des gestionnaires d’événements pris en charge par Event Grid à des fins de consommation. Pour plus d’informations, voir les articles [Schémas](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning), [Concepts](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) et [Tutoriel](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) sur les événements Machine Learning.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>SDK Azure Machine Learning pour Python v1.0.72

+ **Nouvelles fonctionnalités**
  + Ajout de superviseurs de jeu de données via le package [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift), permettant la supervision des jeux de données de série chronologique pour détecter la dérive de données ou d’autres modifications statistiques au fil du temps. Des alertes et des événements peuvent être déclenchés si une dérive est détectée ou si d’autres conditions sur les données sont remplies. Consultez [notre documentation](https://aka.ms/datadrift) pour plus d’informations.
  + Annonce de deux nouvelles éditions (également appelées références SKU) dans Azure Machine Learning. Avec cette version, vous pouvez désormais créer un espace de travail Azure Machine Learning De base ou Entreprise. Tous les espaces de travail existants sont dirigés par défaut vers l’édition De base, et vous pouvez accéder au portail Azure ou à Studio pour mettre à niveau l’espace de travail à tout moment. Vous pouvez créer un espace de travail De base ou Entreprise à partir du portail Azure. Lisez [notre documentation](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) pour en savoir plus. À partir du SDK, l’édition de votre espace de travail peut être déterminée à l’aide de la propriété « sku » de votre objet espace de travail.
  + Nous avons également apporté des améliorations à la Capacité de calcul Azure Machine Learning : vous pouvez désormais visualiser les métriques de vos clusters (comme le nombre total de nœuds, les nœuds en cours d’exécution, le quota total de cœurs) dans Azure Monitor, en plus de la visualisation des journaux de diagnostic pour le débogage. En outre, vous pouvez également afficher les exécutions en cours ou en file d’attente sur votre cluster et des détails comme les adresses IP des différents nœuds de votre cluster. Vous pouvez les afficher dans le portail ou à l’aide des fonctions correspondantes dans le SDK ou l’interface CLI.

  + **Fonctionnalités préliminaires**
    + Nous publions la prise en charge de la préversion du chiffrement de disque pour votre disque SSD local dans la Capacité de calcul Azure Machine Learning. Créez un ticket de support technique pour obtenir votre abonnement afin d’utiliser cette fonctionnalité.
    + Préversion publique d’Azure Machine Learning Batch Inference. Azure Machine Learning Batch Inference cible des travaux d’inférence de grande taille qui ne sont pas sensibles au temps. Batch Inference fournit une mise à l’échelle de calcul d’inférence économique avec un débit inégalé pour les applications asynchrones. Ce système est optimisée pour les débits élevés et les inférences autonomes (fire and forget) sur des ensembles de données volumineux.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Fonctionnalités activées pour le jeu de données étiqueté
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Résolutions de bogue et améliorations**
  + **azure-cli-ml**
    + L’interface CLI prend désormais en charge le packaging de modèle.
    + Ajout d’interface CLI de jeu de données. Pour plus d’informations : `az ml dataset --help`
    + Ajout de la prise en charge du déploiement et du packaging des modèles pris en charge (ONNX, scikit-learn et TensorFlow) sans instance InferenceConfig.
    + Ajout de l’indicateur de remplacement pour le déploiement de service (ACI et AKS) dans le SDK et l’interface CLI. S’il est fourni, l’indicateur remplace le service existant si le service portant le nom existe déjà. Si le service n’existe pas, un nouveau service est créé.
    + Les modèles peuvent être inscrits avec deux nouveaux frameworks, Onnx et Tensorflow. - L’inscription de modèle accepte les exemples de données d’entrée, les exemples de données de sortie et la configuration des ressources pour le modèle.
  + **azureml-automl-core**
    + L’apprentissage d’une itération s’exécute dans un processus enfant uniquement lorsque des contraintes d’exécution sont définies.
    + Ajout d’une barrière de sécurité pour les tâches de prévision, afin de vérifier si une valeur max_horizon spécifiée entraînera un problème de mémoire sur l’ordinateur donné. Si c’est le cas, un message de barrière de sécurité s’affiche.
    + Ajout de la prise en charge de fréquences complexes comme 2 ans et 1 mois. -Ajout d’un message d’erreur compréhensible si la fréquence ne peut pas être déterminée.
    + Ajout d’azureml-defaults à la génération automatique conda env pour résoudre l’échec du déploiement du modèle
    + La conversion des données intermédiaires en jeu de données tabulaire est autorisée dans le pipeline Azure Machine Learning, ainsi que leur utilisation dans `AutoMLStep`.
    + Mise à jour de l’objectif de colonne implémentée pour le streaming.
    + Mise à jour du paramètre de transformateur implémentée pour Imputer et HashOneHotEncoder pour le streaming.
    + Ajout de la taille de données actuelle et de la taille de données minimale requise aux messages d’erreur de validation.
    + Mise à jour de la taille de données minimale requise pour la validation croisée afin de garantir un minimum de deux échantillons dans chaque pli de validation.
  + **azureml-cli-common**
    + L’interface CLI prend désormais en charge le packaging de modèle.
    + Les modèles peuvent être inscrits avec deux nouveaux frameworks, Onnx et Tensorflow.
    + L’inscription de modèle accepte les exemples de données d’entrée, les exemples de données de sortie et la configuration des ressources pour le modèle.
  + **azureml-contrib-gbdt**
    + Correction du canal de publication du Notebook
    + Ajout d’un avertissement pour la cible de calcul non AmlCompute que nous ne prenons pas en charge
    + Ajout de l’estimateur LightGMB au package azureml-contrib-gbdt
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + L’interface CLI prend désormais en charge le packaging de modèle.
    + Ajout d’un avertissement d’obsolescence pour les API de jeu de données dépréciées. Consultez l’avis de modification de l’API de jeu de données sur https://aka.ms/tabular-dataset.
    + Modification de [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) pour qu’il retourne le nom et la version de l’inscription si le jeu de données est inscrit.
    + Correction d’un bogue qui fait que ScriptRunConfig avec le jeu de données en tant qu’argument ne peut pas être utilisé à plusieurs reprises pour soumettre une exécution expérimentale.
    + Les jeux de données récupérés lors d’une exécution feront l’objet d’un suivi et peuvent être visualisés dans la page des détails d’exécution ou en appelant [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) une fois l’exécution terminée.
    + La conversion des données intermédiaires en jeu de données tabulaire est autorisée dans le pipeline Azure Machine Learning, ainsi que leur utilisation dans [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep).
    + Ajout de la prise en charge du déploiement et du packaging des modèles pris en charge (ONNX, scikit-learn et TensorFlow) sans instance InferenceConfig.
    + Ajout de l’indicateur de remplacement pour le déploiement de service (ACI et AKS) dans le SDK et l’interface CLI. S’il est fourni, l’indicateur remplace le service existant si le service portant le nom existe déjà. Si le service n’existe pas, un nouveau service est créé.
    +  Les modèles peuvent être inscrits avec deux nouveaux frameworks, Onnx et Tensorflow. L’inscription de modèle accepte les exemples de données d’entrée, les exemples de données de sortie et la configuration des ressources pour le modèle.
    + Ajout d’un nouveau magasin de données pour Azure Database pour MySQL. Ajout d’un exemple pour l’utilisation d’Azure Database pour MySQL dans DataTransferStep dans des pipelines Azure Machine Learning.
    + Ajout de fonctionnalités pour ajouter et supprimer des balises dans des expérimentations - Ajout de fonctionnalités pour supprimer des balises dans les exécutions
    + Ajout de l’indicateur de remplacement pour le déploiement de service (ACI et AKS) dans le SDK et l’interface CLI. S’il est fourni, l’indicateur remplace le service existant si le service portant le nom existe déjà. Si le service n’existe pas, un nouveau service est créé.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Déplacé de `azureml-contrib-datadrift` vers `azureml-datadrift`
    + Ajout de la prise en charge de la surveillance des jeux de données TimeSeries pour la dérive et d’autres mesures statistiques
    + Nouvelles méthodes `create_from_model()` et `create_from_dataset()` ajoutées à la classe [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)). La méthode `create()` sera déconseillée.
    + Ajustements des visualisations dans Python et l’interface utilisateur dans Azure Machine Learning Studio.
    + Prise en charge de la planification des analyses hebdomadaires et mensuelles, en plus des analyses quotidiennes des jeux de données.
    + Prise en charge du renvoi des métriques du moniteur de données pour analyser les données historiques des analyses de jeu de données.
    + Divers correctifs de bogues
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep n’est plus nécessaire pour soumettre une exécution de pipeline Azure Machine Learning à partir du fichier `yaml` du pipeline.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Ajout d’azureml-defaults à la génération automatique conda env pour résoudre l’échec du déploiement du modèle
    + La formation à distance AutoML comprend désormais les valeurs azureml-defaults pour permettre la réutilisation de l’environnement de formation pour l’inférence.
  + **azureml-train-core**
    + Ajout de la prise en charge de PyTorch 1.3 dans l’estimateur [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interface visuelle (préversion)

+ L’interface visuelle Azure Machine Learning (préversion) a été repensée pour s’exécuter sur [les pipelines Azure Machine Learning](concept-ml-pipelines.md). Les pipelines (auparavant appelés expériences) créés dans l’interface visuelle sont désormais entièrement intégrés à l’expérience de base Azure Machine Learning.
  + Expérience de gestion unifiée avec des ressources de kit de développement logiciel (SDK)
  + Contrôle de version et suivi des modèles d’interface visuels, des pipelines et des points de terminaison
  + Interface utilisateur repensée
  + Ajout du déploiement Batch Inference
  + Ajout de la prise en charge d’Azure Kubernetes service (AKS) pour les des cibles de calcul de l’inférence
  + Nouveau flux de travail de création de pipeline au niveau de Python
  + Nouvelle [page de destination](https://ml.azure.com) pour les outils de création visuelle

+ **Nouveaux modules**
  + Appliquer une opération mathématique
  + Appliquer une transformation SQL
  + Limiter les valeurs
  + Totaliser les données
  + Importer à partir de la base de données SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.69

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + Limitation des explications de modèle à exécuter au mieux au lieu de calculer les explications pour chaque exécution. Modification de ce comportement pour local, remote et ADB.
    + Ajout de la prise en charge des explications de modèle à la demande pour l’interface utilisateur
    + Ajout de psutil en tant que dépendance de `automl` et psutil inclus en tant que dépendance conda dans amlcompute.
    + Correction du problème lié aux décalages heuristiques et aux tailles de fenêtre dynamique sur les jeux de données de prévisions, dont certaines séries peuvent provoquer des erreurs algébriques linéaires
      + Ajout de l’impression pour les paramètres déterminés de manière heuristique dans les exécutions de prévision.
  + **azureml-contrib-datadrift**
    + Ajout de la protection lors de la création de métriques de sortie si la dérive du niveau de jeu de données ne figure pas dans la première section.
  + **azureml-contrib-interpret**
    + Le package azureml-contrib-explain-model a été renommé en azureml-contrib-interpret
  + **azureml-core**
    + API ajoutée pour annuler l’inscription des jeux de données. `dataset.unregister_all_versions()`
    + Le package azureml-contrib-explain-model a été renommé azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + API ajoutée pour annuler l’inscription des jeux de données. dataset.[unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Ajout de l’API de jeu données pour vérifier l’heure de modification des données. `dataset.data_changed_time`.
    + Pouvoir utiliser `FileDataset` et `TabularDataset` en tant qu’entrées pour `PythonScriptStep`, `EstimatorStep` et `HyperDriveStep` dans le pipeline Azure Machine Learning
    + Les performances de `FileDataset.mount` ont été améliorées pour les dossiers avec un grand nombre de fichiers
    + Pouvoir consommer [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) et [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) comme entrées pour [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) et [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) dans le pipeline Azure Machine Learning.
    + Les performances de FileDataset.[mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) ont été améliorées pour les dossiers avec un grand nombre de fichiers
    + Ajout de l’URL aux recommandations d’erreurs connues dans les détails de l’exécution.
    + Correction d’un bogue dans run.get_metrics lors duquel les demandes échouaient si une exécution avait un trop grand nombre d’enfants
    + Correction d’un bogue dans [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) lors duquel les demandes échouaient si une exécution avait un trop grand nombre d’enfants
    + Ajout de la prise en charge de l’authentification sur le cluster Arcadia.
    + La création d’un objet Experiment permet d’obtenir ou de créer l’expérience dans l’espace de travail Azure Machine Learning pour le suivi de l’historique des exécutions. L’ID d’expérience et l’heure archivée sont renseignés dans l’objet Experiment lors de la création. Exemple : experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() et reactivate() sont des fonctions qui peuvent être appelées dans une expérience pour masquer et restaurer l’expérience à partir de l’expérience utilisateur ou retournées par défaut dans un appel pour répertorier les expériences. Si une nouvelle expérience est créée avec le même nom qu’une expérience archivée, vous pouvez renommer l’expérience archivée lors de la réactivation en transmettant un nouveau nom. Il ne peut y avoir qu’une seule expérience active avec un nom donné. Exemple : experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Créez une nouvelle expérience active portant le même nom que celle archivée. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") La liste de méthodes statiques() dans Experiment peut accepter un filtre de nom et un filtre ViewType. Les valeurs ViewType sont "ACTIVE_ONLY", "ARCHIVED_ONLY" et "ALL" Exemple : archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Prise en charge de l’utilisation de l’environnement pour le déploiement de modèle et la mise à jour du service
  + **azureml-datadrift**
    + L’attribut show de la classe DataDriftDector ne prend plus en charge l’argument facultatif « with_details ». L’attribut show affichera uniquement le coefficient de dérive des données et la contribution à la dérive des données des colonnes de caractéristiques.
    + Modifications du comportement de l’attribut DataDriftDetector « get_Output » :
      + les paramètres d’entrée start_time et end_time sont facultatifs au lieu d’obligatoires ;
      + Les entrée spécifiques start_time et/ou end_time avec un run_id spécifique dans le même appel entraînent une exception d’erreur de valeur, car elles s’excluent mutuellement ;
      + lorsque vous entrez des valeurs spécifiques pour start_time et/ou end_time, seuls les résultats des exécutions planifiées sont retournés ;
      + Le paramètre « daily_latest_only » est déconseillé.
    + Prise en charge de la récupération des sorties de dérive des données basées sur les jeux de données.
  + **azureml-explain-model**
    + Renomme le package AzureML-explain-model en AzureML-interpret, en conservant l’ancien package pour la compatibilité descendante pour le moment
    + Correction du bogue `automl` avec des explications brutes définies sur la tâche de classification au lieu de la régression par défaut lors d’un téléchargement à partir d’ExplanationClient
    + Ajout de la prise en charge de `ScoringExplainer` à créer directement à l’aide de `MimicWrapper`
  + **azureml-pipeline-core**
    + Amélioration des performances pour la création de pipelines volumineux
  + **azureml-train-core**
    + Ajout de la prise en charge de TensorFlow 2.0 dans l’estimateur TensorFlow
  + **azureml-train-automl**
    + La création d’un objet [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) permet d’obtenir ou de créer l’expérience dans l’espace de travail Azure Machine Learning pour le suivi de l’historique des exécutions. L’ID d’expérience et l’heure archivée sont renseignés dans l’objet Experiment lors de la création. Exemple :

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) et [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) sont des fonctions qui peuvent être appelées dans une expérience pour masquer et restaurer l’expérience à partir de l’expérience utilisateur ou retournées par défaut dans un appel pour répertorier les expériences. Si une nouvelle expérience est créée avec le même nom qu’une expérience archivée, vous pouvez renommer l’expérience archivée lors de la réactivation en transmettant un nouveau nom. Il ne peut y avoir qu’une seule expérience active avec un nom donné. Exemple :

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        La méthode statique [list()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) dans Experiment peut accepter un filtre de nom et un filtre ViewType. Les valeurs ViewType sont « ACTIVE_ONLY », « ARCHIVED_ONLY » et « ALL ». Exemple :

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Prise en charge de l’utilisation de l’environnement pour le déploiement de modèle et la mise à jour du service.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + L’attribut show de la classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) ne prend plus en charge l’argument facultatif « with_details ». L’attribut show affichera uniquement le coefficient de dérive des données et la contribution à la dérive des données des colonnes de caractéristiques.
    + Modifications du comportement de la fonction DataDriftDetector [get_Output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-)  :
      + les paramètres d’entrée start_time et end_time sont facultatifs au lieu d’obligatoires ;
      + Les entrée spécifiques start_time et/ou end_time avec un run_id spécifique dans le même appel entraînent une exception d’erreur de valeur, car elles s’excluent mutuellement ;
      + lorsque vous entrez des valeurs spécifiques pour start_time et/ou end_time, seuls les résultats des exécutions planifiées sont retournés ;
      + Le paramètre « daily_latest_only » est déconseillé.
    + Prise en charge de la récupération des sorties de dérive des données basées sur les jeux de données.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Renomme le package AzureML-explain-model en AzureML-interpret, en conservant l’ancien package pour la compatibilité descendante pour le moment.
    + Correction du bogue AutoML avec des explications brutes définies sur la tâche de classification au lieu de la régression par défaut lors d’un téléchargement à partir d’ExplanationClient.
    + Ajout de la prise en charge de [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) pour une création directe à l’aide de [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Amélioration des performances pour la création de pipelines volumineux.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Ajout de la prise en charge de TensorFlow 2.0 dans l’estimateur [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow).
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + L’exécution parente n’est plus en échec en cas d’échec de l’itération du programme d’installation, car l’orchestration s’en charge déjà.
    + Ajout de la prise en charge de local-docker et local-conda pour les expériences AutoML
    + Ajout de la prise en charge de local-docker et local-conda pour les expériences AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nouvelle expérience web (préversion) pour les espaces de travail Azure Machine Learning

L’onglet Expérience dans le [nouveau portail des espaces de travail](https://ml.azure.com) a été mis à jour pour que les scientifiques des données puissent superviser leurs expériences de manière plus performante. Vous pouvez explorer les fonctionnalités suivantes :
+ Métadonnées d’expérience pour filtrer et trier facilement votre liste d’expériences
+ Pages de détails des expériences simplifiées et performantes pour vous permettre de visualiser et de comparer vos exécutions
+ Nouvelle conception des pages de détails des exécutions pour comprendre et superviser vos exécutions d’entraînement

## <a name="2019-09-30"></a>30-09-2019

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.65

  + **Nouvelles fonctionnalités**
    + Ajout d’environnements organisés. Ces environnements ont été préconfigurés avec des bibliothèques pour des tâches de Machine Learning courantes, et ont été précompilés et mis en cache en tant qu’images Docker afin d’accélérer l’exécution. Celles-ci s’affichent par défaut dans la liste d’environnements de l’espace de travail, avec le préfixe « AzureML ».
    + Ajout d’environnements organisés. Ces environnements ont été préconfigurés avec des bibliothèques pour des tâches de Machine Learning courantes, et ont été précompilés et mis en cache en tant qu’images Docker afin d’accélérer l’exécution. Celles-ci s’affichent par défaut dans la liste d’environnements de [l’espace de travail](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29), avec le préfixe « AzureML ».

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ajout de la prise en charge de la conversion ONNX pour ADB et HDI

+ **Fonctionnalités préliminaires**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT et BiLSTM pris en charge en tant que caractériseur de texte (préversion uniquement)
    + Personnalisation de caractérisation prise en charge pour les paramètres d’objet de colonne et de transformateur (préversion uniquement)
    + Explications brutes prises en charge lorsque l’utilisateur active l’explication du modèle pendant l’apprentissage (préversion uniquement)
    + Ajout de Prophet pour les prévisions `timeseries` en tant que pipeline apte à l’apprentissage (préversion uniquement)

  + **azureml-contrib-datadrift**
    + Déplacement de packages d’azureml-contrib-datadrift vers azureml-datadrift ; le package `contrib` sera supprimé dans une mise en production ultérieure

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + Introduction de FeaturizationConfig dans AutoMLConfig et AutoMLBaseSettings
    + Introduction de FeaturizationConfig dans [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) et AutoMLBaseSettings
      + Remplacement de l’objectif de la colonne pour caractérisation avec une colonne et un type de caractéristique donnés
      + Remplacement des paramètres du transformateur
    + Ajout d’un message de dépréciation pour explain_model() et retrieve_model_explanations()
    + Ajout de Prophet en tant que pipeline apte à l’apprentissage (préversion uniquement)
    + Ajout d’un message de dépréciation pour explain_model() et retrieve_model_explanations().
    + Ajout de Prophet en tant que pipeline apte à l’apprentissage (préversion uniquement).
    + Ajout de la prise en charge de la détection automatique des décalages cibles, de la taille de fenêtre dynamique et de l’horizon maximal. Si l’une des valeurs target_lags, target_rolling_window_size ou max_horizon est définie sur « auto », les heuristiques sont appliquées pour estimer la valeur du paramètre correspondant en fonction des données d’apprentissage.
    + Correction des prévisions lorsque le jeu de données contient une colonne de grain ; ce grain est de type numérique et il y a un écart entre l’apprentissage et le jeu de test
    + Correction du message d’erreur relatif à l’index dupliqué dans la série distante des tâches de prévision
    + Correction des prévisions lorsque le jeu de données contient une colonne de grain ; ce grain est de type numérique et il y a un écart entre l’apprentissage et le jeu de test.
    + Correction du message d’erreur relatif à l’index dupliqué dans la série distante des tâches de prévision.
    + Ajout d’une barrière de sécurité pour vérifier si un jeu de données est déséquilibré. Si c’est le cas, un message de barrière de sécurité s’affiche sur la console.
  + **azureml-core**
    + Ajout de la possibilité de récupérer l’URL SAP du modèle dans le stockage via l’objet de modèle Ex. : model.get_sas_url()
    + Introduction de `run.get_details()['datasets']` pour obtenir des jeux de données associés à l’exécution envoyée
    + Ajout de l’API `Dataset.Tabular.from_json_lines_files` pour créer un TabularDataset à partir de fichiers Lignes JSON. Pour découvrir ces données tabulaires dans les fichiers Lignes JSON sur TabularDataset, visitez la page https://aka.ms/azureml-data pour accéder à la documentation.
    + Ajout de champs de taille de machine virtuelle (disque du système d’exploitation, nombre de GPU) à la fonction supported_vmsizes ()
    + Ajout de champs à la fonction list_nodes () pour afficher l’exécution, les adresses IP privée et publique, le port, etc.
    + Possibilité de spécifier un nouveau champ pendant l’approvisionnement du cluster, --remotelogin_port_public_access, qui peut être défini sur activé ou désactivé selon que vous souhaitez laisser le port SSH ouvert ou fermé au moment de la création du cluster. Si vous ne le spécifiez pas, le service ouvre ou ferme intelligemment le port selon que vous déployez ou non le cluster à l’intérieur d’un réseau virtuel.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Ajout de la possibilité de récupérer l’URL SAP du modèle dans le stockage via l’objet de modèle Ex. : model.[get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduction de run.[get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] pour obtenir des jeux de données associés à l’exécution envoyée
    + Ajout de l’API `Dataset.Tabular`.[from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) pour créer un TabularDataset à partir de fichiers Lignes JSON. Pour découvrir ces données tabulaires dans les fichiers Lignes JSON sur TabularDataset, visitez la page https://aka.ms/azureml-data pour accéder à la documentation.
    + Ajout de champs de taille de machine virtuelle (disque du système d’exploitation, nombre de GPU) à la fonction [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Ajout de champs à la fonction [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) pour afficher l’exécution, les adresses IP privée et publique, le port, etc.
    + Possibilité de spécifier un nouveau champ pendant le [provisionnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) du cluster `--remotelogin_port_public_access`, qui peut être défini sur activé ou désactivé pour laisser le port SSH ouvert ou fermé au moment de la création du cluster. Si vous ne le spécifiez pas, le service ouvre ou ferme intelligemment le port selon que vous déployez ou non le cluster à l’intérieur d’un réseau virtuel.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Amélioration de la documentation pour les sorties d’explication dans le scénario de classification.
    + Ajout de la possibilité de charger les valeurs y prédites sur l’explication des exemples d’évaluation. Déverrouille des visualisations plus utiles.
    + Ajout de la propriété explainer à MimicWrapper pour permettre l’obtention du MimicExplainer sous-jacent.
  + **azureml-pipeline-core**
    + Ajout d’un bloc-notes pour décrire Module, ModuleVersion et ModuleStep
  + **azureml-pipeline-steps**
    + Ajout de RScriptStep pour prendre en charge l’exécution de script R via le pipeline AML.
    + Correction de l’analyse des paramètres de métadonnées dans AzureBatchStep qui générait le message d’erreur « Assignment for parameter SubscriptionId is not specified » (L’affectation du paramètre SubscriptionId n’est pas spécifiée).
  + **azureml-train-automl**
    + Prise en charge de training_data, validation_data, label_column_name, weight_column_name en tant que format d’entrée de données
    + Ajout d’un message de dépréciation pour explain_model() et retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Ajout d’un [bloc-notes](https://aka.ms/pl-modulestep) pour décrire [Module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) et [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Ajout de [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) pour prendre en charge l’exécution de script R via le pipeline AML.
    + Correction de l’analyse des paramètres de métadonnées dans [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) qui générait le message d’erreur « assignment for parameter SubscriptionId is not specified » (L’affectation du paramètre SubscriptionId n’est pas spécifiée).
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Prise en charge de training_data, validation_data, label_column_name, weight_column_name en tant que format d’entrée de données.
    + Ajout d’un message de dépréciation pour [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) et [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Kit SDK Azure Machine Learning pour Python v1.0.62

+ **Nouvelles fonctionnalités**
  + Introduction de la caractéristique `timeseries` sur TabularDataset. Cette caractéristique permet de filtrer facilement les horodatages sur les données TabularDataset, par exemple capturer toutes les données d’une plage de temps ou les données les plus récentes. Pour en savoir plus sur la caractéristique `timeseries` sur TabularDataset, rendez-vous sur https://aka.ms/azureml-data pour consulter la documentation ou sur https://aka.ms/azureml-tsd-notebook pour obtenir un exemple de notebook.
  + Activation de l’entraînement avec TabularDataset et FileDataset. Pour obtenir un exemple de notebook, consultez https://aka.ms/dataset-tutorial.

  + **azureml-train-core**
    + Ajout de la prise en charge de `Nccl` et `Gloo` dans PyTorch Estimator

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + Le paramètre AutoML « lag_length » et LaggingTransformer ont été dépréciés.
    + Correction de la validation des données d’entrée si elles sont spécifiées dans un format Dataflow
    + Modification de fit_pipeline.py pour générer le json de graphe et charger dans les artefacts.
    + Rendu du graphe sous `userrun` à l’aide de `Cytoscape`.
  + **azureml-core**
    + Nous avons révisé la gestion des exceptions dans le code ADB et apporté des modifications en fonction de la nouvelle gestion des erreurs
    + Ajout de l’authentification MSI automatique pour les machines virtuelles de notebook.
    + Correction d’un bogue où des modèles endommagés ou vides pouvaient être chargés en raison de l’échec des nouvelles tentatives.
    + Correction du bogue qui provoquait le changement du nom `DataReference` lors du changement du mode `DataReference` (par exemple lors de l’appel de `as_upload`, `as_download` ou `as_mount`).
    + `mount_point` et `target_path` sont désormais facultatifs pour `FileDataset.mount` et `FileDataset.download`.
    + Une exception indiquant que la colonne timestamp est introuvable sera levée si l’API associée aux séries chronologiques est appelée sans que la colonne timestamp fine soit affectée ou si les colonnes timestamp affectées sont supprimées.
    + Les colonnes de séries chronologiques doivent être assignées avec une colonne de type Date, sinon une exception est attendue.
    + Les colonnes de séries chronologiques affectant l’API « with_timestamp_columns » peuvent prendre la valeur None pour le nom de colonne timestamp fine/grossière, ce qui effacera les colonnes timestamp précédemment affectées.
    + Une exception est levée quand la colonne timestamp fine ou grossière est supprimée, avec une indication signalant à l’utilisateur que la suppression peut être effectuée soit après l’exclusion de la colonne timestamp de la liste de suppression, soit par l’appel de with_time_stamp avec une valeur None pour libérer les colonnes timestamp.
    + Une exception est levée quand la colonne timestamp fine ou grossière n’est pas incluse dans la liste de colonnes à conserver, avec une indication signalant à l’utilisateur que la conservation peut être effectuée soit après l’inclusion de la colonne timestamp dans la liste des colonnes à conserver, soit par l’appel de with_time_stamp avec une valeur None pour libérer les colonnes timestamp.
    + Ajout de la journalisation de la taille d’un modèle inscrit.
  + **azureml-explain-model**
    + Correction de l’avertissement affiché sur la console quand le package Python « packaging » n’est pas installé : « Utilisation d’une version antérieure à la version prise en charge de lightgbm. Veuillez effectuer une mise à niveau vers une version supérieure à 2.2.1 ».
    + Correction de l’explication relative au modèle de téléchargement avec partitionnement pour des explications globales avec de nombreuses fonctionnalités.
    + Correction des exemples MimicExplainer où il manquait l’initialisation lors de l’explication sur la sortie.
    + Correction d’une erreur immuable sur les propriétés de définition lors du chargement avec client d’explication utilisant deux types de modèles différents.
    + Ajout d’un paramètre get_raw à l’explainer de scoring .explain() afin qu’un explainer de scoring puisse retourner à la fois des valeurs de conception et des valeurs brutes.
  + **azureml-train-automl**
    + Introduction d’API publiques d’AutoML pour prendre en charge les explications à partir du SDK d’explication `automl`. Nouvelle manière de prendre en charge les explications AutoML en découplant la caractérisation AutoML et le SDK d’explication. Intégration de la prise en charge de l’explication brute à partir du SDK d’explication automl pour les modèles AutoML.
    + Suppression d’azureml-defaults des environnements d’entraînement à distance.
    + Modification de l’emplacement du magasin de cache par défaut de FileCacheStore en AzureFileCacheStore pour AutoML sur le chemin de code Azure Databricks.
    + Correction de la validation des données d’entrée si elles sont spécifiées dans un format Dataflow
  + **azureml-train-core**
    + Annulation de la dépréciation de source_directory_data_store.
    + Ajout de la possibilité de remplacer les versions de packages installés par azureml.
    + Ajout de la prise en charge de dockerfile dans le paramètre `environment_definition` dans les estimateurs.
    + Simplification des paramètres d’entraînement distribué dans les estimateurs.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nouvelle expérience web (préversion) pour les espaces de travail Azure Machine Learning
La nouvelle expérience web permet aux chercheurs de données et aux ingénieurs de données d’effectuer leur cycle de vie Machine Learning de bout en bout, de la préparation et la visualisation des données à la formation et au déploiement des modèles dans un emplacement unique.

![IU de l’espaces de travail Azure Machine Learning (préversion)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Fonctionnalités clés :**

À l’aide de cette nouvelle interface Azure Machine Learning, vous pouvez désormais :
+ Gérer vos notebooks ou le lien vers Jupyter
+ [Exécuter des expériences de Machine Learning automatisé](tutorial-first-experiment-automated-ml.md)
+ [Créer des jeux de données à partir de fichiers locaux, de banques de données et de fichiers web](how-to-create-register-datasets.md)
+ Explorer et préparer les jeux de données pour la création de modèles
+ Surveiller la dérive des données pour vos modèles
+ Afficher les ressources récentes à partir d’un tableau de bord

Au moment de cette publication, les navigateurs suivants sont pris en charge : Chrome, Firefox, Safari et la préversion Microsoft Edge.

**Problèmes connus :**

1. Actualisez votre navigateur si vous voyez « Une erreur s’est produite ! Erreur lors du chargement des fichiers blocs » lorsque le déploiement est en cours.

1. Impossible de supprimer ou de renommer un fichier dans des notebooks et des fichiers. Pendant la préversion publique, vous pouvez utiliser le terminal ou l’interface utilisateur Jupyter sur la machine virtuelle Notebook pour effectuer les opérations de mise à jour des fichiers. Étant donné qu’il s’agit d’un système de fichiers réseau monté, toutes les modifications que vous apportez à la machine virtuelle Notebook sont immédiatement reflétées dans l’espace de travail Notebook.

1. Pour une connexion SSH à la machine virtuelle Notebook :
   1. Recherchez les clés SSH qui ont été créées lors de la configuration de la machine virtuelle. Ou, recherchez les clés dans l’espace de travail Azure Machine Learning > ouvrez l’onglet Calcul > localisez la machine virtuelle Notebook dans la liste > ouvrez la boîte de dialogue Propriétés : copiez les clés à partir de la boîte de dialogue.
   1. Importez ces clés SSH publiques et privées sur votre ordinateur local.
   1. Utilisez-les pour effectuer une connexion SSH à la machine virtuelle Notebook.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.60

+ **Nouvelles fonctionnalités**
  + Ajout de FileDataset, qui fait référence à des fichiers uniques ou multiples dans vos magasins de fichiers ou vos URL publiques. Les fichiers peuvent être de n’importe quel format. FileDataset vous offre la possibilité de télécharger ou de monter les fichiers dans votre calcul. Pour en savoir plus sur FileDataset, consultez https://aka.ms/file-dataset.
  + Ajout de la prise en charge du YAML de pipeline pour les étapes PythonScript, Adla, Databricks, DataTransfer et AzureBatch

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + AutoArima est désormais un pipeline suggéré pour la préversion uniquement.
    + Amélioration des rapports d’erreurs pour les prévisions.
    + Amélioration de la journalisation à l’aide d’exceptions personnalisées plutôt que génériques dans les tâches de prévision.
    + La vérification sur max_concurrent_iterations a été supprimée pour être inférieure au nombre total d’itérations.
    + Les modèles AutoML retournent désormais AutoMLExceptions
    + Cette version améliore les performances d’exécution des exécutions locales de Machine Learning automatisé.
  + **azureml-core**
    + Ajoute Dataset.get_all(workspace), qui retourne un dictionnaire d'objets `TabularDataset` et `FileDataset` indexés par nom d’inscription.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Ajout de `parition_format` comme argument pour `Dataset.Tabular.from_delimited_files` et `Dataset.Tabular.from_parquet.files`. Les informations de partition de chaque chemin de données sont extraites dans des colonnes en fonction du format spécifié. « {column_name} » crée une colonne de chaîne, et « {column_name:yyyy/MM/dd/HH/mm/ss} » crée une colonne DateTime, où « yyyy », « MM », « dd », « HH », « mm » et « ss » sont utilisés pour extraire l’année, le mois, le jour, l’heure, la minute et la seconde pour le type DateTime. partition_format doit commencer à partir de la position de la première clé de partition et se poursuivre jusqu’à la fin du chemin d’accès au fichier. Par exemple, si l’on prend le chemin d’accès '../USA/2019/01/01/data.csv' où la partition se fait par pays et par heure, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' crée la colonne chaîne « Country » avec la valeur « USA » et la colonne DateTime « PartitionDate » avec la valeur « 2019-01-01 ».
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Ajout de `partition_format` comme argument pour `Dataset.Tabular.from_delimited_files` et `Dataset.Tabular.from_parquet.files`. Les informations de partition de chaque chemin de données sont extraites dans des colonnes en fonction du format spécifié. « {column_name} » crée une colonne de chaîne, et « {column_name:yyyy/MM/dd/HH/mm/ss} » crée une colonne DateTime, où « yyyy », « MM », « dd », « HH », « mm » et « ss » sont utilisés pour extraire l’année, le mois, le jour, l’heure, la minute et la seconde pour le type DateTime. partition_format doit commencer à partir de la position de la première clé de partition et se poursuivre jusqu’à la fin du chemin d’accès au fichier. Par exemple, si l’on prend le chemin d’accès '../USA/2019/01/01/data.csv' où la partition se fait par pays et par heure, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' crée la colonne chaîne « Country » avec la valeur « USA » et la colonne DateTime « PartitionDate » avec la valeur « 2019-01-01 ».
    + Les méthodes `to_csv_files` et `to_parquet_files` ont été ajoutées à `TabularDataset`. Ces méthodes permettent la conversion entre `TabularDataset` et `FileDataset` en convertissant les données en fichiers au format spécifié.
    + Connectez-vous automatiquement au registre d’images de base lors de l’enregistrement d’un fichier Dockerfile généré par Model.package().
    + « gpu_support » n’est plus nécessaire ; AML détecte et utilise désormais automatiquement l’extension Docker Nvidia lorsqu’elle est disponible. Elle sera supprimée dans une version ultérieure.
    + Ajout de la prise en charge de la création, de la mise à jour et de l’utilisation de PipelineDrafts.
    + Cette version améliore les performances d’exécution des exécutions locales de Machine Learning automatisé.
    + Les utilisateurs peuvent interroger les métriques à partir de l’historique des exécutions par nom.
    + Amélioration de la journalisation à l’aide d’exceptions personnalisées plutôt que génériques dans les tâches de prévision.
  + **azureml-explain-model**
    + Ajout du paramètre feature_maps au nouveau MimicWrapper, pour permettre aux utilisateurs d’obtenir des explications sur les fonctionnalités brutes.
    + Les chargements de jeux de données sont maintenant désactivés par défaut pour le téléchargement d’explications et peuvent être réactivés avec upload_datasets=True
    + Ajout de paramètres de filtrage « is_law » à la liste d’explications et aux fonctions de téléchargement.
    + Ajoute la méthode `get_raw_explanation(feature_maps)` aux objets d’explication globaux et locaux.
    + Ajout de la vérification de version à lightgbm avec un avertissement imprimé si elle est inférieure à la version prise en charge
    + Utilisation optimisée de la mémoire lors du traitement des explications par lots
    + Les modèles AutoML retournent désormais AutoMLExceptions
  + **azureml-pipeline-core**
    + Ajout de la prise en charge de la création, de la mise à jour et de l’utilisation de PipelineDrafts - peut servir à gérer les définitions de pipeline mutable et les utiliser de manière interactive pour exécuter
  + **azureml-train-automl**
    + Création d’une fonctionnalité pour installer des versions spécifiques de pytorch v1.1.0, :::no-loc text="cuda"::: Toolkit 9.0, pytorch-transformers utilisant le processeur graphique, ce qui est nécessaire à l’activation de BERT/XLNet dans l’environnement d’exécution Python distant.
  + **azureml-train-core**
    + Détermination précoce de l’échec pour certaines erreurs de définition d’espace d’hyperparamètre directement dans le kit de développement logiciel (SDK) et non côté serveur.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.14
+ **Résolutions de bogue et améliorations**
  + Activation de l’écriture dans ADLS/ADLSGen2 à l’aide du chemin d’accès brut et des informations d’identification.
  + Correction d’un bogue à cause duquel `include_path=True` ne fonctionnait pas pour `read_parquet`.
  + Résolution de l’échec `to_pandas_dataframe()` provoqué par l’exception « Valeur de propriété non valide : hostSecret ».
  + Correction d’un bogue dans lequel les fichiers ne pouvaient pas être lus sur DBFS en mode Spark.

## <a name="2019-08-19"></a>19/08/2019

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.57
+ **Nouvelles fonctionnalités**
  + Activation de `TabularDataset` pour consommation par AutomatedML. Pour en savoir plus sur `TabularDataset`, consultez https://aka.ms/azureml/howto/createdatasets.

+ **Résolutions de bogue et améliorations**
  + **automl-client-core-nativeclient**
    + Correction de l’erreur déclenchée lorsque des étiquettes d’apprentissage et/ou de validation (y et y_valid) sont fournies sous la forme de trames de données Pandas, mais pas en tant que tableau numpy.
    + Interface mise à jour pour créer un `RawDataContext` pour exiger uniquement les données et l’objet `AutoMLBaseSettings`.
    +  Autoriser les utilisateurs AutoML à supprimer des séries d’apprentissage qui ne sont pas suffisamment longues lors des prévisions. - Autoriser les utilisateurs AutoML à supprimer des grains du jeu de test qui n’existe pas dans le jeu d’apprentissage lors de la prévision.
  + **azure-cli-ml**
    + Vous pouvez maintenant mettre à jour le certificat SSL pour le point de terminaison de score déployé sur le cluster AKS pour les certificats générés par Microsoft et par le client.
  + **azureml-automl-core**
    + Résolution d’un problème dans AutoML où les lignes avec des étiquettes manquantes ne sont pas correctement supprimées.
    + Amélioration de la journalisation des erreurs dans AutoML. Les messages d’erreur complets seront désormais toujours écrits dans le fichier journal.
    + AutoML a mis à jour son épinglage de package pour inclure `azureml-defaults`, `azureml-explain-model` et`azureml-dataprep`. AutoML n’avertira plus en cas d’incompatibilité de package (à l’exception du package `azureml-train-automl`).
    + Correction d’un problème dans `timeseries` où les fractionnements du CV sont de taille inégale, ce qui provoque l’échec du calcul des fichiers binaires.
    + Lors de l’exécution de l’ensemble d’itération pour le type d’apprentissage de validation croisée, si nous avons rencontré un problème lors du téléchargement des modèles formés sur l’ensemble du jeu de données, nous avons une incohérence entre la pondération des modèles et les modèles qui ont été intégrés à l’ensemble de vote.
    + Correction de l’erreur déclenchée lorsque des étiquettes d’apprentissage et/ou de validation (y et y_valid) sont fournies sous la forme de trames de données Pandas, mais pas en tant que tableau numpy.
    + Résolution du problème lié aux tâches de prévision quand Aucune a été trouvé dans les colonnes booléennes des tables d’entrée.
    + Autoriser les utilisateurs AutoML à supprimer des séries d’apprentissage qui ne sont pas suffisamment longues lors des prévisions. - Autoriser les utilisateurs AutoML à supprimer des grains du jeu de test qui n’existe pas dans le jeu d’apprentissage lors de la prévision.
  + **azureml-core**
    + Correction du problème de classement du paramètre blob_cache_timeout.
    + Ajout des types d’exceptions d’ajustement externe et de transformation aux erreurs système.
    + Ajout de la prise en charge des secrets Key Vault pour les exécutions distantes. Ajout d’une classe azureml.core.keyvault.Keyvault pour ajouter, obtenir et répertorier les secrets du coffre de clés associé à votre espace de travail. Opérations prises en charge :
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(name, value)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Méthodes supplémentaires pour obtenir un coffre de clés par défaut et obtenir des secrets lors de l’exécution distante :
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Ajout de paramètres de substitution supplémentaires à la commande submit-hyperdrive de la CLI.
    + Amélioration de la fiabilité des appels d’API pour étendre les nouvelles tentatives aux exceptions de la bibliothèque de demandes courantes.
    + Ajout de la prise en charge de l’envoi des exécutions à partir d’une exécution envoyée.
    + Correction du problème de jeton SAS arrivant à expiration dans FileWatcher, ce qui provoquait l’arrêt du chargement des fichiers après l’expiration de leur jeton initial.
    + Importation des fichiers CSV/TSV HTTP pris en charge dans le SDK Python du jeu de données.
    + Méthode Workspace.Setup() déconseillée. Le message d’avertissement présenté aux utilisateurs suggère d’utiliser create() ou get()/from_config().
    + Ajout de Environment.add_private_pip_wheel(), qui permet de charger des packages Python privés et personnalisés `whl` dans l’espace de travail et de les utiliser en toute sécurité pour générer/matérialiser l’environnement.
    + Vous pouvez maintenant mettre à jour le certificat SSL pour le point de terminaison de score déployé sur le cluster AKS pour les certificats générés par Microsoft et par le client.
  + **azureml-explain-model**
    + Ajout d’un paramètre pour ajouter un ID de modèle aux explications lors du chargement.
    + Ajout du balisage `is_raw` aux explications en mémoire et au chargement.
    + Ajout de la prise en charge et des tests pytorch pour le package azureml-explain-model.
  + **azureml-opendatasets**
    + Prise en charge de la détection et de la journalisation de l’environnement de test automatique.
    + Ajout de classes pour obtenir la population des États-Unis par comté et par code postal.
  + **azureml-pipeline-core**
    + Ajout de la propriété label aux définitions de port d’entrée et de sortie.
  + **azureml-telemetry**
    + Correction de la configuration de télémétrie incorrecte.
  + **azureml-train-automl**
    + Correction du bogue à l’origine de l’échec de l’installation, l’erreur n’étant pas consignée dans le champ Erreurs de l’exécution et donc pas stockée dans le champ Erreurs du parent.
    + Résolution d’un problème dans AutoML où les lignes avec des étiquettes manquantes ne sont pas correctement supprimées.
    + Autoriser les utilisateurs AutoML à supprimer des séries d’apprentissage qui ne sont pas suffisamment longues lors des prévisions.
    + Autoriser les utilisateurs AutoML à supprimer des grains du jeu de test qui n’existent pas dans le jeu d’apprentissage lors de la prévision.
    + Désormais, AutoMLStep passe par la configuration `automl` à backend pour éviter tout problème concernant la modification ou l’ajout de paramètres de configuration.
    + AutoML Data Guardrail est désormais en préversion publique. L’utilisateur verra un rapport Data Guardrail (pour les tâches de classification/régression) après l’apprentissage et pourra également y accéder via l’API du SDK.
  + **azureml-train-core**
    + Ajout de la prise en charge de Torch 1.2 dans PyTorch Estimator.
  + **azureml-widgets**
    + Amélioration des graphiques de la matrice de confusion pour l’apprentissage de la classification.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.12
+ **Nouvelles fonctionnalités**
  + Les listes de chaînes peuvent maintenant être passées comme entrée des méthodes `read_*`.

+ **Résolutions de bogue et améliorations**
  + Les performances de `read_parquet` ont été considérablement améliorées lors de l’exécution dans Spark.
  + Résolution d’un problème où `column_type_builder` échouait en cas de colonne unique avec des formats de date ambigus.

### <a name="azure-portal"></a>Portail Azure
+ **Fonctionnalité en préversion**
  + La diffusion en continu des fichiers journaux et des fichiers de sortie est désormais disponible pour les pages de détail de l’exécution. Ces fichiers diffusent des mises à jour en temps réel lorsque l’aperçu est activé.
  + La possibilité de définir un quota au niveau de l’espace de travail est publiée en version préliminaire. Les quotas AmlCompute sont alloués au niveau de l’abonnement, mais nous vous permettons désormais de distribuer ces quotas entre les espaces de travail et de les allouer pour un partage et une gouvernance équitables. Cliquez simplement sur le panneau **Utilisations+quotas** dans la barre de navigation de gauche de votre espace de travail, puis sélectionnez l’onglet **Configurer les quotas**. Notez que vous devez être administrateur d’abonnement pour pouvoir définir des quotas au niveau de l’espace de travail, car il s’agit d’une opération entre espaces de travail.

## <a name="2019-08-05"></a>05-08-2019

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.55

+ **Nouvelles fonctionnalités**
  + L’authentification basée sur des jetons est désormais prise en charge pour les appels établis vers le point de terminaison de scoring déployé sur AKS. Nous continuons de prendre en charge l’authentification par clé actuelle, et les utilisateurs peuvent utiliser un de ces mécanismes d’authentification à la fois.
  + Possibilité d’inscrire un stockage blob situé derrière le réseau virtuel en tant que banque de données.

+ **Résolutions de bogue et améliorations**
  + **azureml-automl-core**
    + Corrige un bogue où la taille de validation pour les fractionnements CV est petite et entraîne des prédictions erronées par rapport aux graphiques réels pour la régression et la prévision.
    + La journalisation des tâches de prévision sur les exécutions distantes a été améliorée. Désormais, l’utilisateur reçoit un message d’erreur complet en cas d’échec d’exécution.
    + Correction d’échecs de `Timeseries` si la valeur de l’indicateur de prétraitement est True.
    + Messages d’erreur de validation des données de prévision rendus plus actionnables.
    + Réduction de la consommation de mémoire des exécutions AutoML par la suppression ou le chargement différé de jeux de données, en particulier entre les déclenchements de processus
  + **azureml-contrib-explain-model**
    + Ajout de l’indicateur model_task aux explications afin de permettre à l’utilisateur de modifier la logique d’inférence automatique par défaut pour le type de modèle
    + Modifications du widget : Installation automatique avec `contrib`, sans plus aucune installation/activation de `nbextension` – Explication de prise en charge avec seulement l’importance globale de la fonctionnalité (par exemple, Permutative)
    + Modifications du tableau de bord : Diagrammes en boîte et en violon, en plus du diagramme `beeswarm` sur la page de résumé – Réitération beaucoup plus rapide du diagramme `beeswarm` en cas de déplacement du curseur « top-k » – Message utile expliquant le mode de calcul de top-k – Messages personnalisables utiles au lieu de graphiques en l’absence de données
  + **azureml-core**
    + Ajout de la méthode Model.package() pour créer des images Docker et des Dockerfiles qui encapsulent des modèles et leurs dépendances.
    + Mise à jour des services web locaux pour accepter des InferenceConfigs contenant des objets Environnement.
    + Model.Register() fixe produisant des modèles non valides quand ’.’ (pour le répertoire actif) est transmis en tant que paramètre model_path.
    + Ajout de Run.submit_child. La fonctionnalité reflète Experiment.submit lors de la spécification de l’exécution en tant que parent de l’exécution enfant envoyée.
    + Prise en charge des options de configuration de Model.Register dans Run.register_model.
    + Possibilité d’exécuter des travaux JAR sur un cluster existant.
    + Prise en charge des paramètres instance_pool_id et cluster_log_dbfs_path.
    + Ajout de la prise en charge de l’utilisation d’un objet Environnement lors du déploiement d’un modèle sur un service web. L’objet Environnement peut désormais être fourni en tant que composant de l’objet InferenceConfig.
    + Ajout de mappage appinsifht pour les nouvelles régions centralus - westus - northcentralus
    + Ajout de documentation pour tous les attributs de toutes les classes de banque de données.
    + Ajout du paramètre blob_cache_timeout à `Datastore.register_azure_blob_container`.
    + Ajout des méthodes save_to_directory et load_from_directory à azureml.core.environment.Environment.
    + Ajout des commandes « az ml environment show » et « az ml environment list » à l’interface de ligne de commande.
    + Ajout de la méthode Environment.add_private_pip_wheel.
  + **azureml-explain-model**
    + Ajout du suivi des jeux de données aux explications à l’aide du service de jeu de données (préversion).
    + Réduction de la taille de lot par défaut lors de la diffusion en continu d’explications globales de 10 à 100 Ko.
    + Ajout de l’indicateur model_task aux explications pour permettre à l’utilisateur de modifier la logique d’inférence automatique par défaut pour le type de modèle.
  + **azureml-mlflow**
    + Correction du bogue dans mlflow.azureml.build_image qui avait pour effet que des répertoires imbriqués étaient ignorés.
  + **azureml-pipeline-steps**
    + Ajout de la possibilité d’exécuter des travaux JAR sur un cluster Azure Databricks existant.
    + Ajout de la prise en charge des paramètres instance_pool_id et cluster_log_dbfs_path pour l’étape DatabricksStep.
    + Ajout de la prise en charge des paramètres de pipeline dans l’étape DatabricksStep.
  + **azureml-train-automl**
    + Ajout de `docstrings` pour les fichiers associés à l’ensemble.
    + Mise à jour de la documentation dans un langage plus approprié pour `max_cores_per_iteration` et `max_concurrent_iterations`
    + La journalisation des tâches de prévision sur les exécutions distantes a été améliorée. Désormais, l’utilisateur reçoit un message d’erreur complet en cas d’échec d’exécution.
    + Suppression de get_data du bloc-notes `automlstep` du pipeline.
    + Début de la prise en charge `dataprep` dans `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Kit de développement logiciel (SDK) v1.1.10 pour la préparation de données Azure Machine Learning

+ **Nouvelles fonctionnalités**
  + Vous pouvez désormais demander d’exécuter des inspecteurs spécifiques (par exemple, histogramme, nuage de points, etc.) sur des colonnes spécifiques.
  + Ajout d’un argument de parallélisation à `append_columns`. Si la valeur est True, les données sont chargées en mémoire, mais l’exécution se fait en parallèle. Si la valeur est False, l’exécution se fait en continu, mais dans un thread unique.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>SDK Azure Machine Learning pour Python v1.0.53

+ **Nouvelles fonctionnalités**
  + Le machine learning automatisé prend désormais en charge l’entraînement de modèles ONNX sur la cible de calcul distante.
  + Azure Machine Learning offre maintenant la possibilité de reprendre l’entraînement à partir d’une exécution précédente, d’un point de contrôle ou de fichiers de modèle.
    + Découvrez comment [utiliser des estimateurs pour reprendre l’entraînement à partir d’une exécution précédente](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb).

+ **Résolutions de bogue et améliorations**
  + **automl-client-core-nativeclient**
    + Correction du bogue concernant la perte des types de colonnes après la transformation (bogue lié)
    + Possibilité pour y_query d’être un type d’objet débutant par « None(s) » (n° 459519)
  + **azure-cli-ml**
    + Les commandes CLI « model deploy » et « service update » acceptent désormais les paramètres, les fichiers de configuration ou une combinaison des deux. Les paramètres ont priorité sur les attributs des fichiers.
    + La description du modèle peut désormais être mise à jour après l’inscription.
  + **azureml-automl-core**
    + Mise à jour de la dépendance NimbusML vers la version 1.2.0 (actuellement la version la plus récente)
    + Ajout de la prise en charge des pipelines et des estimateurs ML Nimbus à utiliser dans les estimateurs AutoML.
    + Correction d’un bogue dans la procédure de sélection d’ensemble qui ne faisait pas nécessairement croître l’ensemble résultant même si les scores restaient constants
    + Possibilité de réutiliser certaines caractérisations dans les divisions de validation croisée pour les tâches de prévision. Ceci accélère l’exécution du programme d’installation avec un facteur approximatif de n_cross_validations pour les caractérisations coûteuses (décalages et fenêtres dynamiques, par exemple).
    + Résolution d’un problème se produisant quand l’heure est hors de la plage horaire prise en charge par Pandas. Nous levons maintenant une exception DataException si l’heure est antérieure à pd.Timestamp.min ou postérieure à pd.Timestamp.max.
    + La prévision autorise désormais des fréquences différentes dans les jeux d’entraînement et de test s’ils peuvent être alignés. Par exemple, « tous les trimestres à partir de janvier » et « tous les trimestres à partir d’octobre » peuvent être alignés.
    + La propriété « parameters » a été ajoutée à TimeSeriesTransformer.
    + Les anciennes classes d’exception ont été supprimées.
    + Dans les tâches de prévision, le paramètre `target_lags` accepte désormais une valeur entière unique ou une liste d’entiers. Si l’entier est fourni, un seul décalage est créé. Si une liste est fournie, les valeurs uniques des décalages sont utilisées. target_lags=[1, 2, 2, 4] crée des décalages de 1, 2 et 4 périodes.
    + Correction du bogue concernant la perte des types de colonnes après la transformation (bogue lié)
    + Dans `model.forecast(X, y_query)`, possibilité pour y_query d’être un type d’objet débutant par « None(s) » (n° 459519).
    + Ajout des valeurs attendues à la sortie `automl`
  + **azureml-contrib-datadrift**
    +  Améliorations apportées à l’exemple de notebook, notamment le basculement vers azureml-opendatasets au lieu de azureml-contrib-opendatasets et amélioration des performances lors de l’enrichissement des données
  + **azureml-contrib-explain-model**
    + Correction de l’argument des transformations pour l’explicatif LIME pour l’importance des caractéristiques brutes dans le package azureml-contrib-explain-model
    + Ajout de segmentations aux explications d’image dans l’explicatif d’image pour le package AzureML-contrib-explain-model
    + Ajout d’une prise en charge partielle de SciPy pour LimeExplainer
    + Ajout de `batch_size` pour imiter Explainer quand `include_local=False`, pour diffuser en continu des explications globales par lots afin d’améliorer le temps d’exécution de DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correction pour l’appel de set_featurizer_timeseries_params () : contrôle de valeur Null et modification du type de valeur dict - Ajout d’un notebook pour le caractériseur `timeseries`
    + Mise à jour de la dépendance NimbusML vers la version 1.2.0 (actuellement la version la plus récente)
  + **azureml-core**
    + Ajout de la possibilité de joindre des banques de données DBFS dans AzureML CLI
    + Correction du bogue concernant le chargement des banques de données quand un dossier vide est créé si `target_path` commence par `/`
    + Correction du problème avec `deepcopy` dans ServicePrincipalAuthentication.
    + Ajout des commandes « az ml environment show » et « az ml environment list » à l’interface CLI
    + Les environnements prennent maintenant en charge la spécification d’un base_dockerfile comme alternative à un base_image déjà créé.
    + Le paramètre RunConfiguration inutilisé dans auto_prepare_environment a été marqué comme déprécié.
    + La description du modèle peut désormais être mise à jour après l’inscription.
    + Résolution de bogue : Le modèle et la suppression d’image fournissent désormais plus d’informations sur la récupération des objets en amont qui en dépendent en cas d’échec de la suppression en raison d’une dépendance amont.
    + Correction du bogue entraînant l’impression d’une durée vide pour les déploiements lors de la création d’un espace de travail pour certains environnements
    + Amélioration des exceptions d’échec de création d’espace de travail. Les utilisateurs ne voient plus le message « Impossible de créer l’espace de travail. Impossible de trouver... ». Ils voient désormais l’échec de création réel.
    + Ajout de la prise en charge de l’authentification de jeton dans les services web AKS
    + Ajout de la méthode `get_token()` aux objets `Webservice`
    + Ajout de la prise en charge de l’interface CLI pour gérer les jeux de données de machine learning
    + Désormais, `Datastore.register_azure_blob_container` utilise une valeur `blob_cache_timeout` (en secondes) qui configure les paramètres de montage de Blobfuse pour activer l’expiration du cache pour cette banque de données. Par défaut, aucun délai d’expiration n’est appliqué : quand un blob est lu, il reste dans le cache local jusqu’à ce que le travail soit terminé. Ce paramètre est préférable pour la plupart des travaux, mais certains travaux doivent lire plus de données que ne peuvent l’accepter leurs nœuds à partir d’un grand jeu de données. La définition de ce paramètre favorise la réussite de ces travaux. Soyez vigilant lors de la définition de ce paramètre : une valeur trop faible peut altérer les performances, car les données utilisées à une époque peuvent expirer avant d’être réutilisées. Ainsi, toutes les lectures seront effectuées à partir du Stockage Blob (c’est-à-dire à partir du réseau) et non du cache local, entraînant un impact négatif sur les durées d’entraînement.
    + La description du modèle peut désormais être mise à jour correctement après l’inscription.
    + Lors de la suppression de modèle et d’image, de plus amples informations sont désormais fournies concernant les objets en amont qui en dépendent, entraînant l’échec de la suppression.
    + Utilisation améliorée des ressources des exécutions à distance qui utilisent azureml.mlflow
  + **azureml-explain-model**
    + Correction de l’argument des transformations pour l’explicatif LIME pour l’importance des caractéristiques brutes dans le package azureml-contrib-explain-model
    + Ajout d’une prise en charge partielle de SciPy pour LimeExplainer
    + Ajout d’un wrapper explicatif linéaire de forme et d’un autre niveau à l’explicatif tabulaire pour l’explication des modèles linéaires
    + Pour l’explicatif d’imitation dans la bibliothèque de modèles d’explication, correction de l’erreur avec include_local = false pour l’entrée de données éparses
    + Ajout des valeurs attendues à la sortie `automl`
    + Correction de l’importance des caractéristiques par permutation quand l’argument des transformations est fourni pour bénéficier de l’importance des caractéristiques brutes
    + Ajout de `batch_size` à reproduire quand `include_local=False` pour le streaming d’explications globales par lots afin d’améliorer le temps d’exécution de DecisionTreeExplainableModel
    + Pour la bibliothèque d’explicabilité de modèle, correction des explicatifs de boîte noire quand l’entrée de dataframes Pandas est nécessaire pour la prédiction
    + Correction du bogue suivant : `explanation.expected_values` retourne parfois une valeur flottante plutôt qu’une liste avec une valeur flottante.
  + **azureml-mlflow**
    + Amélioration du niveau de performance de mlflow.set_experiment(experiment_name)
    + Correction du bogue concernant l’utilisation d’InteractiveLoginAuthentication pour le tracking_uri mlflow
    + Utilisation améliorée des ressources des exécutions à distance qui utilisent azureml.mlflow
    + Amélioration de la documentation du package azureml-mlflow
    + Correction du bogue suivant : mlflow.log_artifacts (« my_dir ») enregistrait les artefacts sous « my_dir/<artifact-paths> » au lieu de « <artifact-paths> ».
  + **azureml-opendatasets**
    + Ajout de la possibilité d’épingler `pyarrow` de `opendatasets` aux anciennes versions (antérieures à la version 0.14.0) en raison d’un problème de mémoire.
    + Déplacement d’azureml-contrib-opendatasets vers azureml-opendatasets.
    + Les classes de jeu de données ouvertes peuvent être inscrites auprès de l’espace de travail Azure Machine Learning et tirer parti des fonctionnalités de jeu de données AML de manière intégrée.
    + L’amélioration de NoaaIsdWeather enrichie sensiblement les performances de la version non SPARK.
  + **azureml-pipeline-steps**
    + La banque de données DBFS est maintenant prise en charge pour les entrées et les sorties dans DatabricksStep.
    + Mise à jour de la documentation d’Azure Batch Step concernant les entrées/sorties
    + Dans AzureBatchStep, modification de la valeur par défaut *delete_batch_job_after_finish* sur *true*.
  + **azureml-telemetry**
    +  Déplacement d’azureml-contrib-opendatasets vers azureml-opendatasets.
    + Les classes de jeu de données ouvertes peuvent être inscrites auprès de l’espace de travail Azure Machine Learning et tirer parti des fonctionnalités de jeu de données AML de manière intégrée.
    + L’amélioration de NoaaIsdWeather enrichie sensiblement les performances de la version non SPARK.
  + **azureml-train-automl**
    + Mise à jour de la documentation concernant get_output pour refléter le type de retour réel et fournir des informations supplémentaires sur la récupération des propriétés de clé
    + Mise à jour de la dépendance NimbusML vers la version 1.2.0 (actuellement la version la plus récente)
    + Ajout des valeurs attendues à la sortie `automl`
  + **azureml-train-core**
    + Les chaînes sont maintenant acceptées en tant que cible de calcul pour le paramétrage automatisé des hyperparamètres.
    + Le paramètre RunConfiguration inutilisé dans auto_prepare_environment a été marqué comme déprécié.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>SDK de préparation de données Azure Machine Learning v1.1.9

+ **Nouvelles fonctionnalités**
  + Ajout de la prise en charge de la lecture de fichier directement à partir d’une URL http ou https

+ **Résolutions de bogue et améliorations**
  + Amélioration du message d’erreur affiché lors d’une tentative de lecture d’un jeu de données Parquet à partir d’une source distante (qui n’est pas encore prise en charge)
  + Correction d’un bogue se produisant lors de l’écriture vers le format de fichier Parquet dans ADLS Gen 2 et de la mise à jour du nom de conteneur ADLS Gen 2 dans le chemin.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interface visuelle
+ **Fonctionnalités préliminaires**
  + Ajout du module « Exécuter le script R » dans l’interface visuelle.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.48

+ **Nouvelles fonctionnalités**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** est maintenant disponible en tant que **azureml-opendatasets**. L’ancien package peut continuer à fonctionner, mais nous vous recommandons d’utiliser **azureml-opendatasets** pour des fonctionnalités et des améliorations plus riches.
    + Ce nouveau package vous permet d’inscrire des jeux de données ouverts en tant que Dataset dans un espace de travail Azure Machine Learning et de tirer parti des fonctionnalités offertes par le jeu de données.
    + Il comprend également des fonctionnalités existantes, telles que l’utilisation de jeux de données ouverts en tant que dataframes Pandas/SPARK et les jointures d’emplacement pour certains jeux de données tels que la météo.

+ **Fonctionnalités préliminaires**
    + HyperDriveConfig peut désormais accepter l’objet de pipeline en tant que paramètre pour prendre en charge l’hyperparamètres à l’aide d’un pipeline.

+ **Résolutions de bogue et améliorations**
  + **azureml-train-automl**
    + Correction du bogue concernant la perte des types de colonnes après la transformation.
    + Correction du bogue pour permettre à y_query d’être un type d’objet débutant par « Aucun(s) ».
    + Correction du problème dans la procédure de sélection d’ensemble qui ne faisait pas nécessairement croître l’ensemble résultant même si les scores sont restés constants.
    + Correction du problème avec les paramètres whitelist_models et blacklist_models dans AutoMLStep.
    + Correction du problème qui empêchait l’utilisation du prétraitement lorsqu’AutoML aurait été utilisé dans le contexte des pipelines Azure ML.
  + **azureml-opendatasets**
    + Déplacement d’azureml-contrib-opendatasets vers azureml-opendatasets.
    + Les classes de jeu de données ouvertes peuvent être inscrites auprès de l’espace de travail Azure Machine Learning et tirer parti des fonctionnalités de jeu de données AML de manière intégrée.
    + Les NoaaIsdWeather améliorées enrichissent sensiblement les performances de la version non SPARK.
  + **azureml-explain-model**
    + Mise à jour de la documentation en ligne des objets d’interprétation.
    + Ajout de `batch_size` à reproduire lorsque `include_local=False` pour le streaming d’explications globales par lots afin d’améliorer le temps d’exécution de DecisionTreeExplainableModel pour la bibliothèque d’optimisation des modèles.
    + Correction du problème où `explanation.expected_values` retourne parfois une valeur flottante plutôt qu’une liste avec une valeur flottante.
    + Ajout des valeurs attendues à la sortie `automl` pour reproduire l’imitateur dans l’explication du modèle de bibliothèque.
    + Correction de la fonctionnalité de permutation lorsque l’argument des transformations est fourni pour bénéficier de l’importance des fonctionnalités brutes.
  + **azureml-core**
    + Ajout de la possibilité d’attacher des banques de données DBFS dans le AzureML CLI.
    + Correction du problème de chargement des magasins de stockage lorsqu’un dossier vide a été créé si `target_path` commençait par `/`.
    + Activation de la comparaison des deux jeux de données.
    + Le modèle et la suppression d’image fournissent désormais plus d’informations sur la récupération des objets en amont qui en dépendent en cas d’échec de la suppression en raison d’une dépendance amont.
    + Le paramètre RunConfiguration inutilisé dans auto_prepare_environment a été déconseillé.
  + **azureml-mlflow**
    + Utilisation améliorée des ressources des exécutions à distance qui utilisent azureml.mlflow.
    + Amélioration de la documentation du package azureml-mlflow.
    + Correction du problème où mlflow.log_artifacts (« my_dir ») enregistrait les artefacts sous « my_dir/artefact-paths » au lieu de « artefact-paths ».
  + **azureml-pipeline-core**
    + Le paramètre hash_paths pour toutes les étapes de pipeline est déconseillé et sera supprimé à l’avenir. Par défaut, le contenu de source_directory est haché (à l’exception des fichiers listés dans.amlignore ou.gitignore)
    + L’amélioration continue de Module et de ModuleStep pour prendre en charge des modules de calcul spécifiques, en préparation de l’intégration de RunConfiguration et de modifications supplémentaires pour déverrouiller l’utilisation de modules de calcul spécifiques dans les pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep : documentation améliorée en ce qui concerne les entrées/sorties.
    + AzureBatchStep : modification de la valeur par défaut delete_batch_job_after_finish sur true.
  + **azureml-train-core**
    + Les chaînes sont maintenant acceptées en tant que cible de calcul pour le paramétrage automatisé des hyperparamètres.
    + Le paramètre RunConfiguration inutilisé dans auto_prepare_environment a été déconseillé.
    + Paramètres déconseillés `conda_dependencies_file_path` et `pip_requirements_file_path` en faveur de `conda_dependencies_file` et `pip_requirements_file`, respectivement.
  + **azureml-opendatasets**
    + L’amélioration de NoaaIsdWeather enrichie sensiblement les performances de la version non SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.8

+ **Nouvelles fonctionnalités**
 + Les objets de flux de données peuvent maintenant être itérés, produisant une séquence d’enregistrements. Voir la documentation pour `Dataflow.to_record_iterator`.
  + Les objets de flux de données peuvent maintenant être itérés, produisant une séquence d’enregistrements. Voir la documentation pour `Dataflow.to_record_iterator`.

+ **Résolutions de bogue et améliorations**
 + Amélioration de la robustesse du kit de développement logiciel DataPrep.
 + Amélioration de la gestion des dataframes Pandas avec des index de colonne non-chaîne.
 + Amélioration des performances de `to_pandas_dataframe` dans les jeux de données.
 + Correction d’un bogue dans lequel l’exécution Spark des jeux de données a échoué lors de l’exécution dans un environnement à plusieurs nœuds.
  + Amélioration de la robustesse du kit de développement logiciel DataPrep.
  + Amélioration de la gestion des dataframes Pandas avec des index de colonne non-chaîne.
  + Amélioration des performances de `to_pandas_dataframe` dans les jeux de données.
  + Correction d’un bogue dans lequel l’exécution Spark des jeux de données a échoué lors de l’exécution dans un environnement à plusieurs nœuds.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.7

Nous avons rétabli une modification qui a amélioré les performances, car elle était à l’origine de problèmes pour certains clients qui utilisent Azure Databricks. Si vous avez rencontré un problème sur Azure Databricks, vous pouvez mettre à niveau vers la version 1.1.7 en utilisant l’une des méthodes ci-dessous :
1. Exécutez ce script pour la mise à jour : `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recréez le cluster, ce qui va installer la dernière version du kit de développement logiciel (SDK) pour préparation des données.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.45

+ **Nouvelles fonctionnalités**
  + Ajouter le modèle de substitution de l’arbre de décision pour imiter l’explicatif dans azureml-explain-model package
  + Possibilité de spécifier une version CUDA à installer sur les images d’inférence. Prise en charge CUDA 9.0, 9.1 et 10.0.
  + Les informations sur les images de base de formation d’Azure Machine Learning sont maintenant disponibles sur le [Référentiel GitHub des conteneurs Azure Machine Learning](https://github.com/Azure/AzureML-Containers) et [DockerHub](https://hub.docker.com/_/microsoft-azureml).
  + Ajout de la prise en charge de l’interface CLI pour la planification du pipeline. Exécuter « az ml pipeline -h » pour en savoir plus
  + Ajout du paramètre d’espace de noms Kubernetes dans la CLI et la configuration du déploiement du service web AKS.
  + Paramètre hash_paths déconseillé pour toutes les étapes de pipeline
  + Model.Register prend désormais en charge l’inscription de plusieurs fichiers individuels comme un seul modèle avec utilisation du paramètre `child_paths`.

+ **Fonctionnalités préliminaires**
    + Les explicatifs de scoring peuvent maintenant enregistrer optionnellement des informations conda et pip pour une sérialisation et une désérialisation plus fiables.
    + Résolution de bogue pour le sélecteur de fonctionnalité automatique.
    + Mise à jour de mlflow.azureml.build_image dans la nouvelle api, bogues corrigés exposés par la nouvelle implémentation.

+ **Résolutions de bogue et améliorations**
  + Dépendance `paramiko` supprimée de azureml-core. Ajout d’avertissements d’obsolescence pour les méthodes d’attache cible de calcul héritées.
  + Amélioration des performances de run.create_children
  + Dans un explicatif mimique avec classifieur binaire, résolution de l’ordre des probabilités lorsque la probabilité de professeur est utilisée pour la mise à l’échelle des valeurs de forme.
  + Amélioration de la gestion des erreurs et des messages pour l’apprentissage automatique.
  + Résolution du problème de délai d’attente d’itération pour l’apprentissage automatique.
  + Amélioration des performances de transformation de séries chronologiques pour l’apprentissage automatique.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.6

+ **Nouvelles fonctionnalités**
  + Ajout des fonctions de résumé pour les premières valeurs (`SummaryFunction.TOPVALUES`) et les dernières valeurs (`SummaryFunction.BOTTOMVALUES`).

+ **Résolutions de bogue et améliorations**
  + Amélioration significative des performances d’obtention de `read_pandas_dataframe`.
  + Correction d’un bogue qui provoque l’échec de `get_profile()` sur un flux de données qui pointe vers les fichiers binaires.
  + Exposition de `set_diagnostics_collection()` pour permettre l’activation/désactivation par programmation de la collection de données de télémétrie.
  + Modification du comportement de `get_profile()`. Les valeurs NaN sont maintenant ignorées pour Min, Mean, Std et Sum, qui s’aligne sur le comportement de Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.43

+ **Nouvelles fonctionnalités**
  + Azure Machine Learning offre désormais une prise en charge de première classe de l’apprentissage automatique populaire et de la structure d’analyse des données Scikit-learn. À l’aide de [l’estimateur `SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py), les utilisateurs peuvent facilement former et déployer des modèles Scikit-learn.
    + Découvrez comment [exécuter l’optimisation des hyperparamètres avec Scikit-learn à l’aide de HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Ajout de la prise en charge de la création de ModuleStep dans les pipelines ainsi que des classes Module et ModuleVersion pour gérer des unités de calcul réutilisables.
  + Les services ACI webServices prennent désormais en charge scoring_uri persistantes par le biais des mises à jour. Le scoring_uri changera à partir de l’adresse IP pour le nom de domaine complet. L’étiquette du nom Dns pour le nom de domaine complet peut être configurée en définissant le dns_name_label sur deploy_configuration.
  + Nouvelles fonctionnalités de Machine Learning automatisé :
    + Préapprentissage STL pour la prévision
    + Le clustering k-moyennes est activé pour le balayage de fonctionnalité
  + Les approbations de Quota de AmlCompute ont gagné en vitesse ! Nous avons maintenant automatisé le processus pour approuver des demandes de votre quota dans un seuil. Pour plus d’informations sur le fonctionnement des quotas, Découvrez [comment gérer les quotas](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Fonctionnalités préliminaires**
    + Intégration avec le suivi [MLflow](https://mlflow.org) 1.0.0 via le package azureml-mlflow ([exemples de notebooks](https://aka.ms/azureml-mlflow-examples)).
    + Soumettre un notebook Jupyter en tant qu’exécution. [Documentation de référence de l’API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Version préliminaire publique de [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) via le package azureml-contrib-datadrift ([exemples de notebooks](https://aka.ms/azureml-datadrift-example)). La dérive de données est l’une des principales raisons pour lesquelles la précision du modèle se dégrade au fil du temps. Elle se produit lorsque les données proposées dans un modèle en production sont différentes de celles utilisées pour former le modèle. Le détecteur de dérive de données AML aide le client à surveiller la dérive des données et envoie des alertes à chaque dérive détectée.

+ **Dernières modifications**

+ **Résolutions de bogue et améliorations**
  + RunConfiguration charge et enregistre des prises en charge en spécifiant un chemin d’accès de fichier complet avec rétrocompatibilité complète pour le comportement précédent.
  + Ajout de la mise en cache dans ServicePrincipalAuthentication, désactivée par défaut.
  + Journalisation activée pour plusieurs tracés sous le même nom de métrique.
  + Modèle de classe désormais correctement importable à partir de azureml.core (`from azureml.core import Model`).
  + Dans les étapes du pipeline, le paramètre `hash_path` est maintenant déconseillé. Le nouveau comportement consiste à hacher source_directory, sauf les fichiers répertoriés dans .amlignore ou .gitignore.
  + Dans les packages de pipeline, divers méthodes `get_all` et `get_all_*` ont été déconseillées en faveur de `list` et `list_*`, respectivement.
  + azureml.Core.get_run ne nécessite plus l’importation de classes avant de retourner le type de la série d’origine.
  + Correction d’un problème où des appels à la mise à jour du service Web n’a pas déclenchée une mise à jour.
  + Le délai d’expiration du scoring sur AKS webservices doit être compris entre 5 ms et de 300 000 ms. Le nombre maximal autorisé scoring_timeout_ms pour les demandes de scoring a été augmenté de 1 minute à 5 minutes.
  + Les objets LocalWebservice ont maintenant les propriétés `scoring_uri` et `swagger_uri`.
  + Déplacement de la création du répertoire des sorties et du téléchargement du répertoire des sorties hors du processus de l’utilisateur. Kit de développement logiciel (SDK) de l’historique des exécutions capable d’exécuter dans chaque processus utilisateur. Cela devrait résoudre certains problèmes de synchronisation rencontrés lors d’exécution d’entraînement distribué.
  + Le nom du journal azureml écrit à partir du nom de processus utilisateur inclut désormais les noms de processus (pour des entraînements distribués uniquement) et le PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.5

+ **Résolutions de bogue et améliorations**
  + Pour les valeurs de date/heure interprétées qui ont un format d’année à 2 chiffres, la plage d’années valides a été mise à jour pour correspondre à la version de mai de Windows. Cette plage est passée de 1930-2029 à 1950-2049.
  + Lors de la lecture dans un fichier et du paramétrage de `handleQuotedLineBreaks=True`, `\r` sera traitée comme une nouvelle ligne.
  + Correction d’un bogue qui a provoqué l’échec de `read_pandas_dataframe` dans certains cas.
  + Performances améliorées de `get_profile`.
  + Amélioration des messages d’erreur.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.4

+ **Nouvelles fonctionnalités**
  + Vous pouvez maintenant utiliser les fonctions de langage d’expression suivant pour extraire et analyser des valeurs datetime dans des nouvelles colonnes.
    + `RegEx.extract_record()` extrait des éléments de datetime dans une nouvelle colonne.
    + `create_datetime()` crée des objets datetime à partir d’éléments de datetime distincts.
  + Lors de l’appel `get_profile()`, vous pouvez maintenant voir que les colonnes de quantile sont étiquetées (est.) pour indiquer clairement que les valeurs sont des approximations.
  + Vous pouvez maintenant utiliser l’utilisation des caractères génériques ** lors de la lecture depuis le Stockage Blob Azure.
    + Exemple : `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Résolution des bogues**
  + Correction d’un bogue lié à la lecture d’un fichier Parquet à partir d’une source distante (objet Blob Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.39
+ **Modifications**
  + Configuration d’exécution auto_prepare_environment est déconseillée, avec auto préparation qui devient la valeur par défaut.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.3

+ **Nouvelles fonctionnalités**
  + Ajout de la prise en charge de la lecture à partir d’une base de données PostgresSQL, en appelant read_postgresql ou à l’aide d’une banque de données.
    + Consultez les exemples dans les guides pratiques :
      + [Notebook d’ingestion des données](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Notebook de banque de données](https://aka.ms/aml-data-prep-datastore-nb)

+ **Résolutions de bogue et améliorations**
  + Résolution des problèmes avec la conversion de type de colonne :
  + Convertit désormais correctement une colonne booléenne ou numérique en une colonne booléenne.
  + N’échoue plus lorsque vous tentez de définir une colonne de date pour être de type date.
  + Amélioration des types JoinType et de la documentation de référence relative. Lorsque vous joignez deux dataflow, vous pouvez maintenant spécifier un de ces types de jointure :
    + NONE, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Amélioration de l’inférence de type de données pour reconnaître plus de formats de date.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, vous pouvez désormais :
+ Créer des expériences de Machine Learning automatisé
+ Créer une machine virtuelle de notebook pour essayer les exemples de notebook Jupyter ou le vôtre.
+ Nouvelle section de création (préversion) dans l’espace de travail Azure Machine Learning, comprenant le Machine Learning automatisé, l’interface visuelle et les machines virtuelles du notebook hébergées
    + Créer automatiquement un modèle à l’aide du machine learning automatisé
    + Utiliser une interface visuelle avec fonction glisser-déposer pour exécuter des expériences
    + Créer une machine virtuelle notebook pour explorer les données, créer des modèles et déployer des services.
+ Mise à jour des graphiques et des métriques dans les rapports d’exécution et pages des détails de l’exécution
+ Mise à jour de la visionneuse de fichier pour les journaux, les sorties et les instantanés dans les pages de détails de l’exécution.
+ Expérience de création de rapports nouvelle et améliorée dans l’onglet Expériences.
+ Ajout de la capacité à télécharger le fichier config.json dans la page Vue d’ensemble de l’espace de travail Azure Machine Learning.
+ Prise en charge de la création d’espace de travail Azure Machine Learning à partir de l’espace de travail Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.33
+ **Nouvelles fonctionnalités**
  + La méthode _Workspace.create_ accepte désormais les configurations de cluster par défaut pour les clusters GPU et UC.
  + Si la création de l’espace de travail échoue, les ressources dépendantes sont nettoyées.
  + La référence (SKU) par défaut d’Azure Container Registry a été basculée en De base.
  + Azure Container Registry est créé en différé, si nécessaire pour la création d’image ou d’exécution.
  + Prise en charge des environnements pour les exécutions d’apprentissage.

### <a name="notebook-virtual-machine"></a>Machine virtuelle de notebook 

Utiliser une machine virtuelle de notebook comme un environnement d’hébergement sécurisé et adapté aux entreprises pour les notebooks Jupyter dans lesquels vous pouvez programmer des expériences d’apprentissage automatique, déployer des modèles en tant que points de terminaison web et effectuer toutes les autres opérations prises en charge par le kit de développement logiciel (SDK) Azure Machine Learning à l’aide de Python. Elle offre plusieurs fonctions :
+ [Faites tourner rapidement une machine virtuelle de notebook préconfigurée](tutorial-1st-experiment-sdk-setup.md) qui dispose de la dernière version du Kit de développement logiciel Azure Machine Learning et les packages associés.
+ L’accès est sécurisé par le biais de technologies éprouvées, tel que HTTPS, l’authentification et l’autorisation Azure Active Directory.
+ Stockage cloud fiable de notebooks et de code dans votre compte de stockage d’objets blob de votre espace de travail Azure Machine Learning. Vous pouvez supprimer en toute sécurité votre machine virtuelle de notebook sans perdre votre travail.
+ Exemples de notebooks préinstallés pour explorer et tester les fonctionnalités Azure Machine Learning.
+ Fonctions de personnalisation complète de machines virtuelles Azure, tous les types de machine virtuelle, tous les packages, tous les pilotes. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.33.

+ Les modèles accélérés matériellement d’Azure ML sur [FPGA](how-to-deploy-fpga-web-service.md) sont mis à la disposition générale.
  + Vous pouvez à présent [utiliser le package azureml-acceleration-models](how-to-deploy-fpga-web-service.md) à :
    + Former les poids d’un réseau neuronal profond pris en charge (ResNet 50, ResNet 152, DenseNet-121, VGG-16, et SSD-VGG)
    + Utiliser l’apprentissage par transfert avec le réseau neuronal profond pris en charge
    + Inscrire le modèle avec le Service Gestion des modèles et conteneuriser le modèle
    + Déployer le modèle sur une machine virtuelle Azure avec un FPGA dans un cluster Azure Kubernetes Service (AKS)
  + Déployer le conteneur dans un appareil de serveur [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Noter vos données avec le point de terminaison gRPC avec cet [exemple](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Machine Learning automatisé

+ Fonctionnalités de balayage pour activer l’ajout dynamique de :::no-loc text="featurizers"::: pour optimiser les performances. Nouveaux :::no-loc text="featurizers"::: : utiliser les incorporations, poids de preuve, les encodages de cible, le codage de texte cible, la distance de cluster
+ CV intelligent pour gérer des fractions train/valid à l’intérieur de ML automatisé
+ Quelques modifications de l’optimisation de mémoire et amélioration des performances de runtime
+ Amélioration des performances dans l’explication de modèle
+ Conversion de modèle ONNX pour l’exécution locale
+ Prise en charge Subsampling
+ Arrêt intelligent quand aucun critère de sortie n’est défini
+ Ensembles empilés

+ Prévision de séries chronologiques
  + Nouvelle fonction de prévision
  + Vous pouvez maintenant utiliser la validation croisée d’origine propagée sur les données de séries chronologiques
  + Nouvelles fonctionnalités ajoutées pour configurer les séries chronologiques en retard
  + Nouvelles fonctionnalités ajoutées pour prendre en charge des fonctionnalités de regroupement de la fenêtre propagée
  + Nouvelle détection de vacances et préapprentissage lorsque le code de pays est défini dans les paramètres d’expérience

+ Azure Databricks
  + Capacité de prévision des séries chronologiques et d’interprétabilité/explication de modèle activée
  + Vous pouvez désormais annuler et reprendre (continuer) des expériences de Machine Learning automatisé
  + Ajout de la prise en charge pour le traitement multicœur

### <a name="mlops"></a>MLOps
+ **Déploiement local et débogage des conteneurs de scoring**<br/> Vous pouvez désormais déployer un modèle ML localement et itérer rapidement sur votre fichier de scoring et vos dépendances pour vous assurer qu’ils se comportent comme prévu.

+ **Introduction de InferenceConfig et Model.deploy()**<br/> Le modèle de déploiement prend maintenant en charge la spécification d’un dossier source avec un script d’entrée, identique à un Runconfig.  En outre, le déploiement de modèle a été simplifié à une seule commande.

+ **Suivi de référence Git**<br/> Les clients demandent des fonctionnalités d’intégration Git de base depuis un moment, car elles contribuent à conserver une piste d’audit de bout en bout. Nous avons implémenté le suivi dans les entités principales dans Azure ML pour les métadonnées liées à Git (référentiel, commit, état propre). Ces informations seront collectées automatiquement par le Kit de développement logiciel et l’interface CLI.

+ **Service de profilage et de validation de modèle**<br/> Les clients se plaignent souvent de la difficulté à dimensionner correctement le calcul associé à leur service d’inférence. Avec notre service de profilage de modèle, le client peut donner des exemples d’entrées et nous établirons le profil entre 16 UC / configurations de mémoire différents afin de déterminer le dimensionnement optimal pour le déploiement.

+ **Apportez votre propre image de base pour l’inférence**<br/> Une autre critique courante a été la difficulté de déplacement de l’expérimentation aux dépendances de partage RE de l’inférence. Avec notre nouvelle capacité de partage d’image de base, vous pouvez maintenant réutiliser vos images de base d’expérimentation, les dépendances, etc, pour l’inférence. Cela devrait accélérer les déploiements et réduire l’écart de la boucle interne à la boucle externe.

+ **Expérience de génération de schéma Swagger améliorée**<br/> Notre méthode de génération Swagger était sujette à erreurs et était impossible à automatiser. Nous avons une nouvelle façon plus adaptée de générer des schémas swagger à partir de n’importe quelle fonction Python via des éléments décoratifs. Nous avons mis ce code en open source et notre protocole de génération de schéma n’est pas couplé à la plateforme Azure Machine Learning.

+ **L’interface de ligne de commande Azure ML CLI est mise à la disposition générale**<br/> Les modèles peuvent désormais être déployés avec une seule commande CLI. D’après des retours courants de nos clients, personne ne déploie de modèle ML depuis un notebook Jupyter. La [**documentation de référence CLI**](https://aka.ms/azmlcli) a été mise à jour.


## <a name="2019-04-22"></a>2019-04-22

Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.30.

Le [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) a été introduit pour ajouter une nouvelle version d’un pipeline publié tout en conservant le même point de terminaison.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.2

Remarque : Le kit de développement logiciel (SDK) de préparation des données Python n’installe plus les packages `numpy` et `pandas`. Consultez les [instructions d’installation mises à jour](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nouvelles fonctionnalités**
  + Vous pouvez maintenant utiliser la transformation Pivot.
    + Guide pratique : [Notebook Pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + Vous pouvez maintenant utiliser des expressions régulières dans des fonctions natives.
    + Exemples :
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Vous pouvez maintenant utiliser des fonctions `to_upper`  et `to_lower`  dans un langage d’expression.
  + Vous pouvez maintenant voir le nombre de valeurs uniques de chaque colonne dans un profil de données.
  + Pour certaines des étapes lecteur couramment utilisées, vous pouvez maintenant envoyer l’argument `infer_column_types`. Si elle est définie sur `True`, la préparation des données tentera de détecter et de convertir automatiquement les types de colonne.
    + `inference_arguments` est désormais déconseillé.
  + Vous pouvez maintenant appeler `Dataflow.shape`.

+ **Résolutions de bogue et améliorations**
  + `keep_columns`  accepte désormais un argument facultatif supplémentaire `validate_column_exists`, qui vérifie si le résultat de `keep_columns` contient toutes les colonnes.
  + Toutes les étapes lecteur (qui lit à partir d’un fichier) acceptent désormais un argument facultatif supplémentaire `verify_exists`.
  + Amélioration des performances de lecture de tramedonnées pandas et d’obtention de profils de données.
  + Correction d’un bogue où le découpage d’une seule étape d’un dataflow échouait avec un index unique.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portail Azure
  + Vous pouvez désormais renvoyer un script existant exécuté sur un cluster de calcul distant.
  + Vous pouvez maintenant exécuter un pipeline publié avec les nouveaux paramètres sous l’onglet Pipelines.
  + Les détails de l’exécution prennent désormais en charge une nouvelle visionneuse de fichier d’instantané. Vous pouvez afficher un instantané du répertoire lorsque vous avez envoyé une exécution spécifique. Vous pouvez également télécharger le notebook qui a été envoyé pour démarrer l’exécution.
  + Vous pouvez désormais annuler les exécutions parentes à partir du portail Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.23

+ **Nouvelles fonctionnalités**
  + Le kit de développement logiciel (SDK) Azure Machine Learning prend maintenant en charge Python 3.7.
  + Les estimateurs de réseau neuronal profond d’Azure Machine Learning offre désormais une prise en charge multiversion intégrée. Par exemple, l’estimateur `TensorFlow`  accepte maintenant un paramètre `framework_version` et les utilisateurs peuvent spécifier la version ’1.10’ ou ’1.12’. Pour obtenir la liste des versions prises en charge par votre version du kit de développement logiciel actuelle, appelez `get_supported_versions()` sur la classe souhaitée de l’infrastructure .NET (par exemple, `TensorFlow.get_supported_versions()`).
  Pour obtenir la liste des versions prises en charge par la dernière version du Kit de développement logiciel, consultez la [documentation sur l’estimateur du réseau neuronal profond](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.1

+ **Nouvelles fonctionnalités**
  + Vous pouvez lire plusieurs sources de transformations Datastore/DataPath/DataReference sources using read_*.
  + Vous pouvez effectuer les opérations suivantes sur les colonnes pour créer une nouvelle colonne : division, floor, modulo, power, length.
  + La préparation des données fait désormais partie de la suite de tests de diagnostic Azure ML et enregistrera les informations de diagnostic par défaut.
    + Pour désactiver cette option, définissez cette variable d’environnement sur true : DISABLE_DPREP_LOGGER

+ **Résolutions de bogue et améliorations**
  + Amélioration de la documentation du code pour les classes et fonctions couramment utilisées.
  + Correction d’un bogue dans auto_read_file qui ne pouvait lire les fichiers Excel.
  + Ajout de l’option pour remplacer le dossier dans read_pandas_dataframe.
  + Amélioration des performances de l’installation de dépendance dotnetcore2 et prise en charge de Fedora 27/28 et Ubuntu 1804.
  + Amélioration des performances de lecture d’objets blob Azure.
  + La détection du type de colonne prend maintenant en charge les colonnes de type Long.
  + Correction d’un bogue dans lequel certaines valeurs de date ont été affichées en tant qu’horodatages au lieu d’objets datetime Python.
  + Correction d’un bogue dans lequel certains nombres de type ont été affichés comme doubles au lieu d’entiers.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.21

+ **Nouvelles fonctionnalités**
  + La méthode *azureml.core.Run.create_children* permet de créer une faible latence de plusieurs exécutions enfants avec un seul appel.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.1.0

+ **Dernières modifications**
  + Le concept du Package de préparation des données a été déconseillé et n’est plus pris en charge. Au lieu de conserver plusieurs dataflow dans un seul package, vous pouvez les conserver individuellement.
    + Guide pratique : [Ouverture et sauvegarde du notebook de dataflow](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nouvelles fonctionnalités**
  + La préparation des données peut désormais reconnaître les colonnes qui correspondent à un Type particulier de sémantique les fractionner en conséquence. Les STypes actuellement pris en charge incluent : adresse de messagerie, coordonnées géographiques (latitude et longitude), adresses IPv4 et IPv6, numéro de téléphone américain et code postal américain.
    + Guide pratique : [Notebook de types Sémantique](https://aka.ms/aml-data-prep-semantic-types-nb)
  + La préparation des données prend désormais en charge les opérations suivantes pour générer une colonne obtenue à partir de deux colonnes numériques : soustraire, multiplier, diviser et modulo.
  + Vous pouvez appeler `verify_has_data()` sur un dataflow pour vérifier s’il produira des enregistrements en cas d’exécution.

+ **Résolutions de bogue et améliorations**
  + Vous pouvez maintenant spécifier le nombre de compartiments à utiliser dans un histogramme pour les profils de colonne numérique.
  + La transformation `read_pandas_dataframe` requiert désormais que la trame de données dispose de noms de colonnes string- ou byte-.
  + Correction d’un bogue dans la transformation `fill_nulls`, où les valeurs n’étaient pas correctement remplies lorsque la colonne était manquante.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.18

 + **Modifications**
   + Le package azureml-tensorboard remplace azureml-contrib-tensorboard.
   + Avec cette version, vous pouvez configurer un compte d’utilisateur sur votre cluster de calcul géré (amlcompute), lors de sa création. Cela est possible en passant ces propriétés dans la configuration de l’approvisionnement. Vous pouvez trouver plus d’informations dans la [documentation sur la référence du kit de développement logiciel (SDK)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.0.17

+ **Nouvelles fonctionnalités**
  + Prend désormais en charge l’ajout de deux colonnes numériques pour générer une colonne résultante en utilisant le langage d’expression.

+ **Résolutions de bogue et améliorations**
  + Amélioration de la documentation et de la vérification des paramètres de random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.0.16

+ **Résolution de bogue**
  + Correction d’un problème d’authentification au principal de service causé par une modification de l’API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.17

+ **Nouvelles fonctionnalités**

  + Azure Machine Learning fournit désormais une prise en charge de première classe pour le Chainer de l’infrastructure de réseau neuronal profond populaire. À l’aide des classes [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py), les utilisateurs peuvent facilement former et déployer des modèles Chainer.
    + Découvrez comment [exécuter l’entraînement distribué avec ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Découvrez comment [exécuter l’optimisation des hyperparamètres avec Chainer à l’aide de HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb).
  + Ajout de la capacité des pipelines Azure Machine Learning à déclencher une exécution de pipeline en fonction des modifications apportées au magasin de données. Le pipeline [notebook de planification](https://aka.ms/pl-schedule) est mis à jour afin de tirer parti de cette fonctionnalité.

+ **Résolutions de bogue et améliorations**
  + Nous avons ajouté la prise en charge des pipelines Azure Machine Learning pour définir la propriété source_directory_data_store sur un magasin de données de votre choix (par exemple, un stockage d’objets blob) dans des [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) qui sont fournies dans [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Par défaut, les étapes utilisent le stockage Fichier Azure comme banque de données de secours, ce qui peut entraîner des problèmes de limitation de bande passante lorsqu’un grand nombre d’étapes sont exécutées en même temps.

### <a name="azure-portal"></a>Portail Azure

+ **Nouvelles fonctionnalités**
  + Nouvelle expérience glisser-déposer pour les rapports. Les utilisateurs peuvent faire glisser une colonne de la zone de configuration à la zone de la table où s’affiche un aperçu de la table. Les colonnes peuvent être déplacées.
  + Nouvelle visionneuse de fichiers journaux
  + Liens vers exécution d’expériences, calcul, modèles, images et déploiements à partir de l’onglet activités

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.0.15

+ **Nouvelles fonctionnalités**
  + La préparation des données prend maintenant en charge l’écriture de flux de fichier à partir d’un dataflow. Fournit également la possibilité de manipuler les noms de flux de fichiers pour créer de nouveaux noms de fichiers.
    + Guide pratique : [Utiliser des notebook de flux de fichiers](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Résolutions de bogue et améliorations**
  + Amélioration des performances de t-Digest sur de grands ensembles de données.
  + La préparation des données prend désormais en charge la lecture de données à partir d’un chemin de données.
  + Un encodage à chaud fonctionne désormais sur des colonnes booléennes et numériques.
  + Autres correctifs de bogues divers.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.15

+ **Nouvelles fonctionnalités**
  + Ajout de AzureBatchStep ([bloc-notes](https://aka.ms/pl-azbatch)), HyperDriveStep (bloc-notes) et de la fonctionnalité de planification basée sur le temps ([bloc-notes](https://aka.ms/pl-schedule)) dans les pipelines Azure Machine Learning.
  +  Mise à jour de DataTranferStep pour fonctionner avec Azure SQL Server et Azure Database pour PostgreSQL ([bloc-notes](https://aka.ms/pl-data-trans)).

+ **Modifications**
  + Dépréciation de `PublishedPipeline.get_published_pipeline` en faveur de `PublishedPipeline.get`.
  + Dépréciation de `Schedule.get_schedule` en faveur de `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.0.12

+ **Nouvelles fonctionnalités**
  + La préparation des données prend désormais en charge la lecture à partir d’une base de données Azure SQL utilisant une banque de données.

+ **Modifications**
  + Amélioration des performances de mémoire de certaines opérations sur des données volumineuses.
  + L’option `read_pandas_dataframe()` requiert désormais que `temp_folder` soit spécifié.
  + La propriété `name` sur `ColumnProfile` est déconseillée : utilisez `column_name` à la place.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.10

+ **Modifications** :
  + Le Kit de développement logiciel (SDK) Azure ML n’inclut plus de packages azure-cli en tant que dépendance. Plus précisément, les dépendances azure-cli-core et azure-cli-profile ont été supprimées d’azureml-core. Les modifications ayant un impact sur l’utilisateur sont les suivantes :
    + Si vous exécutez la commande « az login » et que vous utilisez azureml-sdk, le Kit de développement logiciel (SDK) exécute une nouvelle fois le code de connexion de navigateur ou d’appareil. Il n’utilise aucun état d’informations d’identification créé par « az login ».
    + Pour l’authentification Azure CLI, telle que l’utilisation d’« az login », utilisez la classe _azureml.core.authentication.AzureCliAuthentication_. Pour l’authentification Azure CLI, utilisez _pip install azure-cli_ dans l’environnement Python où vous avez installé azureml-sdk.
    + Si vous exécutez « az login » à l’aide d’un principal du service pour l’automatisation, nous vous recommandons d’utiliser la classe _azureml.core.authentication.ServicePrincipalAuthentication_, car azureml-sdk n’utilise pas d’état d’informations d’identification créé par Azure CLI.

+ **Résolution des bogues** : Cette version contient principalement des correctifs de bogues mineurs

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.0.8

+ **Résolution des bogues**
  + Amélioration des performances d’obtention de profils de données.
  + Correction de bogues mineurs liés au rapport d’erreurs.

### <a name="azure-portal-new-features"></a>Portail Azure : nouvelles fonctionnalités
+ Nouvelle expérience glisser-déplacer pour les rapports. Les utilisateurs peuvent faire glisser une colonne ou un attribut de la zone de configuration vers la zone graphique où le système sélectionne automatiquement un type de graphique approprié pour l’utilisateur en fonction du type de données. Les utilisateurs peuvent définir le type de graphique sur d’autres types applicables ou ajouter des attributs supplémentaires.

    Types de graphiques pris en charge :
    - Graphique en courbes
    - Histogramme
    - Graphique à barres empilées
    - Diagramme à surfaces
    - Nuage de points
    - Tracé de bulles
+ Le portail génère maintenant dynamiquement des rapports pour les expériences. Lorsqu’un utilisateur envoie une exécution à une expérience, un rapport est automatiquement généré avec des métriques et des graphiques permettant d’effectuer des comparaisons entre différentes exécutions.

## <a name="2019-01-14"></a>14-01-2019

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.8

+ **Résolution des bogues** : Cette version contient principalement des correctifs de bogues mineurs

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning v1.0.7

+ **Nouvelles fonctionnalités**
  + Améliorations des magasins de données (documentées dans le [Guide pratique sur les magasins de données](https://aka.ms/aml-data-prep-datastore-nb))
    + Ajout de la possibilité de lire et d'écrire sur le partage de fichiers Azure et dans les magasins de données ADLS lors de la mise à l'échelle.
    + Lors de l'utilisation de magasins de données, la préparation des données prend désormais en charge l'utilisation de l'authentification du principal de service au lieu de l'authentification interactive.
    + Ajout de la prise en charge des URL wasb et wasbs.

## <a name="2019-01-09"></a>09-01-2019

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>SDK de préparation de données Azure Machine Learning v1.0.6

+ **Résolution des bogues**
  + Correction du bogue lors de la lecture à partir de conteneurs Blob Azure publics lisibles sur Spark

## <a name="2018-12-20"></a>20-12-2018

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Kit SDK Azure Machine Learning pour Python v1.0.6
+ **Résolution des bogues** : Cette version contient principalement des correctifs de bogues mineurs

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Kit SDK de préparation de données Azure Machine Learning v1.0.4

+ **Nouvelles fonctionnalités**
  + La fonction `to_bool` autorise la conversion de valeurs discordantes en valeurs d’erreur. Il s’agit du nouveau comportement par défaut des discordances pour `to_bool` et `set_column_types`, alors que le comportement par défaut précédent était de convertir les valeurs discordantes en valeurs de type False.
  + Lors de l’appel de `to_pandas_dataframe`, une nouvelle option permet d’interpréter les valeurs nulles/manquantes en NaN dans les colonnes numériques.
  + Ajout de la possibilité de vérifier le type de retour de certaines expressions pour s’assurer de la cohérence du type et anticiper les échecs.
  + Vous pouvez maintenant appeler `parse_json` pour analyser les valeurs d’une colonne en tant qu’objets JSON et les développer en plusieurs colonnes.

+ **Résolution des bogues**
  + Correction d’un bogue qui provoquait des incidents de `set_column_types` dans Python 3.5.2.
  + Correction d’un bogue qui provoquait un incident lors de la connexion au magasin de données à l’aide d’une image AML.

+ **Mises à jour**
  * [Exemples de notebooks](https://aka.ms/aml-data-prep-notebooks) pour les didacticiels de prise en main, les études de cas et les guides pratiques.

## <a name="2018-12-04-general-availability"></a>2018-12-04 : Disponibilité générale

Azure Machine Learning est maintenant en disponibilité générale.

### <a name="azure-machine-learning-compute"></a>Capacité de calcul Azure Machine Learning
Avec cette version, nous annonçons une nouvelle expérience de calcul gérée via [la capacité de calcul Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). La cible de calcul remplace le calcul Azure Batch AI pour Azure Machine Learning.

Cette cible de calcul :
+ Est utilisée pour la formation du modèle et l’inférence/scoring par lots
+ Est une cible de calcul de nœud unique vers des nœuds multiples
+ Effectue la gestion des clusters et la planification des travaux pour l’utilisateur
+ Effectue la mise à l’échelle automatique par défaut
+ Prise en charge des ressources UC et GPU
+ Permet l’utilisation de machines virtuelles à faible priorité en vue de réduire les coûts

La capacité de calcul Azure Machine Learning peut être créée dans Python, à l’aide du portail Azure ou de l’interface CLI. Elle doit être créée dans la région de votre espace de travail et ne peut pas être attachée à un autre espace de travail. Cette cible de calcul utilise un conteneur Docker pour votre exécution et empaquette vos dépendances pour répliquer le même environnement sur tous les nœuds.

> [!Warning]
> Nous recommandons la création d’un nouvel espace de travail pour utiliser Azure Machine Learning Compute. Il existe une faible possibilité que les utilisateurs tentant de créer de la capacité de calcul Machine Learning à partir d’un espace de travail reçoivent un message d’erreur. Une capacité de calcul déjà existante dans votre espace de travail devrait continuer à fonctionner sans être affectée.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Kit SDK Azure Machine Learning pour Python v1.0.2
+ **Dernières modifications**
  + Avec cette version, nous supprimons la prise en charge de la création d’une machine virtuelle à partir d’Azure Machine Learning. Vous pouvez toujours associer une machine virtuelle cloud existante ou un serveur local à distance.
  + Nous supprimons également la prise en charge de BatchAI, qui doit maintenant être pris en charge par le biais de la capacité de calcul Machine Learning.

+ **Nouveau**
  + Pour les pipelines de Machine Learning :
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Updated**
  + Pour les pipelines de Machine Learning :
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) accepte désormais runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) copie maintenant vers et à partir d’une source de données SQL
    + Planifier des fonctionnalités dans le Kit SDK pour créer et mettre à jour des planifications pour l’exécution de pipelines publiés

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Kit SDK de préparation de données Azure Machine Learning v0.5.2
+ **Dernières modifications**
  * `SummaryFunction.N` a été renommé `SummaryFunction.Count`.

+ **Résolution des bogues**
  * Utilisez le dernier jeton d’exécution AML lors de la lecture et de l’écriture dans des magasins de données lors d’exécutions à distance. Auparavant, si le jeton d’exécution AML était mis à jour dans Python, l’exécution de la préparation des données n’était pas mise à jour avec le jeton d’exécution AML mis à jour.
  * Ajout de messages d’erreur plus clairs
  * to_spark_dataframe() n’engendre plus de blocage lorsque Spark utilise la sérialisation `Kryo`
  * L’inspecteur de comptage de valeurs peut désormais afficher plus de 1 000 valeurs uniques
  * Le fractionnement aléatoire n’échoue plus si le flux de données d’origine n’a pas de nom

+ **Plus d’informations**
  * [Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs et notebooks
+ Pipelines ML
  + Notebooks nouveaux et mis à jour pour bien démarrer avec les pipelines, l’étendue par lot et des exemples de transfert de style : https://aka.ms/aml-pipeline-notebooks
  + Découvrez comment [créer votre premier pipeline](how-to-create-your-first-pipeline.md)
  + Découvrez comment [exécuter des prédictions par lots à l’aide de pipelines](how-to-use-parallel-run-step.md)
+ Cible de calcul Azure Machine Learning
  + Les [exemples de notebooks](https://aka.ms/aml-notebooks) ont été mis à jour pour utiliser la nouvelle capacité de calcul managé.
  + [En savoir plus sur cette capacité de calcul](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portail Azure : nouvelles fonctionnalités
+ Créez et gérez des types de capacité de calcul [Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) dans le portail.
+ Surveillez l’utilisation du quota et [demandez un quota](how-to-manage-quotas.md) pour la capacité de calcul Azure Machine Learning.
+ Affichez l’état du cluster de capacité de calcul Azure Machine Learning en temps réel.
+ La prise en charge du réseau virtuel a été ajoutée pour la création d’une capacité de calcul Azure Machine Learning et d’Azure Kubernetes Service.
+ Exécutez à nouveau vos pipelines publiés avec les paramètres existants.
+ Nouvelles [chartes machine Learning automatisées](how-to-understand-automated-ml.md) pour les modèles de classification (élévation, gains, étalonnage, graphique d’importance des fonctionnalités avec l’explicabilité de modèle) et les modèles de régression (résidus et graphique d’importance des fonctionnalités avec l’explicabilité de modèle).
+ Les pipelines peuvent être affichés dans le portail Azure




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>SDK Azure Machine Learning pour Python v0.1.80

+ **Dernières modifications**
  * L’espace de noms *azureml.train.widgets* a été déplacé vers *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* déprécie les classes suivantes : *azureml.core.compute.BatchAICompute* et *azureml.core.compute.DSVMCompute*. Cette dernière classe sera supprimée dans les versions ultérieures. La classe AmlCompute a désormais une définition plus facile ; munie simplement d’un vm_size et du max_nodes, elle met automatiquement à l’échelle votre cluster entre 0 et max_nodes quand un travail est envoyé. Nos [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) ont été mis à jour avec ces informations afin de vous proposer des exemples d'utilisation. Nous espérons que vous appréciez cette simplification, que viendront compléter de nombreuses fonctionnalités plus intéressantes dans une version ultérieure !

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK de préparation de données Azure Machine Learning v0.5.1

Découvrez-en plus sur le SDK de préparation de données en lisant les [documents de référence](https://aka.ms/data-prep-sdk).
+ **Nouvelles fonctionnalités**
   * Nous avons créé une interface CLI DataPrep permettant d’exécuter des packages DataPrep et d’afficher le profil de données d’un jeu de données ou d’un flux de données.
   * Nous avons repensé l’API SetColumnType pour améliorer la convivialité.
   * Nous avons renommé smart_read_file en auto_read_file.
   * Nous avons inclus l’inclinaison et le kurtosis dans le profil de données.
   * Vous pouvez créer un échantillon avec un échantillonnage stratifié.
   * Vous pouvez lire à partir de fichiers zip qui contiennent des fichiers CSV.
   * Vous pouvez fractionner les jeux de données selon les lignes de façon aléatoire (par exemple, pour obtenir des jeux de test-entraînement)
   * Vous pouvez obtenir tous les types de données de colonne d’un flux de données ou d’un profil de données en appelant `.dtypes`
   * Vous pouvez obtenir le nombre de lignes d’un flux de données ou d’un profil de données en appelant `.row_count`

+ **Résolution des bogues**
   * Nous avons résolu la conversion de type fixe long en type double.
   * Nous avons résolu l’assertion effectuée après l’ajout d’une colonne.
   * Nous avons corrigé un problème lié au regroupement probable, qui empêchait la détection de groupes dans certains cas.
   * Nous avons résolu une fonction de tri afin de respecter l’ordre de tri sur plusieurs colonnes.
   * Nous avons résolu les expressions « et/ou » afin qu’elles soient similaires à la façon dont `pandas` les gère
   * Nous avons résolu la lecture à partir du chemin dbfs.
   * Nous avons rendu les messages d’erreur plus compréhensibles.
   * Il peut désormais lire les données d’une cible de calcul à distance à l’aide d’un jeton AML.
   * Il n’échoue plus sur DSVM Linux.
   * Il ne plante plus quand des prédicats de chaîne contiennent des valeurs non-chaîne.
   * Il gère désormais les erreurs d’assertion quand le flux de données doit échouer correctement.
   * Il prend désormais en charge les emplacements de stockage montés dbutils sur Azure Databricks.

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Portail Azure
Le portail Azure d’Azure Machine Learning contient les mises à jour suivantes :
  * Un nouveau onglet **Pipelines** pour les pipelines publiés.
  * Ajout de la prise en charge de la connexion d’un cluster HDInsight existant comme cible de calcul.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>SDK Azure Machine Learning pour Python v0.1.74

+ **Dernières modifications**
  * *Workspace.compute_targets, datastores, experiments, images, models et *webservices* ne sont plus des méthodes, mais deviennent des propriétés. Par exemple, *Workspace.compute_targets* remplace *Workspace.compute_targets()* .
  * *Run.get_context* rend *Run.get_submitted_run* obsolète. Cette dernière méthode sera supprimée dans les versions ultérieures.
  * La classe *PipelineData* attend un objet datastore en tant que paramètre et non pas datastore_name. De même, *Pipeline* accepte default_datastore plutôt que default_datastore_name.

+ **Nouvelles fonctionnalités**
  * L’[exemple de notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) Azure Machine Learning Pipelines utilise maintenant les étapes MPI.
  * Le widget RunDetails pour les ordinateurs portables Jupyter est mis à jour pour afficher une visualisation du pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>SDK de préparation de données Azure Machine Learning v0.4.0

+ **Nouvelles fonctionnalités**
  * Nombre de types ajouté au profil de données
  * Nombre de valeurs et histogramme désormais disponible
  * Plus de centiles dans le profil de données
  * Médian disponible dans Summarize (Résumer)
  * Prise en charge de Python 3.7
  * Lorsque vous enregistrez un dataflow contenant des données dans un package DataPrep, les informations du magasin de données sont conservées dans ce package.
  * L’écriture dans le magasin de données est maintenant prise en charge

+ **Bogue corrigé**
  * Les dépassements de capacité d’entiers non signés 64 bits sont maintenant gérés correctement sous Linux
  * Étiquette de texte incorrecte corrigées pour les fichiers en texte brut dans smart_read
  * Le type de colonne chaîne s’affiche maintenant dans la vue métriques
  * Le nombre de types est maintenant corrigé pour associer ValueKinds à un seul FieldType et non pas à des FieldTypes individuels.
  * Write_to_csv n’échoue plus lorsque le chemin d’accès est fourni en tant que chaîne
  * Lorsque vous utilisez Remplacer, le fait de ne pas renseigner « find » (trouver) ne fait plus échouer l’application

## <a name="2018-10-12"></a>12-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>SDK Azure Machine Learning pour Python v0.1.68

+ **Nouvelles fonctionnalités**
  * Prise en charge de plusieurs locataires durant la création d’un espace de travail.

+ **Bogues corrigés**
  * Vous n’avez plus besoin d’épingler la version de la bibliothèque pynacl durant le déploiement d’un service web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>SDK de préparation de données Azure Machine Learning v0.3.0

+ **Nouvelles fonctionnalités**
  * Ajout de la méthode transform_partition_with_file(script_path), qui permet aux utilisateurs de transmettre le chemin d’un fichier Python à exécuter

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>SDK Azure Machine Learning pour Python v0.1.65
La [version 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) inclut de nouvelles fonctionnalités, plus de documentation, des correctifs de bogues et plus d’[exemples de blocs-notes](https://aka.ms/aml-notebooks).

Consultez la [liste des problèmes connus](resource-known-issues.md) pour en savoir plus sur les bogues connus et les solutions de contournement.

+ **Dernières modifications**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services retournent un dictionnaire, et non plus une liste. Consultez la documentation de l’API [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py).

  * Machine Learning automatisé : suppression de l’erreur quadratique moyenne normalisée des métriques principales.

+ **HyperDrive**
  * Divers correctifs de bogues HyperDrive pour la méthode Bayésien, améliorations des performances pour les appels d’obtention de métriques.
  * Mise à niveau de Tensorflow 1.10 depuis 1.9
  * Optimisation des images docker pour le démarrage à froid.
  * Les travaux indiquent maintenant l’état correct même s’ils quittent avec une erreur de code autre que 0.
  * Validation des attributs RunConfig dans le SDK.
  * L’objet d’exécution HyperDrive prend en charge l’annulation comme dans une exécution normale : il n’est pas nécessaire de transmettre de paramètres.
  * Améliorations des widgets pour maintenir l’état des valeurs de liste déroulante pour les exécutions distribuées et les exécutions HyperDrive.
  * Les fichiers journaux, dont ceux de TensorBoard, prennent en charge l’état fixe pour le serveur de paramètres.
  * Prise en charge d’Intel(R) MPI côté service.
  * Résolution de bogue dans le réglage des paramètres pour corriger l’exécution distribuée durant la validation dans BatchAI.
  * Le gestionnaire de contexte identifie désormais l’instance principale.

+ **Expérience du Portail Azure**
  * log_table() et log_row() sont pris en charge dans les détails de l’exécution.
  * Création automatique de graphes pour les tables et les lignes avec 1, 2 ou 3 colonnes numériques et une colonne catégorielle facultative.

+ **Machine Learning automatisé**
  * Amélioration de la gestion des erreurs et de la documentation
  * Résolution des problèmes de performances liés à la récupération de propriétés d’exécution.
  * Résolution d’un problème de poursuite d’exécution.
  * Résolution des problèmes d’itération :::no-loc text="ensembling":::.
  * Correction d’un bogue d’entraînement bloquant sur MAC OS.
  * Sous-échantillonnage d’une courbe PR/ROC pour une macro-moyenne dans un scénario de validation personnalisé.
  * Suppression d’une logique d’index en trop.
  * Suppression d’un filtre de l’API get_output.

+ **Pipelines**
  * Ajout d’une méthode Pipeline.publish() pour publier un pipeline directement, sans nécessiter une exécution au préalable.
  * Ajout d’une méthode PipelineRun.get_pipeline_runs() pour extraire les exécutions de pipeline qui ont été générées à partir d’un pipeline publié.

+ **Projet Brainwave**
  * Mise à jour de la prise en charge des nouveaux modèles d’intelligence artificielle disponibles sur les FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK de préparation de données Azure Machine Learning v0.2.0
La [version 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclut les fonctionnalités et les correctifs de bogues suivants :

+ **Nouvelles fonctionnalités**
  * Prise en charge de l’encodage à chaud
  * Prise en charge de la transformation de quantile

+ **Bogues Corrigés :**
  * Fonctionne avec n’importe quelle version de Tornado, sans besoin de passer à une version antérieure à votre version de Tornado
  * Décompte de valeurs pour toutes les valeurs, pas seulement les trois premières

## <a name="2018-09-public-preview-refresh"></a>09-2018 (actualisation de la préversion publique)

Nouvelle version d’Azure Machine Learning actualisée : découvrez-en plus sur cette version : https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Étapes suivantes

Consultez la vue d’ensemble [d’Azure Machine Learning](overview-what-is-azure-ml.md).
