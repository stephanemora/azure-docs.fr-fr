---
title: Entraîner et déployer des modèles à partir de l’interface CLI
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser l’extension de machine learning pour Azure CLI pour entraîner, inscrire et déployer un modèle à partir de la ligne de commande.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb46aaf04535c1b44cdd80810fbb6382dc727a67
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350425"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Didacticiel : Entraîner et déployer un modèle à partir de l’interface CLI

Dans ce tutoriel, vous allez utiliser l’extension de machine learning pour Azure CLI pour entraîner, inscrire et déployer un modèle.

Les scripts d’entraînement Python présentés dans ce tutoriel utilisent [scikit-learn](https://scikit-learn.org/) pour entraîner un modèle simple. Ce tutoriel n’est pas centré sur les scripts ou le modèle, mais sur l’utilisation de l’interface CLI avec Azure Machine Learning.

Découvrez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Installer l’extension Machine Learning
> * Créer un espace de travail Azure Machine Learning
> * Créer la ressource de calcul utilisée pour entraîner le modèle
> * Lancer une exécution d’entraînement
> * Inscrire et télécharger un modèle
> * Déployer le modèle en tant que service web
> * Attribuer un score aux données à l’aide du service web

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Pour utiliser les commandes CLI dans ce document à partir de votre **environnement local**, vous avez besoin [d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Si vous utilisez [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l’interface CLI est accessible via le navigateur et réside dans le cloud.

## <a name="download-the-example-project"></a>Télécharger l’exemple de projet

Pour ce tutoriel, téléchargez le projet [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps). Les fichiers contenus dans les répertoires `model-training` et `model-deployment` sont utilisés par les étapes de ce tutoriel.

Pour obtenir une copie locale des fichiers, [téléchargez une archive .zip](https://github.com/microsoft/MLOps/archive/master.zip) ou utilisez la commande Git suivante pour cloner le dépôt :

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Fichiers d’entraînement

Le répertoire `model-training` contient les fichiers suivants, qui sont utilisés pendant l’entraînement d’un modèle :

* `.azureml\sklearn.runconfig`: fichier de __configuration d’exécution__. Ce fichier définit l’environnement d’exécution nécessaire à l’entraînement du modèle.
* `train-sklearn.py`: script d’entraînement. Ce fichier entraîne le modèle.
* `mylib.py`: module d’assistance utilisé par `train-sklearn.py`.
* `training-env.yml`: définit les dépendances logicielles nécessaires à l’exécution du script d’entraînement.

Le script d’entraînement utilise le jeu de données « diabetes » fourni avec scikit-learn pour entraîner un modèle.

### <a name="deployment-files"></a>Fichiers de déploiement

Le répertoire `model-deployment` contient les fichiers suivants, qui servent à déployer le modèle entraîné sous forme de service web :

* `aciDeploymentConfig.yml`: fichier de __configuration de déploiement__. Ce fichier définit l’environnement d’hébergement nécessaire au modèle.
* `inferenceConfig.yml`: fichier de configuration de l’inférence. Ce fichier définit l’environnement logiciel utilisé par le service pour attribuer un score aux données avec le modèle.
* `score.py`: script Python qui accepte les données entrantes, leur attribue un score en utilisant le modèle, puis retourne une réponse.
* `scoring-env.yml`: dépendances conda nécessaires à l’exécution du modèle et du script `score.py`.

## <a name="connect-to-your-azure-subscription"></a>Connectez-vous à un abonnement Azure

Il existe plusieurs façons de vous authentifier auprès de votre abonnement Azure à partir de l’interface CLI. La plus simple consiste à s’authentifier de manière interactive à l’aide d’un navigateur. Pour vous authentifier de manière interactive, ouvrez une ligne de commande ou un terminal et utilisez la commande suivante :

```azurecli-interactive
az login
```

Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Dans le cas contraire, vous devez ouvrir un navigateur et suivre les instructions de la ligne de commande. Les instructions impliquent de naviguer vers [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et d’entrer un code d’autorisation.

## <a name="install-the-machine-learning-extension"></a>Installer l’extension Machine Learning

Pour installer l’extension Machine Learning, utilisez la commande suivante :

```azurecli-interactive
az extension add -n azure-cli-ml
```

Si vous obtenez un message indiquant que l’extension est déjà installée, utilisez la commande suivante pour procéder à une mise à jour vers la dernière version :

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur de ressources de base sur la plateforme Azure. Quand vous utilisez le service Azure Machine Learning, le groupe de ressources contient votre espace de travail Azure Machine Learning service. Il contient aussi les autres services Azure utilisés par l’espace de travail. Par exemple, si vous entraînez votre modèle en utilisant une ressource de calcul basée sur le cloud, cette ressource est créée dans le groupe de ressources.

Pour __créer un groupe de ressources__, utilisez la commande suivante. Remplacez `<resource-group-name>` par le nom à utiliser pour ce groupe de ressources. Remplacez `<location>` par la région Azure à utiliser pour ce groupe de ressources :

> [!TIP]
> Vous devez sélectionner une région où Azure Machine Learning service est disponible. Pour plus d’informations, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La réponse de cette commande doit ressembler au JSON suivant :

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Pour plus d’informations sur l’utilisation des groupes de ressources, consultez [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Créer un espace de travail

Pour créer un espace de travail, utilisez la commande suivante. Remplacez `<workspace-name>` par le nom que vous voulez attribuer à cet espace de travail. Remplacez `<resource-group-name>` par le nom du groupe de ressources :

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Le résultat de cette commande doit ressembler au JSON suivant :

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Connecter le projet local à l’espace de travail

À partir d’un terminal ou d’une invite de commandes, utilisez les commandes suivantes pour spécifier le répertoire `mlops`, puis connectez-vous à votre espace de travail :

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Le résultat de cette commande doit ressembler au JSON suivant :

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Cette commande crée un fichier `.azureml/config.json`, qui contient les informations nécessaires pour se connecter à votre espace de travail. Sachant que les autres commandes `az ml` employées dans ce tutoriel utilisent ce fichier, vous n’avez pas besoin d’ajouter l’espace de travail et le groupe de ressources à toutes les commandes.

## <a name="create-the-compute-target-for-training"></a>Créer la cible de calcul pour l’entraînement

Cet exemple utilise une instance de calcul Azure Machine Learning pour entraîner le modèle. Pour créer une instance de calcul, utilisez la commande suivante :

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

Le résultat de cette commande doit ressembler au JSON suivant :

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Cette commande crée une cible de calcul nommée `cpu`, avec un maximum de quatre nœuds. La taille de machine virtuelle sélectionnée procure une machine virtuelle avec une ressource GPU. Pour plus d’informations sur la taille de machine virtuelle, consultez [Types et tailles de machine virtuelle].

> [!IMPORTANT]
> Le nom de la cible de calcul (`cpu` dans le cas présent) est important. Il est référencé par le fichier `.azureml/sklearn.runconfig` utilisé dans la section suivante.

## <a name="submit-the-training-run"></a>Soumettre l’exécution d’entraînement

Pour lancer une exécution d’entraînement sur la cible de calcul `cpu`, indiquez le répertoire `model-training`, puis utilisez la commande suivante :

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Cette commande spécifie un nom pour l’expérience (`myexperiment`). L’expérience stocke des informations sur cette exécution dans l’espace de travail.

Le paramètre `-c sklearn` spécifie le fichier `.azureml/sklearn.runconfig`. Comme indiqué précédemment, ce fichier contient des informations qui servent à configurer l’environnement utilisé par l’exécution de l’entraînement. Si vous inspectez ce fichier, vous verrez qu’il fait référence à la cible de calcul `cpu` que vous avez créée précédemment. Il indique aussi le nombre de nœuds à utiliser pendant l’entraînement (`"nodeCount": "4"`) et contient une section `"condaDependenciees"` qui liste les packages Python nécessaires à l’exécution du script d’entraînement.

Pour plus d’informations sur les fichiers de configuration d’exécution, consultez [Configurer et utiliser des cibles de calcul pour l’entraînement du modèle](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli).

Le paramètre `-t` stocke une référence à cette exécution dans un fichier JSON, qui servira dans les étapes suivantes à inscrire et télécharger le modèle.

Pendant le traitement de l’exécution d’entraînement, les informations de la session d’entraînement sont diffusées en continu sur la ressource de calcul distante. Une partie de ces informations sont similaires au texte suivant :

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Ce texte est consigné à partir du script d’entraînement (`train-sklearn.py`) et présente deux métriques de performances pour ce modèle. Dans ce cas, nous voulons le modèle qui présent la valeur alpha la plus élevée. Les métriques de performances sont propres au modèle que vous entraînez. Elles varient d’un modèle à l’autre.

Si vous examinez le fichier `train-sklearn.py`, vous remarquerez qu’il utilise aussi la valeur alpha au moment de stocker le ou les modèles entraînés dans le fichier. Dans ce cas, il entraîne plusieurs modèles. Celui dont la valeur alpha est la plus élevée doit être le meilleur. Dans la sortie et le code ci-dessus, le modèle dont la valeur d’alpha est 0,95 a été enregistré sous le nom `./outputs/ridge_0.95.pkl`

Le modèle a été enregistré dans le répertoire `./outputs` sur la cible de calcul où il a été entraîné (en l’occurrence, l’instance de calcul Azure Machine Learning dans le cloud Azure). Le processus d’entraînement charge automatiquement le contenu du répertoire `./outputs` de la cible de calcul où s’exerce l’entraînement vers votre espace de travail Azure Machine Learning. Il est stocké en tant que partie intégrante de l’expérience (`myexperiment` dans cet exemple).

## <a name="register-the-model"></a>Inscrire le modèle

Pour inscrire le modèle directement à partir de la version stockée dans votre expérience, utilisez la commande suivante :

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Cette commande inscrit le fichier `outputs/ridge_0.95.pkl` créé par l’exécution de l’entraînement en tant que nouvelle inscription de modèle nommée `mymodel`. `--assets-path` fait référence à un chemin dans une expérience. Dans ce cas, les informations sur l’expérience et l’exécution sont chargées à partir du fichier `runoutput.json` créé par la commande d’entraînement. `-t registeredmodel.json` crée un fichier JSON qui fait référence au nouveau modèle inscrit qui a été créé par cette commande, et est utilisé par les autres commandes CLI qui utilisent des modèles inscrits.

Le résultat de cette commande doit ressembler au JSON suivant :

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Gestion des versions des modèles

Notez le numéro de version retourné pour le modèle. La version est incrémentée chaque fois que vous inscrivez un nouveau modèle sous ce nom. Par exemple, vous pouvez télécharger le modèle et l’inscrire à partir d’un fichier local en utilisant les commandes suivantes :

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

La première commande télécharge le modèle inscrit dans le répertoire actif. Le nom de fichier est `ridge_0.95.pkl`, qui est le fichier que vous avez référencé quand vous avez inscrit le modèle. La deuxième commande inscrit le modèle local (`-p "ridge_0.95.pkl"`) sous le même nom que l’inscription précédente (`mymodel`). Cette fois, les données JSON retournées indiquent la version 2.

## <a name="deploy-the-model"></a>Déployer le modèle

Pour déployer un modèle, indiquez le répertoire `model-deployment`, puis utilisez la commande suivante :

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Il se peut que vous obteniez le message indiquant que la création du client Docker a échoué. Vous pouvez ignorer ce message. L’interface CLI peut déployer un service web dans un conteneur Docker local et recherche Docker. Dans ce cas, nous n’utilisons pas de déploiement local.

Cette commande déploie un nouveau service nommé `myservice`, en utilisant la version 1 du modèle que vous avez inscrit précédemment.

Le fichier `inferenceConfig.yml` fournit des informations sur la façon d’effectuer une inférence, comme le script d’entrée (`score.py`) et les dépendances logicielles. Pour plus d’informations sur la structure de ce fichier, consultez le [schéma de configuration de l’inférence](reference-azure-machine-learning-cli.md#inference-configuration-schema). Pour plus d’informations sur les scripts d’entrée, consultez [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

Le fichier `aciDeploymentConfig.yml` décrit l’environnement de déploiement utilisé pour héberger le service. La configuration du déploiement dépend du type de calcul que vous utilisez pour le déploiement. Dans ce cas, il s’agit d’une instance de conteneur Azure. Pour plus d’informations, consultez le [schéma de configuration de déploiement](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Le processus de déploiement prend plusieurs minutes avant d’aboutir.

> [!TIP]
> Dans cet exemple, Azure Container Instances est utilisé. Les déploiements vers ACI créent automatiquement la ressource ACI nécessaire. Si vous envisagez un déploiement sur Azure Kubernetes Service, vous devez au préalable créer un cluster AKS et le spécifier dans la commande `az ml model deploy`. Pour obtenir un exemple de déploiement sur AKS, consultez [Déployer un modèle sur un cluster Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

Après plusieurs minutes, des informations similaires au JSON suivant sont retournées :

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>URI de scoring

Le `scoringUri` retourné à la suite du déploiement correspond au point de terminaison REST d’un modèle déployé en tant que service web. Vous pouvez aussi obtenir cet URI à l’aide de la commande suivante :

```azurecli-interactive
az ml service show -n myservice
```

Cette commande retourne le même document JSON, avec le `scoringUri`.

Le point de terminaison REST peut être utilisé pour envoyer des données au service. Pour savoir comment créer une application cliente qui envoie des données au service, consultez [Utiliser un modèle Azure Machine Learning déployé en tant que service web](how-to-consume-web-service.md).

### <a name="send-data-to-the-service"></a>Envoyer des données au service

Même si vous pouvez créer une application cliente pour appeler le point de terminaison, l’interface CLI de machine learning propose un utilitaire qui peut faire office de client de test. Utilisez la commande suivante pour envoyer des données de test au service :

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

La réponse de la commande est similaire à `[4684.920839774082]`.

## <a name="clean-up-resources"></a>Supprimer des ressources

> [!IMPORTANT]
> Les ressources que vous avez créées peuvent être utilisées comme conditions préalables pour d’autres didacticiels de Azure Machine Learning et des articles de procédure.

### <a name="delete-deployed-service"></a>Supprimer le service déployé

Si vous prévoyez de continuer à utiliser l’espace de travail Azure Machine Learning, mais que vous souhaitez supprimer le service déployé pour réduire les coûts, utilisez la commande suivante :

```azurecli-interactive
az ml service delete -n myservice
```

Cette commande retourne un document JSON qui contient le nom du service supprimé. La suppression du service peut prendre plusieurs minutes.

### <a name="delete-the-training-compute"></a>Supprimer la cible de calcul d’entraînement

Si vous prévoyez de continuer à utiliser l’espace de travail Azure Machine Learning, mais que vous souhaitez supprimer la cible de calcul `cpu` créée pour l’entraînement, utilisez la commande suivante :

```azurecli-interactive
az ml computetarget delete -n cpu
```

Cette commande retourne un document JSON qui contient l’ID de la cible de calcul supprimée. La suppression de la cible de calcul peut prendre plusieurs minutes.

### <a name="delete-everything"></a>Tout supprimer

Si vous n’avez pas l’intention d’utiliser les ressources que vous avez créées, supprimez-les pour éviter des frais supplémentaires.

Pour supprimer le groupe de ressources et toutes les ressources Azure créées dans ce document, utilisez la commande suivante. Remplacez `<resource-group-name>` par le nom du groupe de ressources que vous avez créé précédemment :

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel sur Azure Machine Learning, vous avez utilisé l’interface CLI de machine learning pour les tâches suivantes :

> [!div class="checklist"]
> * Installer l’extension Machine Learning
> * Création d’un espace de travail Microsoft Azure Machine Learning
> * Créer la ressource de calcul utilisée pour entraîner le modèle
> * Lancer une exécution d’entraînement
> * Inscrire et télécharger un modèle
> * Déploiement du modèle en tant que service web
> * Attribuer un score aux données à l’aide du service web

Pour plus d’informations sur l’utilisation de l’interface CLI, consultez [Utiliser l’extension CLI pour Azure Machine Learning service](reference-azure-machine-learning-cli.md).