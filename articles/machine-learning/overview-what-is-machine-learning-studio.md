---
title: Qu’est-ce que le studio Azure Machine Learning ?
description: Le studio est un portail web pour les espaces de travail Azure Machine Learning. Le studio combine des expériences sans code et Code First pour offrir une plateforme de science des données inclusive.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
adobe-target: true
ms.openlocfilehash: ca53c42c5c80a86e50db5ac52e6efee75c17cd8e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108770638"
---
# <a name="what-is-azure-machine-learning-studio"></a>Qu’est-ce que le studio Azure Machine Learning ?

Cet article vous fait découvrir le studio Azure Machine Learning, qui est le portail web destiné aux développeurs et scientifiques des données dans [Azure Machine Learning](overview-what-is-azure-ml.md). Le studio combine des expériences sans code et Code First pour offrir une plateforme de science des données inclusive.

Dans cet article, vous apprenez :
>[!div class="checklist"]
> - Comment [créer des projets de machine learning](#author-machine-learning-projects) dans le studio.
> - Comment [gérer les ressources et les actifs](#manage-assets-and-resources) dans le studio.
> - Les différences entre le [studio Azure Machine Learning et MS Studio (classique)](#ml-studio-classic-vs-azure-machine-learning-studio).

Nous vous recommandons d’utiliser le navigateur le plus récent compatible avec votre système d’exploitation. Les opérateurs suivants sont pris en charge :
  * Microsoft Edge (le nouveau Microsoft Edge, dernière version. Pas Microsoft Edge hérité)
  * Safari (dernière version, Mac uniquement)
  * Chrome (version la plus récente)
  * Firefox (version la plus récente)

## <a name="author-machine-learning-projects"></a>Créer des projets de machine learning

Le studio propose plusieurs expériences de création en fonction du type du projet et du niveau de l’expérience utilisateur.

+ **Blocs-notes**

  Écrivez et exécutez votre propre code dans les [serveurs Jupyter Notebook](how-to-run-jupyter-notebooks.md) managés qui sont directement intégrés au studio. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Capture d’écran : écrire et exécuter du code dans un notebook":::

+ **Concepteur Azure Machine Learning**

  Utilisez le concepteur pour entraîner et déployer des modèles Machine Learning sans écrire de code. Faites un glisser-déposer des jeux de données et des modules pour créer des pipelines ML. Essayez le [didacticiel du concepteur](tutorial-designer-automobile-price-train-score.md).

    ![Exemple de concepteur Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **IU de Machine Learning automatisé**

  Découvrez comment créer des [expériences de ML automatisé](tutorial-first-experiment-automated-ml.md) avec une interface simple à utiliser. 

  [![Volet de navigation d’Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Étiquetage des données**

    Utilisez l’[étiquetage des données Azure Machine Learning](how-to-create-labeling-projects.md) pour coordonner efficacement les projets d’étiquetage des données.

## <a name="manage-assets-and-resources"></a>Gérer les ressources et les actifs

Gérez vos ressources Machine Learning directement dans votre navigateur. Les ressources sont partagées dans le même espace de travail entre le SDK et le studio pour une expérience simplifiée. Utilisez le studio pour gérer les ressources suivantes :

- Modèles
- Groupes de données
- Magasins de données
- Ressources de calcul
- Notebooks
- Expériences
- Journaux d’exécution
- Pipelines 
- Points de terminaison de pipeline

Même si vous êtes un développeur expérimenté, le studio peut vous aider à gérer les ressources de l’espace de travail.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (classique) et studio Azure Machine Learning

Lancé en 2015, **ML Studio (classique)** a été notre premier concepteur Machine Learning de type glisser-déposer. 

**ML Studio (classic)** est un service autonome qui offre seulement une expérience visuelle. Studio (classique) n’interagit pas avec Azure Machine Learning.

**Azure Machine Learning** est un service distinct et moderne qui fournit une plateforme de science des données complète. Il prend en charge à la fois les expériences Code First et celles avec peu de code.

Le **studio Azure Machine Learning** est un portail web *dans* Azure Machine Learning qui propose des options sans code ou avec peu de code pour créer des projets et gérer les ressources. 

Nous recommandons aux nouveaux utilisateurs de choisir **Azure Machine Learning** au lieu de ML Studio (classique) pour bénéficier de tous les outils de science des données les plus récents. Si vous utilisez déjà ML Studio (classic), pensez à la [migration vers Azure Machine Learning](classic/migrate-overview.md).

Voici quelques-uns des avantages que vous tirerez du passage à Azure Machine Learning :

- Clusters de calcul scalables pour un entraînement à grande échelle
- Sécurité et gouvernance d’entreprise
- Interopérabilité avec de nombreux outils open source populaires
- MLOps de bout en bout

### <a name="feature-comparison"></a>Comparaison des fonctionnalités

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

* **Éléments de l’interface utilisateur manquants dans Studio** Le contrôle d’accès en fonction du rôle Azure peut permettre de limiter les actions possibles avec Azure Machine Learning. Ces restrictions sont susceptibles d’empêcher l’affichage d’éléments de l’interface utilisateur dans Azure Machine Learning studio. Par exemple, si le rôle qui vous est attribué ne permet pas de créer d’instance de calcul, l’option de création d’une instance de calcul n’apparaîtra pas dans Studio. Pour plus d’informations, consultez [Gestion des utilisateurs et des rôles](how-to-assign-roles.md).

## <a name="next-steps"></a>Étapes suivantes

Visitez le [studio](https://ml.azure.com), ou explorez les différentes options de création avec ces tutoriels :  

Commencez par [Démarrage rapide : Bien démarrer avec Azure Machine Learning](quickstart-create-resources.md).  Utilisez ensuite ces ressources pour créer votre première expérience avec votre méthode favorite :

  + [Exécuter un script Python « Hello World! » (partie 1 sur 3)](tutorial-1st-experiment-hello-world.md)
  + [Utiliser un notebook Jupyter pour entraîner des modèles de classification d’images](tutorial-train-models-with-aml.md)
  + [Utiliser le Machine Learning automatisé pour entraîner et déployer des modèles](tutorial-first-experiment-automated-ml.md)  
  + [Utiliser le concepteur pour entraîner et déployer des modèles](tutorial-designer-automobile-price-train-score.md)
  + [Utiliser le studio dans un réseau virtuel sécurisé](how-to-enable-studio-virtual-network.md)