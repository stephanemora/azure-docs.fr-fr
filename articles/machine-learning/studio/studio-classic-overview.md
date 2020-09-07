---
title: Qu’est-il possible de faire avec Machine Learning Studio (classique) ? - Azure
description: Machine Learning Studio (classique) est un outil de type glisser-déposer qui permet de créer rapidement des modèles à partir d’une bibliothèque d’algorithmes et de modules prêts à l’emploi.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 1677bb5e7d94b92376bc42308b7bbcb0a8c24f97
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89039307"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Qu’est-il possible de faire avec Machine Learning Studio (classique) ?

**S’APPLIQUE À :**  ![oui](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classique)   ![non](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (classique) est un outil de type glisser-déposer qui vous permet de créer, tester et déployer des modèles Machine Learning. Studio (classique) publie des modèles en tant que services web pouvant facilement être consommés par des applications personnalisées ou des outils BI comme Excel.


## <a name="studio-classic--interactive-workspace"></a>Espace de travail interactif de Studio (classique)

Pour développer un modèle d’analyse prédictive, vous utilisez généralement les données d’une ou plusieurs sources que vous transformez et analysez par diverses manipulations de données et fonctions statistiques. Vous générez ensuite un ensemble de résultats. Le développement d'un modèle de ce type est un processus itératif. Quand vous modifiez les diverses fonctions et leurs paramètres, vos résultats convergent jusqu'à ce que l'efficacité du modèle formé vous donne satisfaction.

Machine Learning Studio (classique) offre un espace de travail visuel et interactif qui vous permet de créer, tester et reproduire facilement un modèle d’analyse prédictive. Vous faites glisser des ***jeux de données*** et des ***modules*** d’analyse sur un canevas interactif, en les connectant ensemble pour former une ***expérience***, que vous exécutez dans Machine Learning Studio (classique). Pour affiner votre modèle, vous modifiez l’expérience, enregistrez une copie si vous le souhaitez et l’exécutez de nouveau. Quand vous êtes prêt, vous pouvez convertir votre ***expérience de formation*** en une ***expérience prédictive***, puis la publier en tant que ***service web*** afin que votre modèle soit accessible à d’autres.

Aucune programmation n'est nécessaire : visualisez la connexion des jeux de données et des modules pour construire votre modèle d'analyse prédictive.

![Diagramme de Machine Learning Studio (classique) : créez des expériences, lisez les données de nombreuses sources, écrivez des données évaluées, écrivez des modèles.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Télécharger le diagramme de la vue d’ensemble de ML Studio (classique)
Téléchargez le diagramme **Vue d’ensemble des fonctionnalités de Microsoft ML Studio (classique)** pour avoir une vue générale des fonctionnalités de Machine Learning Studio (classique). Imprimez le diagramme au format tabloïd (11 x 17 pouces) pour le conserver à portée de main.

**Téléchargez le diagramme ici : [Vue d’ensemble des fonctionnalités de Microsoft Machine Learning Studio (classique)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Vue d’ensemble des fonctionnalités de Microsoft Machine Learning Studio (classique)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Composants d’une expérience Studio (classique)
Une expérience se compose de jeux de données qui fournissent des données aux modules d'analyse que vous connectez ensemble pour construire un modèle d'analyse prédictive. En particulier, une expérience valide a les caractéristiques suivantes :

* L'expérience comporte au moins un jeu de données et un module
* Il est possible de connecter des jeux de données uniquement à des modules
* Les modules peuvent être connectés à des jeux de données ou à d'autres modules
* Tous les ports d'entrée des modules doivent comporter une connexion au flux de données
* Tous les paramètres obligatoires de chaque module doivent être configurés

Vous pouvez créer une expérience à partir de zéro, ou utiliser un exemple d’expérience existante comme modèle. Pour plus d’informations, consultez [Copier des exemples d’expérience pour créer des expériences d’apprentissage automatique](sample-experiments.md).

Pour obtenir un exemple de création d’une expérience, consultez [Créer une expérience dans Machine Learning Studio (classique)](create-experiment.md).

Pour obtenir une procédure pas à pas expliquant de manière plus complète comment créer une solution d’analytique prédictive, consultez [Développer une solution prédictive avec Machine Learning Studio (classique)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Groupes de données
Un jeu de données représente des données téléchargées dans Machine Learning Studio (classique) de façon à les utiliser dans la procédure de modélisation. Machine Learning Studio (classique) fournit divers exemples de jeux de données utilisables pour vos expériences ; vous pouvez télécharger vers le serveur d'autres jeux de données si vous en avez besoin. Voici quelques exemples de jeux de données fournis :

* **Données sur la quantité de litres au 100 pour différents véhicules automobiles** : valeurs de quantité de litres au 100 pour des automobiles identifiées par leur nombre de cylindres, leur puissance, etc.
* **Données sur le cancer du sein** : données de diagnostics sur le cancer du sein.
* **Données sur les feux de forêts** : tailles des incendies de forêts au nord-est du Portugal.

Au moment de créer une expérience, vous pouvez effectuer un choix dans la liste des jeux de données disponibles à gauche du canevas.

Pour obtenir une liste des exemples de jeux de données inclus dans Machine Learning Studio (classique), consultez [Utiliser les exemples de jeux de données dans Machine Learning Studio (classique)](use-sample-datasets.md).

### <a name="modules"></a>Modules
Un module est un algorithme que vous appliquez à vos données. Machine Learning Studio (classique) comporte divers modules, allant de fonctions d’entrée des données à des procédures d’entraînement, de scoring et de validation. Voici quelques exemples de modules fournis :

* [Convertir en ARFF][convert-to-arff] : convertit un jeu de données sérialisé .NET au format ARFF (Attribute-Relation File Format).
* [Statistiques élémentaires de calcul][elementary-statistics] : calcule des statistiques élémentaires (par exemple, moyenne, écart type, etc.).
* [Régression linéaire][linear-regression] : crée un modèle en ligne de régression linéaire basée sur une descente de gradient.
* [Noter le modèle][score-model] : note un modèle de classification ou de régression formé.

Lorsque vous créez une expérience, vous pouvez utiliser la liste des modules à gauche du canevas.

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Quand vous sélectionnez un module dans le canevas, ses paramètres sont affichés dans le volet **Propriétés** à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.

Pour savoir comment parcourir la vaste bibliothèque d’algorithmes de machine learning disponibles, consultez [Guide pratique pour choisir des algorithmes pour Microsoft Machine Learning Studio (classique)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Déploiement d'un service web d'analyse prédictive
Une fois votre modèle d'analyse prédictive prêt, vous pouvez le déployer comme un service web directement à partir de Machine Learning Studio (classique). Pour plus d’informations sur ce processus, consultez [Déploiement d’un service web Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Étapes suivantes
Apprenez les principes de base de l’analyse prédictive et de l’apprentissage automatique en suivant un [guide de démarrage rapide](create-experiment.md) pas à pas et en [développant à partir d’exemples](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
