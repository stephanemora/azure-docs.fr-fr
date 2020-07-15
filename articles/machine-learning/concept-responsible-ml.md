---
title: Apprentissage automatique responsable
titleSuffix: Azure Machine Learning
description: Découvrez ce qu’est l’apprentissage automatique responsable et comment l’utiliser dans Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 09bb7710df19482e9625c1834d2646f70368fc1d
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920356"
---
# <a name="responsible-machine-learning-ml"></a>Apprentissage automatique responsable

Cet article explique ce qu’est l’apprentissage automatique responsable et comment le mettre en pratique avec Azure Machine Learning.

La confiance doit toujours rester au cœur du développement et de l’utilisation des systèmes d’intelligence artificielle. Confiance dans la plateforme, le processus et les modèles. Chez Microsoft, l’apprentissage automatique responsable englobe les valeurs et principes suivants :

- Comprendre les modèles d’apprentissage automatique
  - Interpréter et expliquer le comportement d’un modèle
  - Évaluer et atténuer la partialité d’un modèle
- Protéger les personnes et leurs données
  - Empêcher l’exposition des données avec la confidentialité différentielle  
- Contrôler le processus d’apprentissage automatique de bout en bout
  - Documenter le cycle de vie de l’apprentissage automatique avec des feuilles de données

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Piliers de l’apprentissage automatique responsable":::

À l’heure où l’intelligence artificielle et les systèmes autonomes s’intègrent plus profondément dans la structure de la société, il est important de faire l’effort d’anticiper et d’atténuer les conséquences inattendues de ces technologies.

## <a name="interpret-and-explain-model-behavior"></a>Interpréter et expliquer le comportement d’un modèle

Les systèmes de boîte opaque peuvent être problématiques, car il est difficile pour les parties prenantes, telles que les développeurs système, les régulateurs, les utilisateurs et les décideurs de comprendre pourquoi les systèmes prennent certaines décisions. Certains systèmes d’intelligence artificielle sont plus faciles à expliquer que d’autres, et ils résultent parfois d’un compromis entre l’exactitude et la transparence.

Pour créer des systèmes d’intelligence artificielle interprétables, utilisez [InterpretML](https://github.com/interpretml/interpret), un package open source créé par Microsoft. Vous pouvez [utiliser InterpretML à l’intérieur d’Azure Machine Learning](how-to-machine-learning-interpretability.md) pour [interpréter et expliquer vos modèles d’apprentissage automatique](how-to-machine-learning-interpretability-aml.md), dont des [modèles d’apprentissage automatique automatisé](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Évaluer et atténuer la partialité d’un modèle

Les systèmes d’intelligence artificielle étant de plus en plus mis à contribution pour les prises de décision quotidiennes, il est extrêmement important qu’ils fonctionnent bien pour fournir des résultats impartiaux ne lésant personne.

Toute partialité des systèmes d’intelligence artificielle peut entraîner les conséquences inattendues suivantes :

- Rétention d’opportunités, de ressources ou d’informations au détriment de personnes.
- Renforcement de biais et stéréotypes.

De nombreux aspects de l’impartialité ne peuvent pas être capturés ou représentés par des métriques. Il existe des outils et pratiques qui peuvent renforcer l’impartialité en lien avec la conception et le développement de systèmes d’intelligence artificielle.

L’évaluation et l’atténuation sont deux étapes clés de l’atténuation de la partialité des systèmes d’intelligence artificielle. Nous vous recommandons d’utiliser [FairLearn](https://github.com/fairlearn/fairlearn), un package open source capable d’évaluer et d’atténuer la partialité potentielle des systèmes d’intelligence artificielle. Pour en savoir plus sur l’impartialité et le package FairLearn, consultez l’[article sur l’impartialité de l’apprentissage automatique](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Empêcher l’exposition des données avec la confidentialité différentielle

Lorsque des données sont utilisées à des fins d’analyse, il est important qu’elles restent privées et confidentielles tout au long de leur utilisation. La confidentialité différentielle est un ensemble de systèmes et pratiques qui permettent de préserver la sécurité et la confidentialité des données des personnes.

Dans les scénarios traditionnels, les données brutes sont stockées dans des fichiers et des bases de données. Lorsque des utilisateurs analysent des données, ils utilisent généralement des données brutes. C’est problématique, car cela peut entraîner une violation de données à caractère personnel. La confidentialité différentielle tente de résoudre ce problème en ajoutant du « bruit » ou une composante aléatoire aux données afin que les utilisateurs ne puissent pas identifier des points de données individuels.

L’implémentation de systèmes de confidentialité différentielle est difficile. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) est un projet open source contenant différents composants pour la création de systèmes globaux assortis d’une confidentialité différentielle. Pour en savoir plus sur la confidentialité différentielle et le projet WhiteNoise, consultez l’article [Préserver la confidentialité des données à l’aide de la confidentialité différentielle et de WhiteNoise](./concept-differential-privacy.md).

> [!NOTE]
> Veuillez noter que nous allons renommer le kit de ressources et présenterons le nouveau nom dans les semaines à venir. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Documenter le cycle de vie de l’apprentissage automatique avec des feuilles de données

La documentation des informations adéquates dans le processus d’apprentissage automatique est la clé de la prise de décisions responsables à chaque étape. Les feuilles de données sont un moyen de documenter les ressources d’apprentissage automatique utilisées et créées dans le cadre du cycle de vie de l’apprentissage automatique.

Il existe une tendance consistant à considérer les modèles comme des « boîtes opaques », qui a pour effet qu’il y a souvent peu d’informations à leur sujet. Les systèmes d’apprentissage automatique intervenant de plus en plus dans les prises de décisions, l’utilisation de feuilles de données est une étape vers le développement de systèmes d’apprentissage automatique plus responsables.

Voici des informations sur le modèle qu’il peut être utile de documenter dans une feuille de données :

- Usage prévu
- Architecture du modèle
- Données d’apprentissage utilisées
- Données d’évaluation utilisées
- Métriques de performances du modèle d’apprentissage
- Informations sur l’impartialité

Pour apprendre à utiliser le Kit de développement logiciel (SDK) Azure Machine Learning pour implémenter des [feuilles de données pour les modèles](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb), consultez l’exemple suivant.

## <a name="additional-resources"></a>Ressources supplémentaires

- Utilisez le chiffrement homomorphe pour [déployer un service web d’inférence chiffré](how-to-homomorphic-encryption-seal.md).
- En savoir plus sur l’ensemble de recommandations [À PROPOS DE L’APPRENTISSAGE AUTOMATIQUE](https://www.partnershiponai.org/about-ml/) pour la documentation de système d’apprentissage automatique.
