---
title: 'Tutoriel : Entraîner un modèle à l’aide du Machine Learning automatisé'
description: Tutoriel sur la façon d’entraîner un modèle Machine Learning sans code dans Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: e219531a88787f19197a2e8c2a80040497c6dc1e
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901417"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Tutoriel : Entraîner un modèle Machine Learning sans code

Vous pouvez enrichir vos données dans les tables Spark avec de nouveaux modèles Machine Learning que vous entraînez à l’aide du [Machine Learning automatisé](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml). Dans Azure Synapse Analytics, vous pouvez sélectionner une table Spark dans l’espace de travail pour l’utiliser comme jeu de données d’entraînement afin de générer des modèles Machine Learning, et vous pouvez le faire sans code.

Dans ce tutoriel, vous apprenez à entraîner des modèles Machine Learning en utilisant une expérience sans code dans Azure Synapse Analytics Studio. Vous utilisez le Machine Learning automatisé dans Azure Machine Learning, au lieu de coder manuellement l’expérience. Le type de modèle que vous entraînez dépend du problème que vous essayez de résoudre.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- Un [espace de travail Azure Synapse Analytics](../get-started-create-workspace.md). Vérifiez que le compte de stockage suivant est configuré en tant que stockage par défaut : Azure Data Lake Storage Gen2. Pour le système de fichiers Data Lake Storage Gen2 que vous utilisez, vérifiez que vous êtes **contributeur aux données Blob de stockage**.
- Un pool Apache Spark dans votre espace de travail Azure Synapse Analytics. Pour en savoir plus, voir [Démarrage rapide : Créer un pool SQL dédié à l’aide d’Azure Synapse Analytics Studio](../quickstart-create-sql-pool-studio.md).
- Un service lié Azure Machine Learning dans votre espace de travail Azure Synapse Analytics. Pour en savoir plus, voir [Démarrage rapide : Créer un service lié Azure Machine Learning dans Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Créer une table Spark pour un jeu de données d’entraînement

Pour ce tutoriel, vous avez besoin d’une table Spark. Le notebook suivant en crée une.

1. Téléchargez le notebook [Create-Spark-Table-NYCTaxi-Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importez le notebook dans Azure Synapse Analytics Studio.
![Capture d’écran d’Azure Synapse Analytics, avec l’option Importer mise en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Sélectionnez le pool Spark à utiliser, puis sélectionnez **Exécuter tout**. Cela permet d’obtenir les données de taxi de New York du jeu de données ouvert, puis de les enregistrer dans votre base de données Spark par défaut.
![Capture d’écran d’Azure Synapse Analytics, avec l’option Exécuter tout et la base de données Spark mises en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Une fois l’exécution du notebook terminée, vous voyez une nouvelle table Spark sous la base de données Spark par défaut. À partir de **Données**, recherchez la table nommée **nyc_taxi**.
![Capture d’écran de l’onglet Données d’Azure Synapse Analytics, avec la nouvelle table mise en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>Lancer l’Assistant Machine Learning automatisé

Voici comment procéder :

1. Cliquez avec le bouton droit sur la table Spark que vous avez créée à l’étape précédente. Pour ouvrir l’Assistant, sélectionnez **Machine Learning** > **Enrichir avec un nouveau modèle**.
![Capture d’écran de la table Spark, avec Machine Learning et Enrichir avec un nouveau modèle mis en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Vous pouvez ensuite fournir vos détails de configuration pour la création d’une exécution de l’expérience de Machine Learning automatisé dans Azure Machine Learning. Cette exécution entraîne plusieurs modèles et le meilleur modèle issu d’une exécution réussie est inscrit dans le registre de modèles Azure Machine Learning.

   ![Capture d’écran des spécifications de la configuration de l’option Enrichir avec un nouveau modèle.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Espace de travail Azure Machine Learning** : Un espace de travail Azure Machine Learning est nécessaire pour la création d’une exécution d’expérience de Machine Learning automatisé. Vous avez également besoin de lier votre espace de travail Azure Synapse Analytics à l’espace de travail Azure Machine Learning à l’aide d’un [service lié](quickstart-integrate-azure-machine-learning.md). Une fois tous les prérequis remplis, vous pouvez spécifier l’espace de travail Azure Machine Learning que vous voulez utiliser pour cette exécution automatisée.

    - **Nom de l’expérience** : spécifiez le nom de l’expérience. Quand vous soumettez une exécution de Machine Learning automatisé, vous fournissez un nom d’expérience. Les informations concernant l’exécution sont stockées sous cette expérience dans l’espace de travail Azure Machine Learning. Par défaut, une expérience est ainsi créée. Elle génère un nom proposé, mais vous pouvez également fournir le nom d’une expérience existante.

    - **Meilleur modèle** : Spécifiez le nom du meilleur modèle de l’exécution automatisée. Celui-ci est attribué au meilleur modèle qui est automatiquement enregistré dans le registre de modèles Azure Machine Learning après cette exécution. Une exécution de Machine Learning automatisé crée de nombreux modèles de Machine Learning. En fonction de la métrique principale que vous sélectionnez dans une étape ultérieure, ces modèles peuvent être comparés et le meilleur modèle peut être sélectionné.

    - **Colonne cible** : il s’agit de ce que le modèle est entraîné à prédire. Choisissez la colonne que vous voulez prédire. (Dans ce tutoriel, nous allons sélectionner la colonne numérique `fareAmount` comme colonne cible.)

    - **Pool Spark** : Pool Spark à utiliser pour l’exécution de l’expérience automatisée. Les calculs sont exécutés sur le pool que vous spécifiez.

    - **Détails de configuration Spark** : en plus du pool Spark, vous avez également la possibilité de fournir des détails sur la configuration de session.

1. Sélectionnez **Continuer**.

## <a name="choose-task-type"></a>Choisir le type de tâche

Sélectionnez le type de modèle Machine Learning pour l’expérience en fonction de la question à laquelle vous essayez de répondre. Étant donné que `fareAmount` est la colonne cible et qu’il s’agit d’une valeur numérique, sélectionnez **Régression** ici. Sélectionnez **Continuer**.

![Capture d’écran de l’option Enrichir avec un nouveau modèle, avec Régression mise en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Configurations supplémentaires

Si vous sélectionnez **Régression** ou **Classification** comme type de modèle dans la section précédente, les configurations suivantes sont disponibles :

- **Métrique principale** : Métrique utilisée pour mesurer les performances du modèle. Il s’agit de la métrique utilisée pour comparer les différents modèles créés lors de l’exécution automatisée et déterminer quel modèle a le mieux fonctionné.

- **Durée du travail de formation (heures)**  : durée maximale, en heures, pour l’exécution et l’entraînement des modèles lors d’une expérience. Notez que vous pouvez également fournir des valeurs inférieures à 1 (par exemple `0.5`).

- **Nombre maximal d'itérations simultanées** : Représente le nombre maximal d’itérations exécutées en parallèle.

- **Compatibilité du modèle ONNX** : Si vous activez cette option, les modèles entraînés par le Machine Learning automatisé sont convertis au format ONNX. Cette opération est particulièrement utile si vous voulez utiliser le modèle pour le scoring dans des pools SQL Azure Synapse Analytics.

Ces paramètres ont tous une valeur par défaut que vous pouvez personnaliser.
![Capture d’écran de configurations supplémentaires de l’option Enrichir avec un nouveau modèle.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Une fois toutes les configurations nécessaires effectuées, vous pouvez démarrer votre exécution automatisée. Vous pouvez choisir **Créer une exécution**, qui démarre votre exécution directement, sans code. Si vous préférez le code, vous pouvez aussi sélectionner **Ouvrir dans un notebook**. Cette option vous permet de voir le code qui crée l’exécution et d’exécuter le notebook.

>[!NOTE]
>Si vous sélectionnez **Prévision de série chronologique** comme type de modèle dans la section précédente, vous devez effectuer des configurations supplémentaires. Les prévisions ne prennent pas non plus en charge la compatibilité du modèle ONNX.

### <a name="create-run-directly"></a>Créer directement une exécution

Pour démarrer directement votre exécution de Machine Learning automatisé, sélectionnez **Démarrer l’exécution**. Vous voyez une notification indiquant que l’exécution est en cours de démarrage. Vous voyez ensuite une autre notification indiquant la réussite de l’opération. Vous pouvez aussi vérifier l’état dans Azure Machine Learning en sélectionnant le lien dans la notification.
![Capture d’écran de la notification réussie.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Créer une exécution avec un notebook

Pour générer un notebook, sélectionnez **Ouvrir dans un notebook**. Ensuite, sélectionnez **Exécuter tout**. Cela vous donne également la possibilité d’ajouter des paramètres supplémentaires à votre exécution de Machine Learning automatisé.

![Capture d’écran du notebook, avec l’option Exécuter tout mise en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Une fois que vous avez correctement envoyé l’exécution, vous voyez un lien vers l’exécution de l’expérience dans l’espace de travail Azure Machine Learning dans la sortie du notebook. Sélectionnez le lien pour superviser votre exécution automatisée dans Azure Machine Learning.
![Capture d’écran d’Azure Synapse Analytics, avec le lien mis en évidence.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Assistant de scoring de modèle Machine Learning pour les pools SQL dédiés](tutorial-sql-pool-model-scoring-wizard.md)
- [Démarrage rapide : Créer un service lié Azure Machine Learning dans Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
