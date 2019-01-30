---
title: Listes d’expressions
titleSuffix: Language Understanding - Azure Cognitive Services
description: Utiliser Language Understanding (LUIS) pour ajouter des fonctionnalités d’application pouvant améliorer la détection ou la prédiction d’intentions et d’entités dans des catégories et des modèles
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: 4f2343e1c6b95c495f03cd94ee58164c546f4b76
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382186"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Utiliser des listes d’expressions pour améliorer le signal de liste de mots

Vous pouvez ajouter des fonctionnalités à votre application LUIS pour améliorer sa précision. Ces fonctionnalités aident LUIS en indiquant que certains mots et expressions font partie d’un vocabulaire de domaine d’application. 

Une [liste d’expressions](luis-concept-feature.md) est un groupe de valeurs (mots ou expressions) qui appartiennent à la même classe et doivent être traitées de la même façon (par exemple, des noms de villes ou de produits). Ce que LUIS apprend sur l’un d’entre elles s’applique automatiquement également aux autres. Cette liste n’est pas une entité de liste fermée (correspondances de texte exactes) de mots correspondants.

Une liste d’expressions complète le vocabulaire du domaine d’application sous la forme d’un deuxième signal vers LUIS sur ces mots.

## <a name="add-phrase-list"></a>Ajouter une liste d’expressions

LUIS permet jusqu’à 10 listes de phrases par application. 

1. Ouvrez votre application en cliquant sur son nom à la page **Mes applications**, puis cliquez sur **Générer**, sur **Listes d’expressions** dans le volet gauche de votre application. 

2. À la page **Listes d’expressions**, cliquez sur **Créer une liste d’expressions**. 
 
3. Dans la boîte de dialogue **Ajouter une liste d’expressions**, tapez « Villes » comme nom de la liste d’expressions. Dans la case **Valeur**, tapez les valeurs de la liste d’expressions. Vous pouvez taper une valeur à la fois, ou un ensemble de valeurs séparées par des virgules, puis appuyer sur **Entrée**.

    ![Ajouter une liste d’expressions Villes](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS peut suggérer des valeurs associées à ajouter à votre liste d’expressions. Cliquez sur **Recommander** pour obtenir un groupe de valeurs proposées liées sémantiquement à la ou aux valeurs ajoutées. Vous pouvez cliquer sur une des valeurs proposées, ou sur **Ajouter tout** pour les ajouter toutes.

    ![Liste d’expressions, valeurs proposées - ajouter tout](./media/luis-add-features/related-values.png)

5. Cliquez sur **Ces valeurs sont interchangeables** si les valeurs ajoutées à la liste d’expressions sont des alternatives pouvant être utilisées indifféremment.

    ![Liste d’expressions, valeurs proposées - sélectionner la case interchangeable ](./media/luis-add-features/interchangeable.png)

6. Cliquez sur **Enregistrer**. La liste d’expressions « Villes » est ajoutée à la page **Listes d’expressions**.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Vous pouvez supprimer ou désactiver une liste de phrases dans la barre d’outils contextuelle de la page **Listes d’expressions**.

## <a name="next-steps"></a>Étapes suivantes

Après ajout, modification, suppression ou désactivation d’une liste d’expressions, [former et tester à nouveau l’application](luis-interactive-test.md) à nouveau pour voir si les performances s’améliorent.
