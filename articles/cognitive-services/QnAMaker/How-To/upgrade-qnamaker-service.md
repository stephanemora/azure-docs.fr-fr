---
title: Mettre à niveau votre service QnA Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: Partagez ou mettez à niveau vos services QnA Maker afin de mieux gérer les ressources.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640483"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Partager ou mettre à niveau votre service QnA Maker
Partagez ou mettez à niveau vos services QnA Maker afin de mieux gérer les ressources. 

Vous pouvez choisir de mettre à niveau des composants individuels de la pile QnA Maker après la création initiale. Consultez les détails des composants dépendants et la sélection de références SKU [ici](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Partager des services existants avec QnA Maker

QnA Maker crée plusieurs ressources Azure. Pour alléger la gestion et profiter du partage des coûts, utilisez le tableau suivant pour comprendre ce que vous pouvez et ne pouvez pas partager :

|de diffusion en continu|Partager|
|--|--|
|Cognitive Services|X|
|Plan App Service|✔|
|App Service|X|
|Application Insights|✔|
|Service de recherche|✔|

## <a name="upgrade-qna-maker-management-sku"></a>Mise à niveau de la référence SKU de gestion de QnA Maker

Si vous avez besoin d’avoir plus de questions et de réponses dans votre base de connaissances, au-delà de votre niveau actuel, mettez à niveau votre niveau tarifaire du service QnA Maker. 

Pour mettre à niveau la référence SKU de gestion de QnA Maker :

1. Accédez à votre ressource QnA Maker dans le portail Azure et sélectionnez **Niveau tarifaire**.

    ![Ressource QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Choisissez la référence SKU appropriée et appuyez sur **Sélectionner**.

    ![Tarification de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Mise à niveau du service d’application

 Lorsque votre base de connaissances doit répondre à un plus grand nombre de demandes de votre application client, mettez à niveau le niveau tarifaire de vos services applicatifs.

Vous pouvez faire [monter en puissance](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou faire descendre en puissance le service d’application.

1. Accédez à la ressource de service d’application dans le portail Azure et sélectionnez l’option **monter en puissance** ou **descendre en puissance** en fonction des besoins.

    ![Mise à l'échelle du service d’application QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Mise à niveau du service Recherche Azure

Lorsque vous prévoyez de disposer de plusieurs bases de connaissances, mettez à niveau votre niveau tarifaire du service Recherche Azure. 

Il n’est actuellement pas possible d’effectuer une mise à niveau sur place de la référence SKU de la recherche Azure. Toutefois, vous pouvez créer une ressource de recherche Azure avec la référence SKU souhaitée, restaurer les données vers la nouvelle ressource, puis la lier à la pile QnA Maker.

1. Créez une ressource de recherche Azure dans le portail Azure et choisissez la référence SKU souhaitée.

    ![Ressources de recherche QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Restaurez les index de votre ressource de recherche Azure d’origine vers la nouvelle. Consultez l’exemple de code de restauration de sauvegarde [ici](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Une fois que les données sont restaurées, accédez à votre nouvelle ressource de recherche Azure, sélectionnez **Clés**, puis notez le **Nom** et la **Clé d’administration**.

    ![Clés de recherche QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Pour lier la nouvelle ressource de recherche Azure à la pile QnA Maker, accédez au service d’application QnA Maker.

    ![Service d’application QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Sélectionnez **Paramètres d'application** et remplacez les champs **AzureSearchName** et **AzureSearchAdminKey** à partir de l’étape 3.

    ![Paramètre du service d’application Service QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Redémarrez le service d'application.

    ![Le service d’application QnA Maker redémarre](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser l’API QnA Maker](../Quickstarts/csharp.md)
