---
title: Entités prédéfinies pour Language Understanding (LUIS).
titleSuffix: Azure Cognitive Services
description: LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. La prise en charge de l’entité prédéfinie varie selon la culture de votre application LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647593"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entités prédéfinies pour reconnaître les types de données courants

LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. 

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie

1. Ouvrez votre application en cliquant sur son nom dans la page **Mes applications**, puis cliquez sur **Entités** du côté gauche. 

1. Dans la page **Entités**, cliquez sur **Manage prebuilt entities** (Gérer les entités prédéfinies).

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


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Documentation de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)
