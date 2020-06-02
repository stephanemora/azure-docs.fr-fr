---
title: Fichier include
description: Fichier include
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 7ab6aa5f830e335a30502207d3a49a528d03f7fd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654278"
---
1. Sélectionnez [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) pour afficher la page GitHub du fichier `pizza-app-for-luis.json`.
1. Cliquez avec le bouton droit ou appuyez longuement sur le bouton **Raw** et sélectionnez **Enregistrer la cible sous** pour enregistrer le fichier `pizza-app-for-luis.json` sur votre ordinateur.
1. Connectez-vous au [portail LUIS](https://www.luis.ai).
1. Sélectionnez [My Apps](https://www.luis.ai/applications) (Mes applications).
1. Dans la page **My Apps**, sélectionnez **+ New app for conversation** (Nouvelle application pour la conversation).
1. Sélectionnez **Import as JSON** (Importer en tant que JSON).
1. Dans la boîte de dialogue **Import new app** (Importer une nouvelle application), sélectionnez le bouton **Choose File** (Choisir un fichier).
1. Sélectionnez le fichier `pizza-app-for-luis.json` que vous avez téléchargé, puis sélectionnez **Open** (Ouvrir).
1. Dans le champ **Name** (Nom) de la boîte de dialogue **Import new app**, entrez un nom pour votre application de pizza, puis sélectionnez le bouton **Done** (terminé).

L’application est importée.

Si apparaît une boîte de dialogue **How to create an effective LUIS app** (Comment créer une application LUIS efficace), fermez-la.

## <a name="train-and-publish-the-pizza-app"></a>Entraîner et publier l’application de pizza

Vous devez voir la page **Intents** (Intentions) avec une liste des intentions de l’application de pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Votre application de pizza est maintenant prête à être utilisée.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Enregistrer l’ID d’application, la clé de prédiction et le point de terminaison de prédiction de votre application de pizza

Pour utiliser votre nouvelle application de pizza, vous aurez besoin de ses ID d’application, clé de prédiction et point de terminaison.

Pour rechercher ces valeurs :

1. Dans la page **Intents** (Intentions), sélectionnez **MANAGE** (GÉRER).
1. Dans la page **Application Settings** (Paramètres de l’application), enregistrez l’**ID d’application**.
1. Sélectionnez **Azure Resources** (Ressources Azure).
1. Dans la page **Azure Resources**, enregistrez la **clé primaire**. Cette valeur est votre clé de prédiction.
1. Enregistrez l’**URL du point de terminaison**. Cette valeur est votre point de terminaison de prédiction.
