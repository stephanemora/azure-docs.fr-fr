---
title: Évaluation des scénarios - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer peut être appliqué dans tous les cas où votre application peut sélectionner l’élément, l’action ou le produit corrects à montrer, de façon à améliorer l’expérience, obtenir de meilleurs résultats métier ou améliorer la productivité.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b24e3d7fd71ef60cf8ebe5ba2c33889ff518580c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025468"
---
# <a name="where-can-you-use-personalizer"></a>Où pouvez-vous utiliser Personalizer ?

Utilisez Personalizer dans tous les cas où votre application doit sélectionner l’élément, l’action ou le produit corrects à montrer, de façon à améliorer l’expérience, obtenir de meilleurs résultats métier ou améliorer la productivité. 

Personalizer utilise le machine learning pour sélectionner l’action à montrer à l’utilisateur. La sélection peut varier considérablement selon la quantité, la qualité et la distribution des données envoyées au service.

### <a name="checklist-for-applying-personalizer"></a>Liste de contrôle pour l’application de Personalizer


Vous pouvez appliquer Personalizer dans les situations où :

* Vous avez un objectif métier ou d’utilisabilité pour votre application.
* Il y a un endroit dans votre application où une décision contextuelle de ce qu’il faut montrer aux utilisateurs va améliorer cet objectif.
* Le meilleur choix peut et doit être appris du comportement collectif des utilisateurs et du score de récompense total.
* L’utilisation du machine learning pour la personnalisation suit des [guides pour une utilisation responsable](ethics-responsible-use.md) et les choix que vous faites.
* La décision contextuelle peut être exprimée sous la forme d’un classement de la meilleure option (action) à partir d’un ensemble limité de choix.
* Le degré de pertinence du choix classé pour votre application peut être déterminé en mesurant certains aspects du comportement de l’utilisateur et en l’exprimant dans un _score de récompense_. Il s’agit d’un nombre compris entre -1 et 1.
* Le score de récompense n’introduit pas trop de facteurs perturbants ou externes. La durée de l’expérience est suffisamment courte pour que le score de récompense puisse être calculé pendant la période de temps où il reste pertinent.
* Vous pouvez exprimer le contexte pour le classement sous la forme d’une liste d’au moins 5 [caractéristiques](concepts-features.md) dont vous pensez qu’elles peuvent aider à faire le bon choix ; ceci n’inclut pas d’informations d’identification personnelles. (PII).
* Vous disposez d’informations sur chaque choix de contenu, _action_, sous la forme d’une liste d’au moins 5 [caractéristiques](concepts-features.md) dont vous pensez qu’elles peuvent aider Personalizer à faire le bon choix.
* Votre application peut conserver des données sur une période suffisamment longue pour accumuler un historique d’au moins 100 000 interactions.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Considérations sur le machine learning pour l’application de Personalizer

Personalizer est basée sur l’apprentissage par renforcement, une approche du machine learning qui est basé sur le feedback dont vous l’alimentez. 

Personalizer fait un meilleur apprentissage dans les situations où :

* Il existe suffisamment d’événements pour continuer à obtenir une personnalisation optimale si le problème se déplace au fil du temps (comme des préférences quant à des actualités ou à la mode). Personalizer s’adaptera aux changements continus du monde réel, mais les résultats ne seront pas optimaux s’il n’y pas suffisamment de données et d’événements servant de base à l’apprentissage pour découvrir et décider de nouveaux modèles. Vous devez choisir un cas d’utilisation qui se produit suffisamment souvent. Recherchez des cas d’utilisation qui se produisent au moins 500 fois par jour.
* Le contexte et les actions ont suffisamment de [caractéristiques](concepts-features.md) pour faciliter l’apprentissage.
* Il y a moins de 50 actions à classer par appel.
* Vos paramètres de conservation des données permettent à Personalizer de collecter suffisamment de données pour effectuer des évaluations hors connexion et l’optimisation de la stratégie. Ceci représente généralement au moins 50 000 points de données.

## <a name="monitor-effectiveness-of-personalizer"></a>Surveiller l’efficacité de Personalizer

Vous pouvez surveiller régulièrement l’efficacité de Personalizer en effectuant des [évaluations hors connexion](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Utiliser Personalizer avec des moteurs de recommandation

De nombreuses entreprises utilisent des moteurs de recommandation, des outils de marketing et de gestion des campagnes, la segmentation et le clustering de l’audience, le filtrage collaboratif et d’autres moyens pour recommander aux clients les produits d’un catalogue étendu.

Le [dépôt GitHub des générateurs de recommandations de Microsoft ](https://github.com/Microsoft/Recommenders) fournit des exemples et des bonnes pratiques pour créer des systèmes de recommandation, fournis sous la forme de notebooks Jupyter. Il fournit des exemples fonctionnels pour la préparation des données, la création de modèles, et l’évaluation, l’optimisation et l’exploitation des moteurs de recommandation, pour de nombreuses approches courantes, notamment xDeepFM, SAR, ALS, RBM et DKN.

Personalizer peut fonctionner avec un moteur de recommandation quand il est présent.

* Les moteurs de recommandation prennent de grandes quantités d’éléments (par exemple 500 000) et recommandent un sous-ensemble (par exemple les 20 premiers) à partir de centaines ou de milliers d’options.
* Personalizer prend un petit nombre d’actions avec un grand nombre d’informations sur celles-ci et les classe en temps réel pour un contexte donné avec de nombreuses caractéristiques, tandis que la plupart des moteurs de recommandation utilisent seulement quelques attributs sur les utilisateurs, les produits et leurs interactions.
* Personalizer est conçu pour explorer de façon autonome et en permanence les préférences des utilisateurs, ce qui produit de meilleurs résultats là où le contenu change rapidement, comme les actualités, les événements en direct, le contenu dynamique d’une communauté, le contenu avec des mises à jour quotidiennes ou le contenu sujet à des variations saisonnières.

Une utilisation courante consiste à prendre le résultat d’un moteur de recommandation (par exemple les 20 premiers produits pour un client spécifique) et à l’utiliser comme actions en entrée pour Personalizer.

## <a name="next-steps"></a>Étapes suivantes

[Déontologie et utilisation responsable](ethics-responsible-use.md).