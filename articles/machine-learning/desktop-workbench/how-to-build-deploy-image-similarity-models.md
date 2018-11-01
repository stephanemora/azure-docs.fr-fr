---
title: Générez et déployez un modèle de similarité d’images à l’aide du package Azure Machine Learning de la vision par ordinateur.
description: Découvrez comment créer, entraîner, tester et déployer un modèle de similarité d’images de la vision par ordinateur à l’aide du package Azure Machine Learning de la vision par ordinateur.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093647"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Générer et déployer des modèles de similarité d’images avec Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Dans cet article, découvrez comment utiliser le package Azure Machine Learning de la vision par ordinateur (AMLPCV, Azure Machine Learning Package for Computer Vision), pour entraîner, tester et déployer un modèle de similarité d’images. Pour une vue d’ensemble de ce package et de sa documentation de référence détaillée, voir https://aka.ms/aml-packages/vision.

Un grand nombre de problèmes survenant dans le domaine de la vision par ordinateur peuvent être résolus au moyen de la similarité d’images. Ces problèmes incluent la création de modèles qui répondent aux questions, telles que :
+ _Y a-t-il un OBJET dans l’image ? Par exemple, un « chien », une « voiture », un « bateau », etc._
+ _Quel est le degré de gravité de la maladie oculaire mise en évidence par le scanner de la rétine de ce patient ?_

Lors de la génération et du déploiement de ce modèle avec AMLPCV, vous parcourez les étapes suivantes :
1. Création d’un jeu de données
2. Visualisation et annotation d’images
3. Augmentation d’images
4. Définition des modèles DNN (Deep Neural Network)
5. Formation du classifieur
6. Évaluation et visualisation
7. Déploiement de services web
8. Test de chargement de services web

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) est utilisé en tant que framework d’apprentissage profond, la formation est assurée localement sur une machine optimisée par GPU, comme l’instance ([Deep learning Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)) et le déploiement utilise Azure ML Operationalization CLI.

## <a name="prerequisites"></a>Prérequis

1. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

1. Les comptes et l’application suivants doivent être configurés et installés :
   - un compte Azure Machine Learning Expérimentation ; 
   - Un compte Azure Machine Learning - Gestion des modèles
   - Azure Machine Learning Workbench installé.

   Si ces trois éléments ne sont pas déjà créés ou installés, suivez le [Guide de démarrage rapide d’Azure Machine Learning et l’installation de Workbench](../desktop-workbench/quickstart-installation.md). 

1. Le package Azure Machine Learning de la vision par ordinateur doit être installé. Découvrez comment installer ce package, tel que décrit dans l’article https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Exemple de données et bloc-notes

### <a name="get-the-jupyter-notebook"></a>Se procurer le bloc-notes Jupyter

Téléchargez le bloc-notes pour exécuter vous-même l’exemple décrit ici.

> [!div class="nextstepaction"]
> [Se procurer le bloc-notes Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Charger l’exemple de données

L’exemple suivant utilise un jeu de données composé de 63 images de pièces de vaisselle. Chaque image est étiquetée comme appartenant à une des quatre classes distinctes : bowl (jatte), cup (gobelet), cutlery (couverts), plate (plat). Le nombre d’images de cet exemple est restreint pour que son exécution soit rapide. Dans la pratique, au moins 100 images par classe doivent être fournies. Toutes les images sont situées à l’emplacement suivant *".. /sample_data/imgs_recycling/"*, dans les sous-répertoires nommés "bowl", "cup", "cutlery" et "plate".

![Jeu de données Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le package Azure Machine Learning de la vision par ordinateur dans les articles suivants :

+ Découvrir comment [améliorer la précision de ce modèle](how-to-improve-accuracy-for-computer-vision-models.md).

+ Lire la [vue d’ensemble du package](https://aka.ms/aml-packages/vision).

+ Explorer la [documentation de référence](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) de ce package.

+ En savoir plus sur les [autres packages Python pour Azure Machine Learning](reference-python-package-overview.md).
