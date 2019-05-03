---
title: 'Former le modèle : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le **former le modèle** module dans le service Azure Machine Learning pour former un modèle de classification ou de régression.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028109"
---
# <a name="train-model-module"></a>Module de formation de modèle

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour former un modèle de classification ou de régression. Formation a lieu une fois que vous avez défini un modèle et définissez ses paramètres et nécessite des données avec balises. Vous pouvez également utiliser **former le modèle** pour reformer un modèle existant avec de nouvelles données. 

## <a name="how-the-training-process-works"></a>Comment fonctionne le processus de formation

Dans Azure Machine Learning, vous est généralement un processus en trois étapes création et l’utilisation d’un modèle d’apprentissage. 

1. Vous configurez un modèle, en choisissant un type particulier d’algorithme et définir ses paramètres ou les hyperparamètres. Choisissez un des types de modèles suivants : 

    + **Classification** modèles, basée sur les réseaux neuronaux, des arbres de décision et des forêts de décision et d’autres algorithmes.
    + **Régression** modèles, qui peut inclure une régression linéaire standard ou qui utilisent d’autres algorithmes, notamment les réseaux neuronaux et régression bayésienne.  

2. Fournir un jeu de données qui porte le nom et qui dispose de données compatibles avec l’algorithme. Se connecter à la fois les données et le modèle à **former le modèle**.

    Produit de formation est un format binaire spécifique, l’iLearner, qui encapsule les modèles statistiques appris à partir des données. Vous ne pouvez pas directement modifier ou de lire ce format ; Toutefois, les autres modules peuvent utiliser ce modèle formé. 
    
    Vous pouvez également afficher les propriétés du modèle. Pour plus d’informations, consultez la section relative aux résultats.

3. Une fois la formation terminée, utilisez le modèle formé avec l’un de la [notation modules](./score-model.md), pour effectuer des prévisions sur de nouvelles données.

## <a name="how-to-use-train-model"></a>Comment utiliser **former le modèle**  
  
1.  Dans Azure Machine Learning, configurez un modèle de classification ou de régression.
    
2. Ajouter le **former le modèle** module à l’expérience.  Vous trouverez ce module sous la **Machine Learning** catégorie. Développez **Train**, puis faites glisser le **former le modèle** module dans votre expérience.
  
3.  Sous l’entrée de gauche, joignez le mode non formé. Attacher le dataset d’apprentissage à l’entrée droite de **former le modèle**.

    Le dataset d’apprentissage doit contenir une colonne d’étiquette. Toutes les lignes sans les étiquettes sont ignorés.
  
4.  Pour **colonne d’étiquette**, cliquez sur **lancer le sélecteur de colonne**, puis choisissez une seule colonne qui contient le modèle peut utiliser pour l’apprentissage de résultats.
  
    - Pour les problèmes de classification, la colonne d’étiquette doit contenir **catégorielles** valeurs ou **discrètes** valeurs. Certains exemples peuvent être une évaluation Oui/Non, un code de classification de maladie ou nom ou un groupe de revenus.  Si vous sélectionnez une colonne non catégorielle, le module renvoie une erreur pendant la formation.
  
    -   Pour les problèmes de régression, la colonne d’étiquette doit contenir **numérique** données qui représente la variable de réponse. Dans l’idéal, les données numériques représentent une échelle continue. 
    
    Exemples peuvent être un score de risque de crédit, la durée projetée échec pour un disque dur ou le nombre prévu d’appels à un centre d’appels sur une journée donnée ou une heure.  Si vous ne choisissez pas une colonne numérique, vous pouvez obtenir une erreur.
  
    -   Si vous ne spécifiez pas de colonne d’étiquette à utiliser, Azure Machine Learning tente de déduire la colonne étiquette appropriée, en utilisant les métadonnées du jeu de données. Si elle choisit la mauvaise colonne, utilisez le sélecteur de colonne pour résoudre ce problème.
  
    > [!TIP] 
    > Si vous avez des difficultés à utiliser le sélecteur de colonne, consultez l’article [Select Columns in Dataset](./select-columns-in-dataset.md) pour obtenir des conseils. Il décrit quelques scénarios courants et les conseils d’utilisation de la **avec règles** et **par nom** options.
  
5.  Exécutez l’expérience. Si vous avez une grande quantité de données, cela peut prendre un certain temps.

## <a name="bkmk_results"></a> résultats

Une fois le modèle formé :

+ Pour afficher les paramètres du modèle et le poids de fonctionnalité, cliquez sur la sortie et sélectionnez **visualiser**.
+ Pour utiliser le modèle dans les autres expériences, cliquez sur le modèle et sélectionnez **enregistrer le modèle**. Tapez un nom pour le modèle. 

    Le modèle est enregistré en tant qu’instantané qui n’est pas mis à jour par les exécutions répétées de l’expérience.
+ Pour utiliser le modèle dans la prédiction de nouvelles valeurs, connectez-le à la [noter le modèle](./score-model.md) module, ainsi que de nouvelles données d’entrée.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 