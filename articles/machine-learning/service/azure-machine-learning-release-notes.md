---
title: Nouveautés de la version
titleSuffix: Azure Machine Learning service
description: Apprenez-en davantage sur les dernières mises à jour d’Azure Machine Learning service et les SDK de Machine Learning et de préparation de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: d43bef902b66976c32735b6d45029f41bb5e3264
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514041"
---
# <a name="azure-machine-learning-service-release-notes"></a>Notes de publication du service Azure Machine Learning

Dans cet article, découvrez les versions du service Azure Machine Learning.  Pour obtenir une description complète de chaque Kit de développement logiciel (SDK), consultez la documentation de référence de :
+ [**Kit de développement logiciel (SDK) principal pour Python**](https://aka.ms/aml-sdk) d’Azure Machine Learning
+ [**Kit de développement logiciel (SDK) de préparation de données**](https://aka.ms/data-prep-sdk) Azure Machine Learning

Consultez la [liste des problèmes connus](resource-known-issues.md) pour en savoir plus sur les bogues connus et les solutions de contournement.


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

+ **Dernières modifications**

+ **Résolutions de bogue et améliorations**
  + Dépendance paramiko supprimée de azureml-core. Ajout d’avertissements d’obsolescence pour les méthodes d’attache cible de calcul héritées.
  + Amélioration des performances de run.create_children
  + Dans un explicatif mimique avec classifieur binaire, résolution de l’ordre des probabilités lorsque la probabilité de professeur est utilisée pour la mise à l’échelle des valeurs de forme
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
  + Les approbations de Quota de AmlCompute ont gagné en vitesse ! Nous avons maintenant automatisé le processus pour approuver des demandes de votre quota dans un seuil. Pour plus d’informations sur le fonctionnement des quotas, Découvrez [comment gérer les quotas](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Fonctionnalités préliminaires**
    + Intégration avec le suivi [MLflow](https://mlflow.org) 1.0.0 via le package azureml-mlflow ([exemples de notebooks](https://aka.ms/azureml-mlflow-examples)).
    + Soumettre un notebook Jupyter en tant qu’exécution. [Documentation de référence de l’API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Version préliminaire publique de [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) via le package azureml-contrib-datadrift ([exemples de notebooks](https://aka.ms/azureml-datadrift-example)). La dérive de données est l’une des principales raisons pour lesquelles la précision du modèle se dégrade au fil du temps. Elle se produit lorsque les données proposées dans un modèle en production sont différentes de celles utilisées pour former le modèle. Le détecteur de dérive de données AML aide le client à surveiller la dérive des données et envoie des alertes à chaque dérive détectée. 

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
+ Toute nouvelle section de création (version préliminaire) dans l’espace de travail du service Machine Learning, qui inclut le Machine Learning automatisé, l’interface visuelle et les machines virtuelles du notebook hébergées
    + Créer automatiquement un modèle à l’aide du machine learning automatisé 
    + Utiliser une interface visuelle avec fonction glisser-déposer pour exécuter des expériences
    + Créer une machine virtuelle notebook pour explorer les données, créer des modèles et déployer des services.
+ Mise à jour des graphiques et des métriques dans les rapports d’exécution et pages des détails de l’exécution
+ Mise à jour de la visionneuse de fichier pour les journaux, les sorties et les instantanés dans les pages de détails de l’exécution.
+ Expérience de création de rapports nouvelle et améliorée dans l’onglet Expériences. 
+ Ajout de la capacité à télécharger le fichier config.json dans la page Vue d’ensemble de l’espace de travail Azure Machine Learning service.
+ Prise en charge de la création d’espace de travail du service Machine Learning à partir de l’espace de travail Azure Databricks 

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
+ [Faites tourner rapidement une machine virtuelle de notebook préconfigurée](quickstart-run-cloud-notebook.md) qui dispose de la dernière version du Kit de développement logiciel Azure Machine Learning et les packages associés.
+ L’accès est sécurisé par le biais de technologies éprouvées, tel que HTTPS, l’authentification et l’autorisation Azure Active Directory.
+ Stockage cloud fiable de notebooks et de code dans votre compte de stockage d’objets blob de votre espace de travail Azure Machine Learning. Vous pouvez supprimer en toute sécurité votre machine virtuelle de notebook sans perdre votre travail.
+ Exemples de notebooks préinstallés pour explorer et tester les fonctionnalités Azure Machine Learning service.
+ Fonctions de personnalisation complète de machines virtuelles Azure, tous les types de machine virtuelle, tous les packages, tous les pilotes. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Kit de développement logiciel (SDK) Azure Machine Learning pour Python v1.0.33.

+ Les modèles accélérés matériellement d’Azure ML sur [FPGA](concept-accelerate-with-fpgas.md) sont mis à la disposition générale.
  + Vous pouvez à présent [utiliser le package azureml-acceleration-models](how-to-deploy-fpga-web-service.md) à :
    + Former les poids d’un réseau neuronal profond pris en charge (ResNet 50, ResNet 152, DenseNet-121, VGG-16, et SSD-VGG)
    + Utiliser l’apprentissage par transfert avec le réseau neuronal profond pris en charge
    + Inscrire le modèle avec le Service Gestion des modèles et conteneuriser le modèle
    + Déployer le modèle sur une machine virtuelle Azure avec un FPGA dans un cluster Azure Kubernetes Service (AKS)
  + Déployer le conteneur dans un appareil de serveur [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Noter vos données avec le point de terminaison gRPC avec cet [exemple](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Machine Learning automatisé

+ Fonctionnalités de balayage pour activer l’ajout dynamique de featurizers pour optimiser les performances. Nouveaux featurizers : utiliser les incorporations, poids de preuve, les encodages de cible, le codage de texte cible, la distance de cluster
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

Remarque : Le kit de développement logiciel (SDK) de préparation des données Python n’installe plus les packages `numpy` et `pandas`. Consultez les [instructions d’installation mises à jour](https://aka.ms/aml-data-prep-installation).

+ **Nouvelles fonctionnalités**
  + Vous pouvez maintenant utiliser la transformation Pivot.
    + Guide pratique : [Notebook Pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + Vous pouvez maintenant utiliser des expressions régulières dans des fonctions natives.
    + Exemples :
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
   + Avec cette version, vous pouvez configurer un compte d’utilisateur sur votre cluster de calcul géré (amlcompute), lors de sa création. Cela est possible en passant ces propriétés dans la configuration de l’approvisionnement. Vous pouvez trouver plus d’informations dans la [documentation sur la référence du kit de développement logiciel (SDK)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

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
  + Ajout de la capacité des pipelines Azure Machine Learning à déclencher une exécution de pipeline en fonction des modifications apportées à la banque de données. Le pipeline [notebook de planification](https://aka.ms/pl-schedule) est mis à jour afin de tirer parti de cette fonctionnalité.

+ **Résolutions de bogue et améliorations**
  + Nous avons ajouté la prise en charge des pipelines d’Azure Machine Learning pour définir la propriété source_directory_data_store sur une banque de données de votre choix (par exemple, un stockage d’objets blob) sur des [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) qui sont fournies dans [ PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Par défaut, les étapes utilisent le stockage Fichier Azure comme banque de données de secours, ce qui peut entraîner des problèmes de limitation de bande passante lorsqu’un grand nombre d’étapes sont exécutées en même temps.

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

Azure Machine Learning service est maintenant en disponibilité générale.

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
  * Utilisez le dernier jeton d’exécution AML lors de la lecture et de l’écriture sur des magasins de données lors des exécutions à distance. Auparavant, si le jeton d’exécution AML était mis à jour dans Python, l’exécution de la préparation des données n’était pas mise à jour avec le jeton d’exécution AML mis à jour.
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
  + Découvrez comment [exécuter des prédictions par lots à l’aide de pipelines](how-to-run-batch-predictions.md)
+ Cible de calcul Azure Machine Learning
  + Les [exemples de notebooks](https://aka.ms/aml-notebooks) ont été mis à jour pour utiliser la nouvelle capacité de calcul managé.
  + [En savoir plus sur cette capacité de calcul](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portail Azure : nouvelles fonctionnalités
+ Créez et gérez des types de capacité de calcul [Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) dans le portail.
+ Surveillez l’utilisation du quota et [demandez un quota](how-to-manage-quotas.md) pour la capacité de calcul Azure Machine Learning.
+ Affichez l’état du cluster de capacité de calcul Azure Machine Learning en temps réel.
+ La prise en charge du réseau virtuel a été ajoutée pour la création d’une capacité de calcul Azure Machine Learning et d’Azure Kubernetes Service.
+ Exécutez à nouveau vos pipelines publiés avec les paramètres existants.
+ Nouvelles [chartes machine Learning automatisées](how-to-track-experiments.md#auto) pour les modèles de classification (élévation, gains, étalonnage, graphique d’importance des fonctionnalités avec l’explicabilité de modèle) et les modèles de régression (résidus et graphique d’importance des fonctionnalités avec l’explicabilité de modèle). 
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
   * Il peut désormais lire sur une cible de calcul à distance à l’aide d’un jeton AML.
   * Il n’échoue plus sur DSVM Linux.
   * Il ne plante plus quand des prédicats de chaîne contiennent des valeurs non-chaîne.
   * Il gère désormais les erreurs d’assertion quand le flux de données doit échouer correctement.
   * Il prend désormais en charge les emplacements de stockage montés dbutils sur Azure Databricks.

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Portail Azure 
Le portail Azure du service Azure Machine Learning contient les mises à jour suivantes :
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

## <a name="2018-10-01"></a>01-10-2018

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
  * Résolution de problèmes liés aux itérations ensemblistes.
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

Consultez la présentation [d’Azure Machine Learning service](../service/overview-what-is-azure-ml.md).
