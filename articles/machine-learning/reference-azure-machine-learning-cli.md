---
title: Extension CLI
titleSuffix: Azure Machine Learning
description: Découvrez l’extension CLI Azure Machine Learning pour l'interface de ligne de commande Azure. L’interface de ligne de commande Azure est un utilitaire de ligne de commande multiplateforme qui vous permet d'utiliser des ressources du cloud Azure. Grâce à l’extension de Machine Learning, vous pouvez utiliser Azure Machine Learning. L’interface de ligne de commande Machine Learning crée et gère des ressources telles que votre espace de travail, les magasins de données, les jeux de données, les pipelines, les modèles et les déploiements.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9b30ace8d7ae7a818611856d46b2c50ed8db06f7
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292448"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Utiliser l’extension CLI pour Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

L'interface CLI Azure Machine Learning est une extension pour l'[interface Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), une interface de ligne de commande multiplateforme pour la plateforme Azure. Cette extension fournit les commandes à utiliser avec Azure Machine Learning. Il vous permet d’automatiser vos activités Machine Learning. La liste suivante fournit des exemples d’actions que vous pouvez effectuer avec l’extension CLI :

+ Exécuter des expérimentations pour créer des modèles Machine Learning

+ Inscrire des modèles Machine Learning pour l’usage des clients

+ Empaqueter, déployer et suivre le cycle de vie de vos modèles Machine Learning

L’interface CLI ne remplace en rien le kit de développement logiciel (SDK) Azure. Il s’agit d’un outil complémentaire optimisé pour gérer les tâches hautement paramétrables qui conviennent parfaitement à l’automatisation.

## <a name="prerequisites"></a>Conditions préalables requises

* Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Documents de référence complets

