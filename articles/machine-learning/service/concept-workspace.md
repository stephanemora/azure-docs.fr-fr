---
title: Qu’est un espace de travail
titleSuffix: Azure Machine Learning service
description: L’espace de travail est la ressource de niveau supérieur du service Azure Machine Learning. Il conserve un historique de toutes les exécutions de formation, y compris les journaux, mesures, sortie et un instantané de vos scripts. Ces informations vous permettent de déterminer qui s’exécutent de formation produit le meilleur modèle
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 3ecdf62cfed7d70873f3dc752bfacd134e367a90
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388949"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Qu’est un espace de travail du service Azure Machine Learning ?

L’espace de travail est la ressource de niveau supérieur pour le service Azure Machine Learning, en fournissant un emplacement centralisé pour travailler avec tous les artefacts que vous créez lorsque vous utilisez le service Azure Machine Learning.  L’espace de travail conserve un historique de toutes les exécutions de formation, y compris les journaux, mesures, sortie et un instantané de vos scripts. Vous utilisez ces informations pour déterminer quelle exécution d’entraînement produit le meilleur modèle.  

Une fois que vous avez un modèle que vous le souhaitez, enregistrez-le avec l’espace de travail. Vous ensuite utiliser le modèle inscrit et le score de scripts pour déployer sur Azure Container Instances, Azure Kubernetes Service, ou sur un tableau de portes à champ programmable (FPGA) comme un point de terminaison HTTP basé sur REST. Vous pouvez également déployer le modèle sur un appareil Azure IoT Edge en tant que module.

## <a name="taxonomy"></a>Taxonomie 

Le diagramme suivant représente une taxonomie de l’espace de travail :

[![Taxonomie de l’espace de travail](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Le diagramme montre les composants suivants d’un espace de travail :

+ Un espace de travail peut contenir [machines virtuelles de bloc-notes](quickstart-run-cloud-notebook.md), configurés avec l’environnement Python nécessaire pour exécuter Azure Machine Learning de ressources de cloud.
+ [Rôles d’utilisateur](how-to-assign-roles.md) vous permettent de partager votre espace de travail avec d’autres utilisateurs, les équipes ou les projets.
+ [Cibles de calcul](concept-azure-machine-learning-architecture.md#compute-target) sont utilisés pour exécuter vos expériences.
+ Lorsque vous créez l’espace de travail, [ressources associées](#resources) sont également créés pour vous.
+ [Expériences](concept-azure-machine-learning-architecture.md#experiment) sont les exécutions d’apprentissage vous permet de créer vos modèles.  Vous pouvez créer et exécuter des expériences avec
    + Le [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Le [automatisée des expériences d’apprentissage automatique (version préliminaire)](how-to-create-portal-experiments.md) section dans le portail Azure.
    + Le [interface visuelle (version préliminaire)](ui-concept-visual-interface.md).
+ [Pipelines](concept-azure-machine-learning-architecture.md#pipeline) sont des flux de travail réutilisables pour l’apprentissage et la REFORMATION de votre modèle.
+ [Jeux de données](concept-azure-machine-learning-architecture.md#dataset) facilitent la gestion des données que vous utilisez pour la création de formation et de pipeline de modèle.
+ Une fois que vous avez un modèle que vous souhaitez déployer, vous créez un [modèle inscrit](concept-azure-machine-learning-architecture.md#model-registry).
+ Utiliser le modèle inscrit et un script de notation pour créer un [déploiement](concept-azure-machine-learning-architecture.md#image-registry).

## <a name="tools-for-workspace-interaction"></a>Outils pour l’interaction de l’espace de travail

Vous pouvez interagir avec votre espace de travail comme suit :

+ Sur le web :
    + [Portail Azure](https://portal.azure.com)
    + Le [interface visuelle (version préliminaire)](ui-concept-visual-interface.md)
+ Dans Python à l’aide d’Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Sur la ligne de commande à l’aide de l’Azure Machine Learning [extension CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Apprentissage automatique avec un espace de travail

Tâches d’apprentissage automatique lire et/ou écrire d’artefacts à votre espace de travail. 

+ Exécutez une expérience pour former un modèle - écritures tester les résultats de l’exécution à l’espace de travail.
+ Utilisation automatisée ML pour former un modèle - écrit les résultats de formation dans l’espace de travail.
+ Inscrire un modèle dans l’espace de travail.
+ Déployer un modèle - utilise le modèle inscrit pour créer un déploiement.
+ Créer et exécuter des flux de travail réutilisables.
+ Vue apprentissage artefacts tels que des expériences, des pipelines, des modèles, des déploiements.
+ Modèles de suivi et le moniteur.

## <a name="workspace-management"></a>Gestion de l’espace de travail

Vous pouvez également effectuer les tâches de gestion d’espace de travail suivantes :

| Tâche de gestion d’espace de travail   | Portail              | Kit SDK         | Interface de ligne de commande        |
|---------------------------|------------------|------------|------------|
| Créer un espace de travail        | **&check;**     | **&check;** | **&check;** |
| Créer et gérer des ressources de calcul    | **&check;**   | **&check;** |  **&check;**   |
| Gérer l’accès de l’espace de travail    | **&check;**   | |  **&check;**    |
| Création d’une machine virtuelle Notebook | **&check;**   | |     |

Prise en main du service à [création d’un espace de travail](setup-create-workspace.md).

## <a name="resources"></a> Ressources associées

Lorsque vous créez un nouvel espace de travail, celui-ci crée automatiquement plusieurs ressources Azure qui sont utilisées par l’espace de travail :

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) : Enregistre les conteneurs docker que vous utilisez pendant la formation et lorsque vous déployez un modèle. Pour réduire les coûts, ACR est **chargées en différé** jusqu'à ce que les images de déploiement sont créés.
+ [Compte Stockage Azure](https://azure.microsoft.com/services/storage/) : Utilisé comme magasin de données par défaut pour l’espace de travail.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) : Stocke les informations de supervision concernant les modèles.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : Stocke les secrets qui sont utilisés par les cibles de calcul, ainsi que d’autres informations sensibles dont a besoin l’espace de travail.

> [!NOTE]
> En plus de créer de nouvelles versions, vous pouvez utiliser les services Azure existants.

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main le service Azure Machine Learning, consultez :

+ [Vue d’ensemble des services Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Créer un espace de travail](setup-create-workspace.md)
+ [Gérer un espace de travail](how-to-manage-workspace.md)
+ [Tutoriel : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
