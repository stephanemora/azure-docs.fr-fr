---
title: Descripteurs - LUIS
titleSuffix: Azure Cognitive Services
description: Utiliser Language Understanding (LUIS) pour ajouter des fonctionnalités d’application pouvant améliorer la détection ou la prédiction d’intentions et d’entités dans des catégories et des modèles
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123127"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Utiliser des descripteurs pour améliorer le signal de liste de mots

Vous pouvez ajouter des fonctionnalités à votre application LUIS pour améliorer sa précision. Ces fonctionnalités aident LUIS en indiquant que certains mots et expressions font partie d’un vocabulaire de domaine d’application. 

Un [descripteur](luis-concept-feature.md) (une liste d’expressions) est un groupe de valeurs (mots ou expressions) qui appartiennent à la même classe et doivent être traitées de la même façon (par exemple, des noms de villes ou de produits). Ce que LUIS apprend sur l’un d’entre elles s’applique automatiquement également aux autres. Cette liste n’est pas une [entité de liste](reference-entity-list.md) (correspondances de texte exactes) de mots correspondants.

Un descripteur complète le vocabulaire du domaine d’application sous la forme d’un deuxième signal vers LUIS sur ces mots.

Passez en revue les [concepts des caractéristiques](luis-concept-feature.md) pour comprendre quand et pourquoi utiliser un descripteur. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Ajouter un descripteur

1. Ouvrez votre application en cliquant sur son nom dans la page **Mes applications**, puis cliquez sur **Build**, puis sur **Descripteurs** dans le panneau gauche de votre application. 

1. Dans la page **Descripteurs**, cliquez sur **+ Ajouter un descripteur**. 
 
1. Dans la boîte de dialogue **Créer un descripteur de liste d’expressions**, entrez un nom comme `Cities` pour le descripteur. Dans la zone **Valeur**, tapez les valeurs des descripteurs, comme `Seattle`. Vous pouvez taper une valeur à la fois, ou un ensemble de valeurs séparées par des virgules, puis appuyer sur **Entrée**.

    > [!div class="mx-imgBorder"]
    > ![Ajouter le descripteur Cities](./media/luis-add-features/add-phrase-list-cities.png)

    Une fois que vous avez entré suffisamment de valeurs pour LUIS, des suggestions apparaissent. Vous pouvez cliquer sur **+ Ajouter** pour toutes les valeurs proposées ou sélectionner des termes individuels.

1. Laissez la case **Ces valeurs sont interchangeables** cochée si les valeurs ajoutées sont des alternatives pouvant être utilisées indifféremment.

1. Sélectionnez **Terminé**. Le nouveau descripteur est ajouté à la page **Descripteurs**.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Vous pouvez supprimer ou désactiver un descripteur à partir de la barre d’outils contextuelle de la page **Descripteurs**.

## <a name="next-steps"></a>Étapes suivantes

Après l’ajout, la modification, la suppression ou la désactivation d’un descripteur, [réentraînez et retestez l’application](luis-interactive-test.md) pour voir si les performances s’améliorent.
