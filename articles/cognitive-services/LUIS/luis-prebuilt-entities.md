---
title: Entités prédéfinies
titleSuffix: Azure Cognitive Services
description: LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. La prise en charge de l’entité prédéfinie varie selon la culture de votre application LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: ade09c7527e62112ac7225df21e25d4139d99e58
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878991"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entités prédéfinies pour reconnaître les types de données courants

LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. 

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie

1. Ouvrez votre application en cliquant sur son nom dans la page **Mes applications**, puis cliquez sur **Entités** du côté gauche. 

1. Dans la page **Entities**, cliquez sur **Add prebuilt entity** (Ajouter une entité prédéfinie).

1. Dans la boîte de dialogue **Add prebuilt entities** (Ajouter des entités prédéfinies), sélectionnez l’entité prédéfinie datetimeV2. 

    ![Boîte de dialogue Add prebuilt entities (Ajouter des entités prédéfinies)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Sélectionnez **Terminé**.

## <a name="publish-the-app"></a>Publier l’application

La manière la plus simple de visualiser la valeur d’une entité prédéfinie consiste à interroger cette valeur à partir du point de terminaison publié. 

1. Dans la barre d’outils supérieure, sélectionnez **Publier**. Choisissez l’emplacement de publication **Production**. 

1. Lorsque la notification de réussite de couleur verte s’affiche, sélectionnez le lien **Refer to the list of endpoints** (Consulter la liste des points de terminaison) pour visualiser les points de terminaison.

1. Sélectionnez un point de terminaison. Un nouvel onglet de navigateur s’ouvre au niveau de ce point de terminaison. Laissez l’onglet ouvert et poursuivez à la section **Test**.

## <a name="test"></a>Test
Une fois l’entité ajoutée, vous n’avez pas besoin d’effectuer l’apprentissage de l’application. 

Testez la nouvelle intention au niveau du point de terminaison en ajoutant une valeur pour le paramètre **q**. Utilisez les suggestions d’énoncés pour **q** fournies dans le tableau ci-après :

|Énoncé de test|Valeur d’entité|
|--|:--|
|Réserver un vol pour demain|2018-10-19|
|Annuler le rendez-vous du 3 mars|LUIS a renvoyé la date du 3 mars précédent (2018-03-03) et celle du 3 mars prochain (2019-03-03), car l’énoncé n’a pas spécifié l’année.|
|Planifier une réunion à 10 h|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Marquage des entités contenant un jeton d’entité prédéfinie
 Si vous avez du texte, tel que `HH-1234`, que vous souhaitez marquer comme une entité personnalisée _et_ que vous avez l’[entité prédéfinie number](luis-reference-prebuilt-number.md) ajoutée au modèle, vous ne pouvez pas marquer l’entité personnalisée dans le portail LUIS. Vous pouvez la marquer avec l’API. 

 Pour marquer ce type de jeton, où une partie est déjà marquée avec une entité prédéfinie, supprimez l’entité prédéfinie de l’application LUIS. Vous n’avez pas besoin d’entraîner l’application. Marquez le jeton avec votre propre entité personnalisée. Ensuite, rajoutez l’entité prédéfinie à l’application LUIS.

 En guise d’autre exemple, considérez l’énoncé comme une liste de préférences de classes : `I want first year spanish, second year calculus, and fourth year english lit.` Si l’application LUIS a l’entité prédéfinie ordinal ajoutée, `first`, `second` et `fourth` seront déjà marquées avec des ordinaux. Si vous souhaitez capturer l’ordinal et la classe, vous pouvez créer une entité composite et l’envelopper autour de l’entité prédéfinie ordinal et de l’entité personnalisée pour le nom de la classe.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Documentation de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)
