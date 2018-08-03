---
title: Ajouter des fonctionnalités aux applications LUIS | Microsoft Docs
description: Utiliser Language Understanding (LUIS) pour ajouter des fonctionnalités d’application pouvant améliorer la détection ou la prédiction d’intentions et d’entités dans des catégories et des modèles
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222951"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>Utiliser les fonctionnalités pour améliorer les performances de votre application LUIS  

Vous pouvez ajouter des fonctionnalités à votre application LUIS pour améliorer sa précision. Ces fonctionnalités aident LUIS en indiquant que certains mots et expressions font partie d’une catégorie. Si LUIS vous apprend à reconnaître un membre de la catégorie, il peut traiter les autres de la même façon.

## <a name="add-phrase-list"></a>Ajouter une liste d’expressions

1. Ouvrez votre application en cliquant sur son nom à la page **Mes applications**, puis cliquez sur **Générer**, sur **Listes d’expressions** dans le volet gauche de votre application. 

    ![Navigation dans la liste d’expressions](./media/luis-add-features/phrase-list-nav.png)

2. À la page **Listes d’expressions**, cliquez sur **Créer une liste d’expressions**. 
 
    ![Créer une liste d’expressions](./media/luis-add-features/create-new-phrase-list.png)
    
3. Dans la boîte de dialogue **Ajouter une liste d’expressions**, tapez « Villes » comme nom de la liste d’expressions. Dans la case **Valeur**, tapez les valeurs de la liste d’expressions. Vous pouvez taper une valeur à la fois, ou un ensemble de valeurs séparées par des virgules, puis appuyer sur **Entrée**.

    ![Ajouter une liste d’expressions Villes](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS peut suggérer des valeurs associées à ajouter à votre liste d’expressions. Cliquez sur **Recommander** pour obtenir un groupe de valeurs proposées liées sémantiquement à la ou aux valeurs ajoutées. Vous pouvez cliquer sur une des valeurs proposées, ou sur **Ajouter tout** pour les ajouter toutes.

    ![Liste d’expressions valeurs proposées](./media/luis-add-features/related-values.png)

5. Cliquez sur **Ces valeurs sont interchangeables** si les valeurs ajoutées à la liste d’expressions sont des alternatives pouvant être utilisées indifféremment.

    ![Liste d’expressions valeurs proposées](./media/luis-add-features/interchangeable.png)

6. Cliquez sur **Enregistrer**. La liste d’expressions « Villes » est ajoutée à la page **Listes d’expressions**.

    ![Liste d’expressions ajoutée](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Modifier une liste d’expressions

Cliquez sur le nom de la liste d’expressions à la page **Liste d’expressions**. Dans la boîte de dialogue **Modifier une liste d’expressions** qui s’ouvre, effectuez les modifications requises, puis cliquez sur **Enregistrer**.

 ![Liste d’expressions ajoutée](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Supprimer une liste d’expressions 

Cliquez sur le bouton à points de suspension (***...*** ) à la fin de la ligne et sélectionnez **Supprimer**.

 ![Supprimer une liste ajoutée](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Désactiver une liste d’expressions 

Cliquez sur le bouton à points de suspension (***...*** ) à la fin de la ligne et sélectionnez **Désactiver**.

 ![Désactiver une liste ajoutée](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Fonctionnalité de modèle (expression régulière) 
**Cette fonctionnalité est déconseillée**. Il n’est pas possible d’ajouter de nouvelles fonctionnalités de modèle à LUIS. Les fonctionnalités de modèle existantes sont prises en charge jusqu'à mai 2018. Contribuer à une expression régulière LUIS standard correspondant à PR dans le [référentiel Github de modules de reconnaissance de texte](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Étapes suivantes

Après ajout, modification, suppression ou désactivation d’une liste d’expressions, [former et tester à nouveau l’application](luis-interactive-test.md) à nouveau pour voir si les performances s’améliorent.
