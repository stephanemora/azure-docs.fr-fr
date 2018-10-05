---
title: Listes d’expressions pour améliorer la détection d’entité
titleSuffix: Azure Cognitive Services
description: Utiliser Language Understanding (LUIS) pour ajouter des fonctionnalités d’application pouvant améliorer la détection ou la prédiction d’intentions et d’entités dans des catégories et des modèles
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6831f67a33d114ca5c42ddacf8ef4de704e21711
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036954"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Utiliser des listes d’expressions pour améliorer le signal de liste de mots

Vous pouvez ajouter des fonctionnalités à votre application LUIS pour améliorer sa précision. Ces fonctionnalités aident LUIS en indiquant que certains mots et expressions font partie d’un vocabulaire de domaine d’application. 

## <a name="add-phrase-list"></a>Ajouter une liste d’expressions

1. Ouvrez votre application en cliquant sur son nom à la page **Mes applications**, puis cliquez sur **Générer**, sur **Listes d’expressions** dans le volet gauche de votre application. 

2. À la page **Listes d’expressions**, cliquez sur **Créer une liste d’expressions**. 
 
3. Dans la boîte de dialogue **Ajouter une liste d’expressions**, tapez « Villes » comme nom de la liste d’expressions. Dans la case **Valeur**, tapez les valeurs de la liste d’expressions. Vous pouvez taper une valeur à la fois, ou un ensemble de valeurs séparées par des virgules, puis appuyer sur **Entrée**.

    ![Ajouter une liste d’expressions Villes](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS peut suggérer des valeurs associées à ajouter à votre liste d’expressions. Cliquez sur **Recommander** pour obtenir un groupe de valeurs proposées liées sémantiquement à la ou aux valeurs ajoutées. Vous pouvez cliquer sur une des valeurs proposées, ou sur **Ajouter tout** pour les ajouter toutes.

    ![Liste d’expressions valeurs proposées](./media/luis-add-features/related-values.png)

5. Cliquez sur **Ces valeurs sont interchangeables** si les valeurs ajoutées à la liste d’expressions sont des alternatives pouvant être utilisées indifféremment.

    ![Liste d’expressions valeurs proposées](./media/luis-add-features/interchangeable.png)

6. Cliquez sur **Enregistrer**. La liste d’expressions « Villes » est ajoutée à la page **Listes d’expressions**.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Vous pouvez modifier, supprimer ou désactiver une liste d’expressions à l’aide du bouton ***...*** qui se trouve à la fin de la ligne de chaque liste d’expressions.

## <a name="pattern-regular-expression-feature"></a>Fonctionnalité de modèle (expression régulière) 
**Cette fonctionnalité est déconseillée**. Il n’est pas possible d’ajouter de nouvelles fonctionnalités de modèle à LUIS. Les fonctionnalités de modèle existantes sont prises en charge jusqu'à mai 2018. Contribuer à une expression régulière LUIS standard correspondant à PR dans le [référentiel Github de modules de reconnaissance de texte](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Étapes suivantes

Après ajout, modification, suppression ou désactivation d’une liste d’expressions, [former et tester à nouveau l’application](luis-interactive-test.md) à nouveau pour voir si les performances s’améliorent.
