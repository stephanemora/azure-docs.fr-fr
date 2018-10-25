---
title: Créer une base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: L’ajout d’échanges de conversation à votre bot rend ses conversations plus naturelles et engageantes. QnA Maker vous permet d’ajouter facilement un ensemble prédéfini d’échanges de conversation dans votre base de connaissances. Cela peut constituer un point de départ pour la conversation de votre bot, et peut vous permettre d’économiser le temps et les efforts qui seraient nécessaires à leur écriture à partir de zéro.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: c9d36014bc364d8b016221e6b9ff380b0bd4b8ed
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854165"
---
# <a name="create-a-knowledge-base"></a>Créer une base de connaissances

QnA Maker facilite l’ajout de vos sources de données existantes lors de la création d’une base de connaissances. Vous pouvez créer une base de connaissances QnA Maker à partir des types de documents suivants :

<!-- added for scanability -->
* Pages de FAQ
* Manuels de produits
* Documents structurés

## <a name="steps"></a>Étapes

1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai) avec vos informations d’identification Azure, puis sélectionnez **Créer une base de connaissances**.

2. Si vous n’avez pas encore créé de service QnA Maker, sélectionnez **Create a QnA service** (Créer un service QnA). 

3. Sélectionnez votre locataire Azure, le nom de l’abonnement Azure et le nom de la ressource Azure associée au service QnA Maker parmi les listes à l’**étape 2** dans le portail QnA Maker. Sélectionnez le service Azure QnA Maker qui hébergera la base de connaissances.

    ![Créer un service QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Entrez le nom de votre base de connaissances et les sources de données pour la base de connaissances.

    ![Définir les sources de données](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Donnez un **nom** à votre service. Les noms dupliqués et les caractères spéciaux sont pris en charge.
    - Ajoutez des URL pour les données à extraire. Pour plus d’informations sur les types de sources prises en charge, [cliquez ici](../Concepts/data-sources-supported.md).
    - Chargez des fichiers pour les données à extraire. Consultez les [prix](https://aka.ms/qnamaker-pricing) pour savoir combien de documents vous pouvez ajouter.
    - Si vous souhaitez ajouter manuellement des QnA, vous pouvez ignorer l’**étape 4** illustré dans l’image précédente.

5. Ajoutez des **échanges de conversation** à votre base de connaissances. Choisissez d’ajouter la prise en charge des échanges de conversation pour votre bot en sélectionnant parmi les trois personnalités prédéfinies. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Sélectionnez **Create your KB** (Créer votre base de connaissances).

    ![Créer la base de connaissances](../media/qnamaker-how-to-create-kb/create-kb.png)

7. L’extraction des données peut prendre quelques minutes.

    ![Extraction](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. Une fois votre base de connaissances créée, vous êtes redirigé vers la page **Base de connaissances**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des échanges de conversation personnels](./chit-chat-knowledge-base.md)
