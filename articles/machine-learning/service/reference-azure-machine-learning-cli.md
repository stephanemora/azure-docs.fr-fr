---
title: À propos de l’extension CLI Azure Machine Learning
description: Découvrez l’extension CLI d’apprentissage automatique pour Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 5d14373b265ea30d235cc5bc7b87ee13c4fd8105
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991791"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Qu’est-ce que l’interface CLI Azure Machine Learning ?

L’extension Interface de ligne de commande (CLI) Azure Machine Learning est destinée aux scientifiques des données et aux développeurs qui utilisent le service Azure Machine Learning. Elle permet d’automatiser rapidement les workflows de machine learning et de les mettre en production, comme :
+ Exécuter des expérimentations pour créer des modèles Machine Learning

+ Inscrire des modèles Machine Learning pour l’usage des clients

+ Empaqueter, déployer et suivre le cycle de vie de vos modèles Machine Learning

Cette interface CLI de Machine Learning est une extension de l’interface [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Elle repose sur le <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> basé sur Python pour le service Azure Machine Learning.

> [!NOTE]
> L’interface CLI est actuellement en préversion et va être mise à jour.

## <a name="installing-and-uninstalling"></a>Installation et désinstallation

Vous pouvez installer l’interface CLI à l’aide de cette commande à partir de notre index PyPi en préversion :
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Vous pouvez supprimer l’interface CLI à l’aide de cette commande :
```AzureCLI
az extension remove -n azure-cli-ml
```

## <a name="using-the-cli-vs-the-sdk"></a>Comparaison de l’utilisation de l’interface CLI et du SDK
L’interface CLI est mieux adaptée à l’automation par une personne des opérations de développement, ou dans le cadre d’un pipeline de livraison et d’intégration continues. Elle est optimisée pour gérer les tâches peu fréquentes et très paramétrables. 

Voici quelques exemples :
- provisionnement du calcul
- soumission d’expérimentations paramétrables
- inscription de modèles, création d’images
- déploiement de services

Il est recommandé aux scientifiques des données d’utiliser le SDK Azure ML.

## <a name="common-machine-learning-cli-commands"></a>Commandes CLI de machine learning courantes

Utilisez les nombreuses commandes `az ml` pour interagir avec le service dans n’importe quel environnement de ligne de commande, dont le service Cloud Shell du portail Azure.

Voici un exemple de commandes courantes :

### <a name="workspace-creation--compute-setup"></a>Création d’un espace de travail et configuration du calcul

+ Créez un espace de travail Azure Machine Learning, la ressource de niveau supérieur pour le machine learning.
  ```AzureCLI
  az ml workspace create -n myworkspace -g myresourcegroup
  ```

+ Définissez l’interface CLI pour utiliser cet espace de travail par défaut.
```AzureCLI
az configure --defaults aml_workspace=myworkspace group=myresourcegroup
```

+ Créez une machine virtuelle DSVM (machine virtuelle Science des données) pour les modèles d’entraînement. Vous pouvez également créer des clusters BatchAI pour l’entraînement distribué.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Soumission d’expérimentations
+ Rattachez-vous à un projet (configuration d’exécution) pour la soumission d’une expérimentation. Cela permet de suivre vos exécutions d’expérimentations.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Soumettez une expérimentation sur le service Azure Machine Learning sur la cible de calcul de votre choix (cet exemple utilise une machine virtuelle Data Science VM).
  ```AzureCLI
  az ml run submit -c mydsvm train.py
  ```

+ Affichez une liste des expérimentations soumises.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Inscription de modèles, création d’images et déploiement

+ Inscrivez un modèle dans Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m mymodel.pkl  -w myworkspace -g myresourcegroup
  ```

+ Créez une image pour contenir votre modèle Machine Learning et vos dépendances. 
  ```AzureCLI
  az ml image create -n myimage -r python -m rfmodel.pkl -f score.py -c myenv.yml
  ```

+ Déployez votre modèle empaqueté sur des cibles, dont ACI et AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice -i myimage:1
  ```
    
## <a name="full-command-list"></a>Liste complète des commandes
La liste complète des commandes pour l’extension CLI (et leurs paramètres pris en charge) est disponible en exécutant ```az ml COMMANDNAME -h```. 
