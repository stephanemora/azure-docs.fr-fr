---
title: Problèmes connus et résolutions
titleSuffix: Azure Machine Learning
description: Liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 2760033cd66e99a7a7f6d331e03c6f98c486d286
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231966"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problèmes connus et dépannage d’Azure Machine Learning

Cet article vous permet de rechercher et de corriger les erreurs ou défaillances que vous pouvez rencontrer lors de l’utilisation d’Azure Machine Learning.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Pour afficher certains journaux : 
1. Visitez [Azure Machine Learning Studio](https://ml.azure.com). 
1. Sur le côté gauche, sélectionnez **Expérience**. 
1. Sélectionner une expérience.
1. Sélectionnez une exécution.
1. En haut, sélectionnez **Sorties + journaux**.

> [!NOTE]
> Azure Machine Learning consigne des informations de diverses sources pendant la formation, telles que AutoML ou le conteneur Docker qui exécute le travail de formation. La plupart de ces journaux ne sont pas documentés. Si vous rencontrez des problèmes et que vous contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.


## <a name="resource-quotas"></a>Quotas de ressources

Découvrez plus d’informations sur les [quotas des ressources](how-to-manage-quotas.md) que vous pouvez rencontrer quand vous utilisez Azure Machine Learning.

## <a name="installation-and-import"></a>Installation et importation

* **Installation de pip : il n’est pas garanti que les dépendances soient cohérentes avec l’installation à ligne unique** : 

   Il s’agit d’une limitation connue de pip, car il ne dispose pas d’un outil de résolution des dépendances fonctionnel lorsque vous effectuez une installation à partir d’une ligne unique. La première dépendance unique est la seule qu’il examine. 

   Dans le code suivant, `azure-ml-datadrift` et `azureml-train-automl` sont tous deux installés à l’aide d’une installation pip à ligne unique. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   Pour cet exemple, supposons que `azure-ml-datadrift` nécessite une version supérieure à 1.0 et que `azureml-train-automl` nécessite une version inférieure à 1.2. Si la dernière version de `azure-ml-datadrift` est 1.3, les deux packages sont mis à niveau vers la version 1.3, indépendamment de la nécessité d’une version antérieure pour le package `azureml-train-automl`. 

   Pour vous assurer que les versions appropriées sont installées pour vos packages, installez-les en utilisant plusieurs lignes de commande comme dans le code suivant. L’ordre n’est pas un problème ici, puisque pip passe explicitement à une version antérieure dans le cadre de l’appel de ligne suivant. Ainsi, les dépendances de version appropriées sont appliquées.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```

* **Erreurs Panda : généralement visibles pendant l’expérience AutoML :**
   
   Lorsque vous configurez manuellement votre environnement à l’aide de pip, vous remarquerez des erreurs d’attributs (en particulier des pandas) dues à l’installation de versions non prises en charge des paquets. Pour éviter ce type d’erreur, [installez le Kit de développement logiciel (SDK) AutoML à l’aide de la commande automl_setup.cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md) :
   
    1. Ouvrez une invite Anaconda et clonez le référentiel GitHub pour un ensemble d’exemples de notebooks.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. Accédez au dossier how-to-use-azureml/automated-machine-learning où les exemples de notebooks ont été extraits, puis exécutez :
    
    ```bash
    automl_setup
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

* **Erreur d'importation Databricks : impossible d'importer le nom « Timedelta » à partir de « pandas._libs.tslibs »**  : Si cette erreur apparaît lorsque vous utilisez le Machine Learning automatisé, exécutez les deux lignes suivantes dans votre notebook :
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
|Seuls des jeux de données créés sur des magasins de données blob peuvent être utilisés     |  Il s’agit d’une limitation connue de la version actuelle.       |
|Après la création, le projet affiche le message « Initialisation » pendant une longue période de temps     | Actualisez la page manuellement. L’initialisation doit se faire à environ 20 points de données par seconde. L’absence d’actualisation automatique est un problème connu.         |
|Lors du passage en revue des images, les images récemment étiquetées ne sont pas affichées     |   Pour charger toutes les images étiquetées, choisissez le bouton **Premier**. Le bouton **Premier** vous ramène au début de la liste, mais charge toutes les données étiquetées.      |
|Appuyer sur la touche Échap lors de l’étiquetage pour la détection d’objet crée une étiquette de taille zéro dans l’angle supérieur gauche. L’envoi d’étiquettes dans cet état échoue.     |   Pour supprimer l’étiquette, cliquez sur la croix en regard de celle-ci.  |

## <a name="azure-machine-learning-designer"></a>Concepteur Azure Machine Learning

Problèmes connus :

* **Calcul de préparation de longue durée** : La première fois que vous vous connectez ou créez une cible de calcul, la préparation peut prendre quelques minutes voire plus. 

## <a name="train-models"></a>Entraîner des modèles

* **ModuleErrors (aucun module nommé)**  :  Si vous rencontrez des erreurs de module (ModuleErrors) lors de la soumission d’expériences dans Azure ML, cela signifie que le script d’apprentissage attend qu’un package soit installé, mais qu’il n’est pas ajouté. Une fois que vous avez fourni le nom du package, Azure ML installe le package dans l’environnement utilisé pour l’exécution de votre script d’apprentissage. 

    Si vous utilisez des [Estimateurs](concept-azure-machine-learning-architecture.md#estimators) pour soumettre des expériences, vous pouvez spécifier un nom de package à l’aide du paramètre `pip_packages` ou `conda_packages` dans l’estimateur en fonction de la source à partir de laquelle vous souhaitez installer le package. Vous pouvez également spécifier un fichier yml avec toutes vos dépendances à l’aide de `conda_dependencies_file` ou répertorier toutes vos exigences PIP dans un fichier txt à l’aide du paramètre `pip_requirements_file`. Si vous souhaitez remplacer l’image par défaut utilisée par l’estimateur dans votre propre objet d’environnement Azure ML, vous pouvez spécifier cet environnement par le bais du paramètre `environment` du constructeur estimateur.

    Azure ML fournit également des estimateurs spécifiques à l’infrastructure pour Tensorflow, PyTorch, Chainer et SKLearn. À l’aide de ces estimateurs, assurez-vous que les dépendances d’infrastructure principales sont installées pour vous dans l’environnement utilisé pour la formation. Vous avez la possibilité de spécifier des dépendances supplémentaires comme décrit ci-dessus. 
 
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

* **Flux de tenseur** : Le machine learning automatisé ne prend actuellement pas en charge la version 1.13 des flux de tenseur. Si vous installez cette version, les dépendances de package cesseront de fonctionner. Nous nous efforçons de résoudre ce problème dans une version ultérieure.

* **Graphiques d’expérience** : Les graphiques de classification binaire (rappel de précision, ROC, obtenir la courbe, etc.) indiqués dans les itérations d’expériences ML automatisées ne sont pas correctement rendus dans l’interface utilisateur depuis le 12/04. Les tracés de graphique affichent actuellement les résultats inverses, ainsi les modèles plus performants sont affichés avec les résultats les plus bas. Une résolution est en cours d’investigation.

* **Annuler l’exécution d’un machine learning automatisé Databricks** : Si vous utilisez les fonctionnalités de machine learning automatisé sur Azure Databricks et souhaitez annuler une exécution pour en démarrer une nouvelle à des fins d’expérimentation, redémarrez votre cluster Azure Databricks.

* **>10 itérations d’apprentissage automatique automatisé Databricks** : Dans les paramètres de machine learning automatisé, si vous avez plus de 10 itérations, définissez `show_output` sur `False` lorsque vous soumettez l’exécution.

* **Widget Databricks pour le Kit de développement logiciel (SDK) Azure Machine Learning et le Machine Learning automatisé** : Le widget du Kit de développement logiciel (SDK) Azure Machine Learning n’est pas pris en charge dans un notebook Azure Databricks, car les notebooks ne peuvent pas analyser les widgets HTML. Vous pouvez afficher le widget dans le portail à l’aide de ce code Python dans la cellule du notebook Azure Databricks :

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

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
