---
title: Créer et gérer une instance de calcul
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et gérer une instance de calcul Azure Machine Learning. Utilisez-la comme environnement de développement, ou comme cible de calcul à des fins de développement/test.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5aaaf2000fb8310a5208447f8eaf8546feda90fb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509317"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Créer et gérer une instance de calcul Azure Machine Learning

Découvrez comment créer et gérer une [instance de calcul](concept-compute-instance.md) dans votre espace de travail Azure Machine Learning.

Utilisez une instance de calcul comme environnement de développement complètement configuré et managé dans le cloud. Pour le développement et le test, vous pouvez également utiliser l’instance en tant que [cible de calcul d’entraînement](concept-compute-target.md#train) ou pour une [cible d’inférence](concept-compute-target.md#deploy).   Une instance de calcul peut exécuter plusieurs travaux en parallèle et dispose d’une file d’attente de travaux. En tant qu’environnement de développement, une instance de calcul ne peut pas être partagée avec d’autres utilisateurs dans votre espace de travail.

Dans cet article, vous apprendrez comment :

* Créer une instance de calcul 
* Gérer (démarrer, arrêter, redémarrer, supprimer) une instance de calcul
* Accéder à la fenêtre de terminal 
* Installer des packages R ou Python
* Créer des environnements ou des noyaux Jupyter

Les instances de calcul peuvent exécuter des travaux en toute sécurité dans un [environnement de réseau virtuel](how-to-secure-training-vnet.md), sans qu’il soit nécessaire pour les entreprises d’ouvrir des ports SSH. Le travail s’exécute dans un environnement conteneurisé et empaquette les dépendances de votre modèle dans un conteneur Docker. 

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro) ou l’[extension Azure Machine Learning pour Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Créer

**Durée estimée** : 5 minutes environ.

La création d’une instance de calcul est un processus unique pour votre espace de travail. Vous pouvez réutiliser le calcul en tant que station de travail de développement ou en tant que cible de calcul pour l’entraînement. Plusieurs instances de calcul peuvent être attachées à votre espace de travail.

Le quota de cœurs dédiés par région par famille de machine virtuelle et le quota régional total, qui s’appliquent à la création d’une instance de calcul, sont unifiés et partagés avec le quota de clusters de calcul d’entraînement Azure Machine Learning. L’arrêt de l’instance de calcul n’a pas pour effet de libérer le quota pour s’assurer que vous puissiez redémarrer l’instance de calcul. Notez qu’il n’est pas possible de changer la taille de machine virtuelle de l’instance de calcul une fois celle-ci créée.

L’exemple suivant montre comment créer une instance de calcul :

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Pour plus d’informations sur les classes, les méthodes et les paramètres utilisés dans cet exemple, consultez les documents de référence suivants :

* [Classe ComputeInstance](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Pour plus d’informations, consultez la documentation de référence sur [az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Dans votre espace de travail dans Azure Machine Learning Studio, créez une instance de calcul à partir de la section **Calcul** ou de la section **Blocs-notes** lorsque vous êtes prêt à exécuter l’un de vos blocs-notes.

Pour plus d’informations sur la création d’une instance de calcul dans le studio, consultez [Créer des cibles de calcul dans le studio Azure Machine Learning](how-to-create-attach-compute-studio.md#compute-instance).

---

Vous pouvez également créer une instance de calcul avec un [modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Créer au nom de (préversion)

En tant qu’administrateur, vous pouvez créer une instance de calcul au nom d’un scientifique des données et lui affecter l’instance avec :
* [Modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)  Pour plus d’informations sur la façon de trouver les valeurs TenantID et ObjectID nécessaires dans ce modèle, consultez [Rechercher des ID d’objet d’identité pour la configuration de l’authentification](../healthcare-apis/find-identity-object-ids.md).  Vous pouvez également trouver ces valeurs dans le portail Azure Active Directory.
* API REST

Le scientifique des données pour lequel vous créez l’instance de calcul doit disposer des [autorisations de contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) : 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

Le scientifique des données peut démarrer, arrêter et redémarrer l’instance de calcul. Il peut utiliser l’instance de calcul pour :
* Jupyter
* JupyterLab
* RStudio
* Notebooks intégrés

## <a name="manage"></a>Gérer

Démarrez, arrêtez, redémarrez et supprimez une instance de calcul. Une instance de calcul ne fait pas automatiquement l’objet d’un scale-down. Veillez donc à arrêter la ressource pour éviter les frais récurrents.

# <a name="python"></a>[Python](#tab/python)

Dans les exemples ci-dessous, le nom de l’instance de calcul est **instance**.

* Obtenir l’état

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Arrêter

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Démarrer

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Redémarrer

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Supprimer

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Dans les exemples ci-dessous, le nom de l’instance de calcul est **instance**.

* Arrêter

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

* Démarrer 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

* Redémarrer 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

* Supprimer

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Pour plus d’informations, consultez [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Dans votre espace de travail dans Azure Machine Learning Studio, sélectionnez **Calcul**, puis sélectionnez **Instance de calcul** en haut.

![Gérer une instance de calcul](./media/concept-compute-instance/manage-compute-instance.png)

Vous pouvez effectuer les actions suivantes :

* Créer une instance de calcul 
* Actualiser l’onglet des instances de calcul.
* Démarrer, arrêter et redémarrer une instance de calcul.  Vous payez l’instance chaque fois qu’elle s’exécute. Quand vous n’utilisez pas l’instance de calcul, arrêtez-la pour réduire les coûts. L’arrêt d’une instance de calcul a pour effet de la libérer. Ensuite, redémarrez-la quand vous en avez besoin.
* Supprimer une instance de calcul.
* Filtrer la liste des instances de calcul pour afficher uniquement celles que vous avez créées.

Pour chaque instance de calcul de votre espace de travail que vous avez créée (ou qui a été créée pour vous), vous pouvez :

* Accéder à Jupyter, JupyterLab et RStudio sur l’instance de calcul.
* Ajouter un accès SSH à une instance de calcul. L’accès SSH est désactivé par défaut, mais il peut être activé au moment de la création de l’instance de calcul. L’accès SSH s’effectue par le biais d’un mécanisme de clé publique/privée. L’onglet vous donne des informations sur la connexion SSH, telles que l’adresse IP, le nom d’utilisateur et le numéro de port.
* Obtenir des informations sur une instance de calcul spécifique, telles que l’adresse IP et la région.

---

Le [contrôle d'accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md) vous permet de contrôler les utilisateurs de l'espace de travail qui peuvent créer, supprimer, démarrer ou arrêter une instance de calcul. Tous les utilisateurs ayant les rôles Contributeur et Propriétaire dans l’espace de travail sont autorisés à créer, supprimer, démarrer, arrêter et redémarrer des instances de calcul dans tout l’espace de travail. Toutefois, seul le créateur d’une instance de calcul spécifique, ou l’utilisateur affecté si elle a été créée en son nom, est autorisé à accéder à Jupyter, JupyterLab et RStudio sur cette instance de calcul. Une instance de calcul est dédiée à un seul utilisateur disposant d’un accès racine et peut accéder au terminal par le biais de Jupyter/JupyterLab/RStudio. L’instance de calcul utilise une connexion mono-utilisateur et toutes les actions utilisent l’identité de cet utilisateur pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC) et l’attribution des exécutions d’expériences. L’accès SSH est contrôlé par le biais d’un mécanisme de clé publique/privée.

Ces actions peuvent être contrôlées par Azure RBAC :
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Étapes suivantes

* [Accéder au terminal d’une instance de calcul](how-to-access-terminal.md)
* [Créer et gérer des fichiers](how-to-manage-files.md)
* [Soumettre une exécution d’entraînement](how-to-set-up-training-targets.md)
