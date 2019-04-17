---
title: 'Démarrage rapide : exécuter un notebook sur votre propre serveur de notebooks'
titleSuffix: Azure Machine Learning service
description: Prise en main d’Azure Machine Learning service. Utilisez votre propre serveur de notebooks local pour essayer votre espace de travail.  Dans le cloud, l’espace de travail est le socle que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: ade0444110dbb23e762a869dc79805872c0f0bf0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281129"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utiliser votre propre serveur de notebooks pour démarrer avec Azure Machine Learning

Utilisez votre propre serveur de notebooks pour exécuter du code qui enregistre des valeurs dans l’[espace de travail Azure Machine Learning Service](concept-azure-machine-learning-architecture.md). Dans le cloud, l’espace de travail est le socle que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning avec Machine Learning.

Ce guide de démarrage rapide utilise votre propre environnement Python et votre serveur Jupyter Notebook. Pour lire un guide de démarrage rapide sans installer de SDK, consultez [Démarrage rapide : Utiliser un serveur de notebooks cloud pour démarrer avec Azure Machine Learning](quickstart-run-cloud-notebook.md) 

Regardez une vidéo de ce guide de démarrage rapide :

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Prérequis

* Un serveur de notebooks Python 3.6 avec le SDK Azure Machine Learning installé
* Un espace de travail Azure Machine Learning service
* Un fichier de configuration d’espace de travail (**aml_config/config.json**).

Obtenez tous ces prérequis à partir de [Créer un espace de travail Azure Machine Learning service](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>Utiliser l'espace de travail

Créez un script ou démarrez un notebook dans le même répertoire que votre fichier de configuration d’espace de travail. Exécutez du code qui utilise les API de base du SDK pour suivre les exécutions des expériences.

1. Créer une expérience dans l’espace de travail.
1. Journaliser une valeur unique dans l’expérience.
1. Journaliser une liste de valeurs dans l’expérience.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Afficher les résultats enregistrés

Une fois l’exécution terminée, vous pouvez afficher l’essai sur le Portail Azure. Pour imprimer une URL permettant d’accéder aux résultats de la dernière exécution, utilisez le code suivant :

```python
print(run.get_portal_url())
```

Ce code retourne un lien que vous pouvez utiliser pour afficher les valeurs enregistrées sur le portail Azure dans votre navigateur.

![Valeurs enregistrées dans le Portail Azure](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Supprimer des ressources 

>[!IMPORTANT]
>Vous pouvez utiliser les ressources que vous avez créées ici comme conditions préalables à l’utilisation d’autres didacticiels Machine Learning et articles de procédure.

Si vous ne souhaitez pas utiliser les ressources que vous avez créées dans cet article, supprimez-les pour éviter des frais inutiles.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé les ressources dont vous avez besoin pour expérimenter des modèles et les déployer. Vous avez exécuté du code dans un bloc-notes et exploré l’historique des exécutions de ce code dans votre espace de travail dans le cloud.

> [!div class="nextstepaction"]
> [Didacticiel : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)

Vous pouvez aussi explorer des [exemples plus avancés sur GitHub](https://aka.ms/aml-notebooks) ou consulter le [guide utilisateur du SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
