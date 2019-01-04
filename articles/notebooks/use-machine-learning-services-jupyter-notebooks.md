---
title: Utiliser Azure Machine Learning Services dans Azure Notebooks
description: Vue d’ensemble des exemples de notebooks pour Azure Machine Learning Services que vous pouvez utiliser avec Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b6d3cbd56182b8dba3738f38798efa378c6b49d0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254824"
---
# <a name="use-azure-machine-learning-services-in-a-notebook"></a>Utiliser Azure Machine Learning Services dans un notebook

Azure Notebooks est préconfiguré avec l’environnement nécessaires pour travailler avec [Azure Machine Learning Services](/azure/machine-learning/service/). Vous pouvez facilement cloner un exemple de projet dans votre compte Notebooks pour explorer un large éventail de scénarios Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Cloner l’exemple dans votre compte

1. Connectez-vous à [Azure Notebooks](https://notebooks.azure.com/).
1. Sélectionnez **My Projects** (Mes projets) pour accéder au tableau de bord des projets.
1. Sélectionnez le bouton **Upload GitHub Repo** (Charger le dépôt GitHub) (flèche vers le haut) pour ouvrir la fenêtre contextuelle **Upload GitHub Repo** (Charger le dépôt GitHub).
1. Dans la fenêtre contextuelle, entrez `Azure/MachineLearningNotebooks` dans **GitHub repository** (Dépôt GitHub),indiquez un nom de projet dans **Project Name** (Nom du projet), par exemple « Azure ML Services », entrez un identificateur dans **Project ID** (ID de projet), désactivez **Public** si vous le souhaitez, puis sélectionnez **Import** (Importer).

    ![Importer un exemple Azure Machine Learning Notebook dans votre compte Notebooks](media/azureml-import-project.png)

1. Après une minute ou deux, Azure Notebooks vous redirige automatiquement vers le tableau de bord du nouveau projet.

## <a name="run-a-sample-notebook"></a>Exécuter un exemple de notebook

1. Sélectionnez **00 - configuration.ipynb** pour démarrer la section de configuration du notebook et suivez ses instructions pour créer un espace de travail Azure Machine Learning.

    - Étant donné qu’Azure Notebooks contient déjà les packages Python nécessaires, vous pouvez exécuter l’extrait de code de l’étape 2 de la configuration requise pour vérifier la version du SDK Azure ML.

1. Une fois la configuration terminée, sélectionnez **01.getting-started** pour naviguer dans le dossier contenant les treize différents exemples de notebooks. Notez que chacun d’eux est explicite.

## <a name="next-steps"></a>Étapes suivantes

La documentation Azure Machine Learning Services contient toutes sortes d’autres ressources qui vous guident tout au long de votre utilisation de Machine Learning Services dans des notebooks :

- [Démarrage rapide : Utilisation de Python pour démarrer avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Tutoriel 1 : Effectuer l’apprentissage d’un modèle de classification d’images avec Azure Machine Learning Service](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Tutoriel 2 : Déployer un modèle de classification d’images dans Azure Container Instances (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Didacticiel : Entraînement d’un modèle de classification par Machine Learning automatisé dans le service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Consultez également la documentation relative à [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