Recherchez les [documents de référence complets concernant l’extension azure-cli-ml d’Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Installer l’extension

Pour installer l'extension d'interface de ligne de commande Machine Learning, utilisez la commande suivante :

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Vous pouvez trouver des exemples de fichiers à utiliser avec les commandes ci-dessous [ici](https://aka.ms/azml-deploy-cloud).

Lorsque vous y êtes invité, sélectionnez `y` pour installer l’extension.

Pour vérifier que l’extension a été installée, utilisez la commande suivante afin d'afficher la liste des sous-commandes spécifiques à ML :

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Mettre à jour de l’extension

Pour mettre à jour l’extension d’interface de ligne de commande Machine Learning, utilisez la commande suivante :

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Supprimer l’extension

Pour supprimer l'extension CLI, utilisez la commande suivante :

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestion des ressources

Les commandes suivantes montrent comment utiliser l'interface de ligne de commande pour gérer les ressources utilisées par Azure Machine Learning.

+ Créez un groupe de ressources si vous n’en avez pas déjà un :

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Créez un espace de travail Azure Machine Learning :

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Cette commande crée un espace de travail d’édition de base. Pour créer un espace de travail d’entreprise, utilisez le commutateur `--sku enterprise` avec la commande `az ml workspace create`. Pour plus d’informations sur les éditions d’Azure Machine Learning, consultez [Présentation d’Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Pour plus d’informations, consultez [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Joignez la configuration d’un espace de travail à un dossier pour activer la prise en compte du contexte CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Cette commande crée un sous-répertoire `.azureml` qui contient des exemples de fichiers d’environnement runconfig et conda. Il contient également un fichier `config.json` utilisé pour communiquer avec votre espace de travail Azure Machine Learning.

    Pour plus d’informations, consultez [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Joignez un conteneur d’objets blob Azure en tant que magasin de données.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Pour plus d’informations, consultez [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Chargez les fichiers dans un magasin de données.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Pour plus d’informations, voir [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Joignez un cluster AKS en tant que cible de calcul.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Pour plus d’informations, consultez [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks).

+ Créez une nouvelle cible de calcul AML.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Pour plus d’informations, consultez [aaz ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Exécuter des expériences

* Exécutez votre expérimentation. Lorsque vous utilisez cette commande, spécifiez le nom du fichier runconfig (le texte avant \*.runconfig si vous regardez le système de fichiers) avec le paramètre - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > La commande `az ml folder attach` crée un sous-répertoire `.azureml` qui contient deux exemples de fichiers runconfig. 
    >
    > Si vous disposez d’un script Python qui crée un objet de configuration de série de tests par programmation, vous pouvez utiliser [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) pour l’enregistrer dans un fichier runconfig.
    >
    > Le schéma runconfig complet se trouve dans ce [fichier JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Le schéma s’auto-documente à l’aide de la clé `description` de chaque objet. En outre, il existe des énumérations pour les valeurs possibles et un extrait de modèle à la fin.

    Pour plus d’informations, consultez [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Afficher une liste des expériences :

    ```azurecli-interactive
    az ml experiment list
    ```

    Pour plus d’informations, consultez [az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gestion des jeux de données

Les commandes suivantes montrent comment utiliser des jeux de données dans Azure Machine Learning :

+ Inscrire un jeu de données :

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Pour obtenir des informations sur le format du fichier JSON utilisé pour définir le jeu de données, exécutez la commande `az ml dataset --show-template`.

    Pour plus d’informations, consultez [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archiver un jeu de données actif ou déprécié :

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Pour plus d’informations, consultez [az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Déprécier un jeu de données :

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Pour plus d’informations, consultez [az ml dataset deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Lister tous les jeux de données dans un espace de travail :

    ```azurecli-interactive
    az ml dataset list
    ```

    Pour plus d’informations, consultez [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Obtenir les détails d’un jeu de données :

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Pour plus d’informations, consultez [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Réactiver un jeu de données archivé ou déprécié :

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Pour plus d’informations, consultez [az ml dataset reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Désinscrire un jeu de données :

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Pour plus d’informations, consultez [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestion de l’environnement

Les commandes suivantes montrent comment créer, inscrire et lister des [environnements](how-to-configure-environment.md) Azure Machine Learning pour votre espace de travail :

+ Créer des fichiers de génération de modèles automatique pour un environnement :

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Pour plus d’informations, voir [az ml environment scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Inscrivez un environnement :

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Pour plus d’informations, voir [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Répertoriez les environnements inscrits :

    ```azurecli-interactive
    az ml environment list
    ```

    Pour plus d’informations, voir [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Téléchargez un environnement inscrit :

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Pour en savoir plus, voir [az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schéma de configuration de l’environnement

Si vous avez utilisé la commande `az ml environment scaffold`, elle génère un fichier `azureml_environment.json` de modèle qui peut être modifié et utilisé pour créer des configurations d’environnement personnalisées avec l’interface CLI. L’objet de niveau supérieur est mappé librement à la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) dans le SDK Python. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

Le tableau suivant détaille chaque champ de niveau supérieur dans le fichier JSON, son type et sa description. Si un type d’objet est lié à une classe du SDK Python, il existe une correspondance 1:1 libre entre chaque champ JSON et le nom de la variable publique dans la classe Python. Dans certains cas, le champ peut être mappé à un argument de constructeur plutôt qu’à une variable de classe. Par exemple, le champ `environmentVariables` est mappé à la variable `environment_variables` dans la classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).

| Champ JSON | Type | Description |
|---|---|---|
| `name` | `string` | Nom de l’environnement. Ne commencez pas le nom avec **Microsoft** ou **AzureML**. |
| `version` | `string` | Version de l’environnement. |
| `environmentVariables` | `{string: string}` | Table de hachage des noms et valeurs de variables d’environnement. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objet qui définit l’environnement et l’interpréteur Python à utiliser sur la ressource de calcul cible. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Définit des paramètres permettant de personnaliser l’image Docker construite pour les spécifications de l’environnement. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | La section configure les paramètres Spark. Elle est utilisée uniquement quand le framework a la valeur PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Configure les dépendances de bibliothèque Databricks. |
| `inferencingStackVersion` | `string` | Spécifie la version de pile d’inférence ajoutée à l’image. Pour éviter d’ajouter une pile d’inférence, laissez la valeur `null` dans ce champ. Valeur valide : « latest ». |

## <a name="ml-pipeline-management"></a>Gestion des pipelines ML

Les commandes suivantes montrent comment travailler avec des pipelines d’apprentissage automatique :

+ Créez un pipeline d’apprentissage automatique :

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Pour plus d’informations, consultez [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Pour plus d’informations sur le fichier YAML du pipeline, consultez [Define machine learning pipelines in YAML (Définir des pipelines d’apprentissage automatique YAML)](reference-pipeline-yaml.md).

+ Exécutez un pipeline :

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Pour plus d’informations, consultez [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Pour plus d’informations sur le fichier YAML du pipeline, consultez [Define machine learning pipelines in YAML (Définir des pipelines d’apprentissage automatique YAML)](reference-pipeline-yaml.md).

+ Planifiez un pipeline :

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Pour plus d’informations, consultez [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Pour plus d’informations sur le fichier YAML de planification du pipeline, consultez [Define machine learning pipelines in YAML (Définir des pipelines d’apprentissage automatique YAML)](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Inscription, profilage, déploiement du modèle

Les commandes suivantes montrent comment inscrire un modèle entraîné, puis le déployer en tant que service de production :

+ Inscrivez un modèle dans Azure Machine Learning :

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Pour plus d’informations, consultez [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **FACULTATIF** Profilez votre modèle pour obtenir des valeurs de processeur et de mémoire optimales pour le déploiement.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Pour plus d’informations, consultez [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Déployer votre modèle sur AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Pour plus d’informations sur le schéma du fichier de configuration d'inférence, consultez [Schéma de configuration de l’inférence](#inferenceconfig).
    
    Pour plus d’informations sur le schéma du fichier de configuration de déploiement, consultez [Schéma de configuration de déploiement](#deploymentconfig).

    Pour plus d’informations, consultez [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schéma de configuration de l’inférence

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schéma de configuration de déploiement

### <a name="local-deployment-configuration-schema"></a>Schéma de configuration de déploiement local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schéma de configuration de déploiement d’Azure Container Instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schéma de configuration de déploiement d’Azure Kubernetes Service

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur les commandes de l’extension CLI Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Former et déployer des modèles Machine Learning à l’aide d’Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
