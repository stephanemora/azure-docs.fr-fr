---
title: Créer une base de connaissances – QnA Maker
titleSuffix: Azure Cognitive Services
description: Utilisez le portail de service de l’API QnA Maker pour ajouter/créer une base de connaissances avec des échanges de conversation. Votre application s’en trouvera plus attrayante. Ajoutez à votre base de connaissances un ensemble prérempli des principaux échanges comme point de départ de la conversation de votre bot, ce qui vous évitera de les écrire entièrement.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dfd0663c09b78539a1d6b78aa7820385b9ddbdca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563078"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Démarrage rapide : Créer une base de connaissances à partir du portail de service de l’API QnA Maker

Le portail de service de l’API QnA Maker facilite l’ajout de vos sources de données existantes lors de la création d’une base de connaissances. Vous pouvez créer une base de connaissances QnA Maker à partir des types de documents suivants :

<!-- added for scanability -->
* Pages de FAQ
* Manuels de produits
* Documents structurés

Incluez une personnalité d’échanges de conversation pour rendre votre base de connaissances plus attrayante aux yeux de vos utilisateurs.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="create-a-new-knowledge-base"></a>Créer une base de connaissances

1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai) avec vos informations d’identification Azure, puis sélectionnez **Créer une base de connaissances**.

1. Si vous n’avez pas encore créé de service QnA Maker, sélectionnez **Create a QnA service** (Créer un service QnA). 

1. Sélectionnez votre locataire Azure, le nom de l’abonnement Azure et le nom de la ressource Azure associée au service QnA Maker parmi les listes à l’**étape 2** dans le portail QnA Maker. Sélectionnez le service Azure QnA Maker qui hébergera la base de connaissances.

    ![Créer un service QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Entrez le nom de votre base de connaissances et les sources de données pour la base de connaissances.

    ![Définir les sources de données](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Donnez un **nom** à votre service. Les noms dupliqués et les caractères spéciaux sont pris en charge.
    - Ajoutez des URL pour les données à extraire. Pour plus d’informations sur les types de sources prises en charge, [cliquez ici](../Concepts/data-sources-supported.md).
    - Chargez des fichiers pour les données à extraire. Consultez les [prix](https://aka.ms/qnamaker-pricing) pour savoir combien de documents vous pouvez ajouter.
    - Si vous souhaitez ajouter manuellement des QnA, vous pouvez ignorer l’**étape 4** illustré dans l’image précédente.

1. Ajoutez des **échanges de conversation** à votre base de connaissances. Choisissez d’ajouter la prise en charge des échanges de conversation pour votre bot en effectuant un choix parmi les personnalités. 

    ![Ajouter des échanges de conversation à la base de connaissances](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Sélectionnez **Create your KB** (Créer votre base de connaissances).

    ![Créer la base de connaissances](../media/qnamaker-how-to-create-kb/create-kb.png)

1. L’extraction des données peut prendre quelques minutes.

    ![Extraction](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Une fois votre base de connaissances créée, vous êtes redirigé vers la page **Base de connaissances**.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous en avez fini avec la base de connaissances, supprimez-la du portail QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

Pour réduire vos coûts, vous pouvez [partager](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) certaines mais pas toutes les ressources Azure créées pour QnA Maker.

> [!div class="nextstepaction"]
> [Ajouter des échanges de conversation personnels](./chit-chat-knowledge-base.md)
