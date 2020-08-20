---
title: Problèmes connus et résolutions
titleSuffix: Azure Machine Learning
description: Obtenez de l’aide pour trouver et corriger des erreurs ou des défaillances dans Azure Machine Learning. Découvrez les problèmes connus et leur mode de résolution ainsi que des solutions de contournement.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/06/2020
ms.openlocfilehash: 17d6137dd243c3bce011a1841ea9bca64e0b64ba
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120760"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Problèmes connus et résolution des problèmes dans Azure Machine Learning

Cet article vous aide à résoudre les problèmes connus que vous pouvez rencontrer durant l’utilisation d’Azure Machine Learning. 

Pour plus d’informations sur la résolution des problèmes, consultez [Étapes suivantes](#next-steps) à la fin de cet article.

> [!TIP]
> Les erreurs ou autres problèmes rencontrés sont parfois dus aux [quotas de ressources](how-to-manage-quotas.md) auxquels vous êtes confrontés quand vous travaillez avec Azure Machine Learning. 

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Pour afficher certains journaux : 
1. Visitez [Azure Machine Learning Studio](https://ml.azure.com). 
1. Sur le côté gauche, sélectionnez **Expérience**. 
1. Sélectionner une expérience.
1. Sélectionnez une exécution.
1. En haut, sélectionnez **Sorties + journaux**.

> [!NOTE]
> Azure Machine Learning consigne des informations de diverses sources pendant la formation, telles que AutoML ou le conteneur Docker qui exécute le travail de formation. La plupart de ces journaux ne sont pas documentés. Si vous rencontrez des problèmes et que vous contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.


## <a name="installation-and-import"></a>Installation et importation
                           
* **Installation de pip : il n’est pas garanti que les dépendances soient cohérentes avec l’installation monoligne :** 

   Il s’agit d’une limitation connue de pip, car il ne dispose pas d’un outil de résolution des dépendances fonctionnel lorsque vous effectuez une installation à partir d’une ligne unique. La première dépendance unique est la seule qu’il examine. 

   Dans le code suivant, `azureml-datadrift` et `azureml-train-automl` sont tous deux installés à l’aide d’une installation pip monoligne. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   Pour cet exemple, supposons que `azureml-datadrift` nécessite une version supérieure à 1.0 et que `azureml-train-automl` nécessite une version inférieure à 1.2. Si la dernière version de `azureml-datadrift` est 1.3, les deux packages sont mis à niveau vers la version 1.3, indépendamment de la nécessité d’une version antérieure pour le package `azureml-train-automl`. 

   Pour vous assurer que les versions appropriées sont installées pour vos packages, installez-les en utilisant plusieurs lignes de commande comme dans le code suivant. L’ordre n’est pas un problème ici, puisque pip passe explicitement à une version antérieure dans le cadre de l’appel de ligne suivant. Ainsi, les dépendances de version appropriées sont appliquées.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **L’installation du package d’explication n’est pas garantie durant l’installation de azureml-train-automl-client :** 
   
   Durant une exécution AutoML distante avec activation des explications du modèle, vous verrez le message d’erreur « Installez le package azureml-explain-model pour les explications du modèle. » Il s'agit d'un problème connu. Pour contourner ce problème, suivez l’une des étapes ci-dessous :
  
  1. Installez azureml-explain-model en local.
   ```
      pip install azureml-explain-model
   ```
  2. Désactivez entièrement la fonctionnalité d’explicabilité en passant model_explainability=False dans la configuration AutoML.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Erreurs Panda : généralement visibles pendant l’expérience AutoML :**
   
   Quand vous configurez manuellement votre environnement à l’aide de pip, vous pouvez remarquer des erreurs d’attributs (en particulier de Pandas) en raison de l’installation de versions de package non prises en charge. Pour éviter ce type d’erreur, [installez le Kit de développement logiciel (SDK) AutoML à l’aide de la commande automl_setup.cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md) :
   
    1. Ouvrez une invite Anaconda et clonez le référentiel GitHub pour un ensemble d’exemples de notebooks.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. Accédez au dossier how-to-use-azureml/automated-machine-learning où les exemples de notebooks ont été extraits, puis exécutez :
    
    ```bash
    automl_setup
    ```
    
* **KeyError: « brand » lors de l’exécution d’AutoML sur une capacité de calcul locale ou dans un cluster Azure Databricks**

    Si un nouvel environnement a été créé après le 10 juin 2020 à l’aide du kit de développement logiciel (SDK) 1.7.0 ou version antérieure, la formation peut échouer avec cette erreur en raison d’une mise à jour dans le package py-cpuinfo. (Les environnements créés jusqu’au 10 juin 2020 inclus ne sont pas affectés, tout comme les expériences exécutées sur une capacité de calcul distante, car des images de formation mises en cache sont utilisées.) Pour contourner ce problème, prenez l’une des deux mesures suivantes :
    
    * Mettez à jour la version du kit de développement logiciel (SDK) vers 1.8.0 ou une version ultérieure (py-cpuinfo revient alors à la version antérieure, soit 5.0.0) :
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Passez à la version antérieure de py-cpuinfo, soit 5.0.0 :
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Message d’erreur : Impossible de désinstaller « PyYAML »**

    Kit SDK Azure Machine Learning pour Python : PyYAML est un projet installé `distutils`. Par conséquent, nous ne pouvons pas déterminer avec précision les fichiers qui lui appartiennent en cas de désinstallation partielle. Pour poursuivre l’installation du kit de développement logiciel (SDK) en ignorant cette erreur, utilisez :
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Échec Databricks lors de l’installation des packages**

    L’installation du Kit de développement logiciel (SDK) Azure Machine Learning échoue sur Azure Databricks lorsque plusieurs packages sont installés. Certains packages, comme `psutil`, peuvent provoquer des conflits. Pour éviter les erreurs d’installation, installez les packages en bloquant la version des bibliothèques. Ce problème est lié à Azure Databricks et non au Kit de développement logiciel (SDK) Azure Machine Learning. Vous pouvez également rencontrer ce problème avec d’autres bibliothèques. Exemple :
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Vous pouvez également utiliser des scripts init si les problèmes d’installation persistent avec les bibliothèques Python. Cette approche n’est pas officiellement prise en charge. Pour plus d’informations, consultez [Scripts init à étendue au réseau en cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Erreur d’importation Databricks : impossible d’importer le nom `Timedelta` à partir de `pandas._libs.tslibs`**  : Si cette erreur apparaît lorsque vous utilisez le Machine Learning automatisé, exécutez les deux lignes suivantes dans votre notebook :
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Erreur d’importation Databricks : Aucun module nommé « pandas.core.indexes »**  : Si cette erreur s’affiche lorsque vous utilisez le machine learning automatisé :

    1. Exécutez cette commande pour installer deux packages dans votre cluster Azure Databricks :
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Détachez, puis réattachez le cluster à votre notebook.
    
    Si ces étapes ne résolvent pas le problème, essayez de redémarrer le cluster.

* **Databricks FailToSendFeather** : Si vous voyez une erreur `FailToSendFeather` lors de la lecture de données sur le cluster Azure Databricks, reportez-vous aux solutions suivantes :
    
    * Effectuez une mise à niveau du package `azureml-sdk[automl]` vers la dernière version.
    * Ajoutez la version 1.1.8 ou une version supérieure de `azureml-dataprep`.
    * Ajoutez la version 0.11 ou une version supérieure de `pyarrow`.
    
## <a name="create-and-manage-workspaces"></a>Créer et gérer des espaces de travail

> [!WARNING]
> Le déplacement de votre espace de travail Azure Machine Learning vers un autre abonnement, ou le déplacement de l’abonnement propriétaire vers un nouveau locataire, n’est pas pris en charge. En effet, cela peut provoquer des erreurs.

* **Portail Azure**: Si vous accédez directement à votre espace de travail à partir d’un lien de partage provenant du kit SDK ou du portail, vous ne pourrez pas afficher la page **Vue d’ensemble** normale comportant des informations sur l’abonnement dans l’extension. Vous ne pourrez pas non plus basculer sur un autre espace de travail. Si vous souhaitez afficher un autre espace de travail, accédez directement à [Azure Machine Learning Studio](https://ml.azure.com), puis à rechercher le nom de l’espace de travail.

* **Navigateurs pris en charge dans le portail web Azure Machine Learning Studio** : Nous vous recommandons d’utiliser le navigateur le plus récent compatible avec votre système d’exploitation. Les opérateurs suivants sont pris en charge :
  * Microsoft Edge (le nouveau Microsoft Edge, dernière version. Pas Microsoft Edge hérité)
  * Safari (dernière version, Mac uniquement)
  * Chrome (version la plus récente)
  * Firefox (version la plus récente)

## <a name="set-up-your-environment"></a>Configurer votre environnement

* **Problèmes de création d’une capacité AmlCompute** : Il peut arriver, dans de rares cas, que des utilisateurs ayant créé leur espace de travail Azure Machine Learning sur le Portail Azure avant la disponibilité générale ne puissent pas créer de capacité AmlCompute dans cet espace de travail. Vous pouvez créer une demande de support auprès du service ou créer un espace de travail sur le Portail ou avec le Kit de développement logiciel (SDK) pour vous débloquer sans délai.

## <a name="work-with-data"></a>Utilisation des données

### <a name="overloaded-azurefile-storage"></a>Stockage Fichier Azure surchargé

Si vous recevez une erreur `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, appliquez les solutions de contournement suivantes.

Si vous utilisez le partage de fichiers pour d’autres charges de travail, telles que le transfert de données, il est recommandé d’utiliser des objets blob afin de permettre l’utilisation du partage de fichiers pour l’envoi des exécutions. Vous pouvez également répartir la charge de travail entre deux espaces de travail.

### <a name="passing-data-as-input"></a>Passer des données en tant qu’entrée

*  **Type d’erreur : FileNotFound: Pas de fichier ou de répertoire correspondant** : Cette erreur se produit si le chemin d’accès au fichier que vous fournissez n’est pas l’emplacement du fichier. Vous devez vous assurer que la façon dont vous faites référence au fichier est cohérente avec l’emplacement où vous avez monté votre jeu de données sur votre cible de calcul. Pour garantir un état déterministe, nous vous recommandons d’utiliser le chemin d’accès abstrait lors du montage d’un jeu de données sur une cible de calcul. Par exemple, dans le code suivant, nous montons le jeu de données sous la racine du système de fichiers de la cible de calcul, `/tmp`. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Si vous n’incluez pas la barre oblique « / » de début, vous devez préfixer le répertoire de travail, par exemple `/mnt/batch/.../tmp/dataset`, sur la cible de calcul pour indiquer l’emplacement où vous souhaitez monter le jeu de données.

### <a name="data-labeling-projects"></a>Projets d'étiquetage de données

|Problème  |Résolution  |
|---------|---------|
|Seuls des jeux de données créés dans des magasins de données d’objets blob peuvent être utilisés.     |  Il s’agit d’une limitation connue de la version actuelle.       |
|Une fois la création effectuée, le projet affiche le message « Initialisation » pendant une longue période.     | Actualisez la page manuellement. L’initialisation doit se faire à environ 20 points de données par seconde. L’absence d’actualisation automatique est un problème connu.         |
|Au moment du passage en revue des images, les images récemment étiquetées ne sont pas affichées.     |   Pour charger toutes les images étiquetées, choisissez le bouton **Premier**. Le bouton **Premier** vous ramène au début de la liste, mais charge toutes les données étiquetées.      |
|Appuyer sur la touche Échap lors de l’étiquetage pour la détection d’objet crée une étiquette de taille zéro dans l’angle supérieur gauche. L’envoi d’étiquettes dans cet état échoue.     |   Pour supprimer l’étiquette, cliquez sur la croix en regard de celle-ci.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Superviseurs de dérive de données

Limitations et problèmes connus des superviseurs de dérive de données :

* L’intervalle de temps lors de l’analyse des données d’historique est limité à 31 intervalles du paramètre de fréquence du moniteur. 
* Limitation à 200 caractéristiques sauf si aucune liste de caractéristiques n’est spécifiée (toutes les caractéristiques sont utilisées).
* La taille de calcul doit être suffisamment grande pour gérer les données.
* Vérifiez que votre jeu de données contient des données comprises entre la date de début et la date de fin pour l’exécution d’un superviseur donné.
* Les analyses de jeu de données ne fonctionnent que sur les jeux de données qui contiennent 50 lignes ou plus.
* Dans le jeu de données, les colonnes, ou caractéristiques, sont classées comme catégories ou comme nombres, en fonction des conditions du tableau suivant. Si la caractéristique ne respecte pas ces conditions (par exemple, une colonne de type String avec plus de 100 valeurs uniques), elle est supprimée de notre algorithme de dérive des données, mais elle est toujours profilée. 

    | Type de caractéristique | Type de données | Condition | Limites | 
    | ------------ | --------- | --------- | ----------- |
    | Par catégorie | string, bool, int, float | Le nombre de valeurs uniques dans la caractéristique est inférieur à 100 et inférieur à 5 % du nombre de lignes. | La valeur null est traitée comme sa propre catégorie. | 
    | Numérique | int, float | Les valeurs de la caractéristique sont d’un type de données numérique et ne répondent pas aux conditions d’une caractéristique par catégorie. | Caractéristique supprimée si moins de 15 % des valeurs ont la valeur null. | 

* Si vous avez [créé un superviseur de dérive de données](how-to-monitor-datasets.md), mais que vous ne voyez pas les données sur la page **Superviseurs de jeux de données** dans Azure Machine Learning Studio, essayez les procédures suivantes.

    1. Vérifiez que vous avez sélectionné la bonne plage de dates en haut de la page.  
    1. Sous l’onglet **Superviseurs de jeux de données**, sélectionnez le lien d’expérience pour vérifier l’état d’exécution.  Le lien se trouve tout à droite du tableau.
    1. Si l’exécution s’est correctement déroulée, consultez les journaux des pilotes pour connaître le nombre de métriques qui ont été générées et voir s’il y a des messages d’avertissement.  Vous trouverez les journaux des pilotes sous l’onglet **Sortie + journaux** après avoir cliqué sur une expérience.

* Si la fonction `backfill()` du kit de développement logiciel (SDK) ne génère pas le résultat attendu, cela peut être dû à un problème d’authentification.  Lorsque vous créez le calcul à transmettre à cette fonction, n’utilisez pas `Run.get_context().experiment.workspace.compute_targets`.  Au lieu de cela, utilisez [ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) comme suit pour créer le calcul que vous transmettez à cette fonction `backfill()` : 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Concepteur Azure Machine Learning

* **Longue durée de préparation du calcul :**

La première fois que vous vous connectez ou créez une cible de calcul, la préparation peut prendre quelques minutes voire plus. 

À partir du collecteur de données du modèle, les données peuvent prendre jusqu’à 10 minutes pour arriver dans votre compte de stockage d’objets blob. Attendez 10 minutes avant de vous assurer que les cellules ci-dessous s’exécutent.

```python
import time
time.sleep(600)
```

## <a name="train-models"></a>Entraîner des modèles

* **ModuleErrors (aucun module nommé)**  :  Si vous rencontrez des erreurs de module (ModuleErrors) lors de la soumission d’expériences dans Azure ML, cela signifie que le script d’apprentissage attend qu’un package soit installé, mais qu’il n’est pas ajouté. Une fois que vous avez fourni le nom du package, Azure ML installe ce dernier dans l’environnement utilisé pour l’entraînement. 

    Si vous utilisez des [Estimateurs](concept-azure-machine-learning-architecture.md#estimators) pour soumettre des expériences, vous pouvez spécifier un nom de package à l’aide du paramètre `pip_packages` ou `conda_packages` dans l’estimateur en fonction de la source à partir de laquelle vous souhaitez installer le package. Vous pouvez également spécifier un fichier yml avec toutes vos dépendances à l’aide de `conda_dependencies_file` ou répertorier toutes vos exigences PIP dans un fichier txt à l’aide du paramètre `pip_requirements_file`. Si vous souhaitez remplacer l’image par défaut utilisée par l’estimateur dans votre propre objet d’environnement Azure ML, vous pouvez spécifier cet environnement par le bais du paramètre `environment` du constructeur estimateur.

    Azure ML fournit également des estimateurs spécifiques au framework pour TensorFlow, PyTorch, Chainer et SKLearn. À l’aide de ces estimateurs, assurez-vous que les dépendances d’infrastructure principales sont installées pour vous dans l’environnement utilisé pour la formation. Vous avez la possibilité de spécifier des dépendances supplémentaires comme décrit ci-dessus. 
 
    Azure ML a géré les images Docker dont le contenu est visible dans [AzureML Containers](https://github.com/Azure/AzureML-Containers).
    Les dépendances spécifiques à l’infrastructure sont répertoriées dans la documentation de l’infrastructure correspondante, [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) et [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Si vous pensez qu’un package particulier est suffisamment courant pour être ajouté dans des environnements et images gérés par Azure ML, signalez un problème GitHub dans [AzureML Containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (nom non défini), AttributeError (objet sans attribut)**  : Cette exception doit provenir de vos scripts d’apprentissage. Vous pouvez consulter les fichiers journaux du portail Azure pour obtenir des informations supplémentaire sur l’erreur de nom non défini ou d’attribut. À partir du Kit de développement logiciel (SDK), vous pouvez utiliser `run.get_details()` pour examiner le message d’erreur. Cette opération répertorie également tous les fichiers journaux générés pour votre exécution. Veillez à examiner votre script d’apprentissage et à corriger l’erreur avant de soumettre à nouveau votre exécution. 

* **Horovod a été arrêté** : Dans la plupart des cas, si vous rencontrez le message d’erreur « AbortedError : Horovod a été arrêté », cette exception signifie qu’une exception sous-jacente dans l’un des processus a entraîné l’arrêt de Horovod. Chaque rang dans le travail MPI obtient son propre fichier journal dédié dans Azure ML. Ces journaux sont nommés `70_driver_logs`. Dans le cas d’une formation distribuée, les noms de journaux sont suivis du suffixe `_rank` pour faciliter leur différenciation. Pour trouver l’erreur exacte qui a provoqué l’arrêt de Horovod, parcourez tous les fichiers journaux et recherchez `Traceback` à la fin des fichiers driver_log. L’un de ces fichiers indiquera l’exception sous-jacente réelle. 

* **Exécuter ou expérimenter la suppression** :  Les expériences peuvent être archivées à l’aide de la méthode [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) ou à partir de l’onglet Expérience dans le client Azure Machine Learning Studio via le bouton « Archiver l’expérience ». Cette action masque l’expérience des requêtes et des vues de liste, mais elle ne la supprime pas.

    La suppression définitive d’expériences ou d’exécutions individuelles n’est actuellement pas prise en charge. Pour plus d’informations sur la suppression de ressources d’espace de travail, consultez [Exporter ou supprimer vos données d’espace de travail du service Machine Learning](how-to-export-delete-data.md).

* **Le document de métriques est trop volumineux** : Azure Machine Learning comporte des limites internes sur la taille des objets métriques qui peuvent être consignés simultanément dans une exécution d’entraînement. Si vous rencontrez une erreur « Le document de métrique est trop volumineux » lors de l’enregistrement d’une mesure de liste, essayez de fractionner la liste en segments plus petits, par exemple :

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    En interne, Azure ML concatène les blocs portant le même nom de métrique dans une liste contiguë.

## <a name="automated-machine-learning"></a>Machine learning automatisé

* **TensorFlow** : Depuis la version 1.5.0 du Kit de développement logiciel (SDK), le Machine Learning automatisé n’installe pas de modèles TensorFlow par défaut. Pour installer TensorFlow et l’utiliser avec vos expériences de ML automatisé, installez tensorflow==1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Graphiques d’expérience** : Les graphiques de classification binaire (rappel de précision, ROC, obtenir la courbe, etc.) indiqués dans les itérations d’expériences ML automatisées ne sont pas correctement rendus dans l’interface utilisateur depuis le 12/04. Les tracés de graphique affichent actuellement les résultats inverses, ainsi les modèles plus performants sont affichés avec les résultats les plus bas. Une résolution est en cours d’investigation.

* **Annuler l’exécution d’un machine learning automatisé Databricks** : Si vous utilisez les fonctionnalités de machine learning automatisé sur Azure Databricks et souhaitez annuler une exécution pour en démarrer une nouvelle à des fins d’expérimentation, redémarrez votre cluster Azure Databricks.

* **>10 itérations d’apprentissage automatique automatisé Databricks** : Dans les paramètres de machine learning automatisé, si vous avez plus de 10 itérations, définissez `show_output` sur `False` lorsque vous soumettez l’exécution.

* **Widget Databricks pour le Kit de développement logiciel (SDK) Azure Machine Learning et le Machine Learning automatisé** : Le widget du Kit de développement logiciel (SDK) Azure Machine Learning n’est pas pris en charge dans un notebook Azure Databricks, car les notebooks ne peuvent pas analyser les widgets HTML. Vous pouvez afficher le widget dans le portail à l’aide de ce code Python dans la cellule du notebook Azure Databricks :

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **Échec d’automl_setup** : 
    * Sur Windows, exécutez automl_setup à partir d’une invite Anaconda. Pour installer Miniconda, cliquez [ici](https://docs.conda.io/en/latest/miniconda.html).
    * Assurez-vous que Conda 64 bits est installé au lieu de la version 32 bits à l’aide de la commande `conda info`. La `platform` doit être `win-64` pour Windows ou `osx-64` pour Mac.
    * Assurez-vous que Conda 4.4.10 ou une version ultérieure est installé. Vous pouvez vérifier la version à l’aide de la commande `conda -V`. Si une version précédente est installée, vous pouvez la mettre à jour à l’aide de la commande : `conda update conda`.
    * Linux – `gcc: error trying to exec 'cc1plus'`
      *  Si l’erreur `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` est rencontrée, installez le package build-essentials à l’aide de la commande `sudo apt-get install build-essential`.
      * Transmettez un nouveau nom comme premier paramètre à automl_setup pour créer un nouvel environnement Conda. Affichez les environnements Conda existants à l’aide de `conda env list` et supprimez-les avec `conda env remove -n <environmentname>`.
      
* **Échec d’automl_setup_linux.sh** : Si automl_setup_linus.sh échoue sur Ubuntu Linux avec l’erreur : `unable to execute 'gcc': No such file or directory`-
  1. Assurez-vous que les ports de sortie 53 et 80 sont activés. Sur une machine virtuelle Azure, vous pouvez le faire à partir du portail Azure en sélectionnant la machine virtuelle et en cliquant sur Mise en réseau.
  2. Exécutez la commande `sudo apt-get update`
  3. Exécutez la commande `sudo apt-get install build-essential --fix-missing`
  4. Exécutez de nouveau `automl_setup_linux.sh`

* **Échec de configuration.ipynb**  :
  * Pour un environnement Conda local, assurez-vous d’abord qu’automl_setup s’est bien exécutée.
  * Assurez-vous que le paramètre subscription_id est correct. Recherchez le subscription_id dans le portail Azure en sélectionnant Tous les services, puis Abonnements. Les caractères « < » et « > » ne doivent pas être inclus dans la valeur subscription_id. Par exemple, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` a le format valide.
  * Assurez-vous que le Contributeur ou le Propriétaire a accès à l’abonnement.
  * Vérifiez que la région est l’une des régions prises en charge : `eastus2`, `eastus`, `westcentralus`, `southeastasia`, `westeurope`, `australiaeast`, `westus2`, `southcentralus`.
  * Vérifiez l’accès à la région à l’aide du portail Azure.
  
* **Échec de l’importation d’AutoMLConfig** : La version 1.0.76 de Machine Learning automatisé a fait l’objet de modifications qui nécessitent de désinstaller la version précédente avant de passer à la nouvelle version. Si `ImportError: cannot import name AutoMLConfig` est rencontré après une mise à niveau à partir d’une version du Kit de développement logiciel (SDK) antérieure à v1.0.76 vers v1.0.76 ou une version ultérieure, résolvez l’erreur en exécutant : `pip uninstall azureml-train automl` puis `pip install azureml-train-auotml`. Le script automl_setup.cmd le fait automatiquement. 

* **Échec de workspace.from_config** : Si l’appel ws = Workspace.from_config()' échoue -
  1. Assurez-vous que le notebook configuration.ipynb s’est exécuté correctement.
  2. Si le notebook est exécuté à partir d’un dossier qui ne se trouve pas sous le dossier dans lequel `configuration.ipynb` a été exécuté, copiez le dossier aml_config et le fichier config.json qu’il contient dans le nouveau dossier. Workspace.from_config lit le fichier config.json pour le dossier du notebook ou son dossier parent.
  3. Si vous utilisez un nouvel abonnement, un nouveau groupe de ressources, un nouvel espace de travail ou une nouvelle région, veillez à exécuter à nouveau le notebook `configuration.ipynb`. La modification directe de config.json ne fonctionnera que si l’espace de travail existe déjà dans le groupe de ressources spécifié sous l’abonnement spécifié.
  4. Si vous souhaitez modifier la région, modifiez l’espace de travail, le groupe de ressources ou l’abonnement. `Workspace.create` ne crée pas ni ne met à jour un espace de travail s’il existe déjà, même si la région spécifiée est différente.
  
* **Échec de l’exemple de notebook** : Si un exemple de notebook échoue avec une erreur indiquant que la propriété, la méthode ou la bibliothèque n’existe pas :
  * Assurez-vous que le noyau approprié a été sélectionné dans le notebook Jupyter. Le noyau est affiché en haut à droite de la page du notebook. La valeur par défaut est azure_automl. Notez que le noyau est enregistré dans le cadre du notebook. Par conséquent, si vous basculez vers un nouvel environnement Conda, vous devrez sélectionner le nouveau noyau dans le notebook.
      * Pour Azure Notebooks, il doit s’agir de Python 3.6. 
      * Pour les environnements Conda locaux, il doit s’agir du nom de l’environnement Conda que vous avez spécifié dans automl_setup.
  * Assurez-vous que le notebook est destiné à la version du Kit de développement logiciel (SDK) que vous utilisez. Vous pouvez vérifier la version du Kit de développement logiciel (SDK) en exécutant `azureml.core.VERSION` dans une cellule de notebook Jupyter. Vous pouvez télécharger la version précédente des exemples de notebooks à partir de GitHub en cliquant sur le bouton `Branch`, en sélectionnant l’onglet `Tags`, puis en sélectionnant la version.

* **L’importation de NumPy échoue dans Windows** : Certains environnements Windows rencontrent une erreur lors du chargement de NumPy avec la dernière version de Python 3.6.8. Si vous rencontrez ce problème, essayez avec la version 3.6.7 de Python.

* **L’importation de NumPy échoue** : Vérifiez la version de TensorFlow dans l’environnement Conda de Machine Learning automatisé. Les versions prises en charge sont celles antérieures à la version 1.13. Désinstallez TensorFlow de l’environnement s’il s’agit de la version 1.13 ou d’une version ultérieure. Vous pouvez vérifier la version de TensorFlow et la désinstaller comme suit :
  1. Démarrez un interpréteur de commandes, puis activez l’environnement Conda dans lequel les packages de ML automatisé sont installés.
  2. Entrez `pip freeze` et recherchez `tensorflow` : le cas échéant, la version indiquée doit être antérieure à la version 1.13.
  3. Si la version indiquée n’est pas une version prise en charge, exécutez `pip uninstall tensorflow` dans l’interpréteur de commandes et entrez « y » pour confirmer.

## <a name="deploy--serve-models"></a>Déployer et traiter des modèles

Effectuez ces actions pour les erreurs suivantes :

|Error  | Résolution  |
|---------|---------|
|Échec de génération d’image lors du déploiement de service web     |  Ajoutez « pynacl==1.2.1 » en tant que dépendance pip au fichier Conda pour la configuration d’image       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Remplacez la référence SKU des machines virtuelles utilisées dans votre déploiement par une référence SKU disposant de plus de mémoire. |
|Échec FPGA     |  Vous ne serez pas en mesure de déployer des modèles sur des FPGA tant que vous n’aurez pas demandé un quota FPGA et qu’il n’aura pas été approuvé. Pour demander un accès, remplissez le formulaire de demande de quota : https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Mise à jour des composants d’Azure Machine Learning dans le cluster AKS

Les mises à jour des composants Azure Machine Learning installés dans un cluster Azure Kubernetes Service doivent être appliquées manuellement. 

Vous pouvez appliquer ces mises à jour en détachant le cluster de l’espace de travail Azure Machine Learning, puis en rattachant le cluster à l’espace de travail. Si le protocole TLS est activé dans le cluster, vous devrez fournir le certificat et la clé privée TLS/SSL lors du rattachement du cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Si vous n’avez plus le certificat et la clé privée TLS/SSL, ou si vous utilisez un certificat généré par Azure Machine Learning, vous pouvez récupérer les fichiers avant de détacher le cluster en vous connectant au cluster avec `kubectl` et en extrayant le secret `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes stocke les secrets dans un format encodé en base 64. Vous devez décoder en base 64 les composants `cert.pem` et `key.pem` des secrets avant de les fournir à `attach_config.enable_ssl`. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Défaillances des WebServices dans Azure Kubernetes Service

De nombreuses défaillances de WebService dans Azure Kubernetes Service peuvent être déboguées en se connectant au cluster à l’aide de `kubectl`. Vous pouvez obtenir `kubeconfig.json` pour un cluster Azure Kubernetes Service en exécutant

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Erreurs d’authentification

Si vous effectuez une opération de gestion sur une cible de calcul à partir d'un travail distant, vous recevrez l'une des erreurs suivantes : 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Par exemple, vous recevrez une erreur si vous essayez de créer ou de joindre une cible de calcul à partir d'un pipeline Machine Learning soumis en vue d'une exécution à distance.

## <a name="missing-user-interface-items-in-studio"></a>Éléments de l’interface utilisateur manquants dans Studio

Le contrôle d’accès en fonction du rôle Azure peut permettre de limiter les actions possibles avec Azure Machine Learning. Ces restrictions sont susceptibles d’empêcher l’affichage d’éléments de l’interface utilisateur dans Azure Machine Learning Studio. Par exemple, si le rôle qui vous est attribué ne permet pas de créer d’instance de calcul, l’option de création d’une instance de calcul n’apparaîtra pas dans Studio.

Pour plus d’informations, consultez [Gestion des utilisateurs et des rôles](how-to-assign-roles.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres articles de résolution des problèmes liés à Azure Machine Learning :

* [Résolution des problèmes de déploiement Docker liés à Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Déboguer des pipelines de machine learning](how-to-debug-pipelines.md)
* [Déboguer la classe ParallelRunStep du kit SDK Azure Machine Learning](how-to-debug-parallel-run-step.md)
* [Débogage interactif d’une instance de Capacité de calcul Machine Learning avec VS Code](how-to-debug-visual-studio-code.md)
* [Utiliser Application Insights pour déboguer des pipelines de machine learning](how-to-debug-pipelines-application-insights.md)
