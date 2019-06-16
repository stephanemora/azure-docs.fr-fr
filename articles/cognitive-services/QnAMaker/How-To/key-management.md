---
title: Gestion des clés et des ressources - QnA Maker
titleSuffix: Azure Cognitive Services
description: Votre service QnA Maker porte sur deux types de clés, les clés d’abonnement et les clés de point de terminaison.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 458d587c7ac73f7c8dacdceae3c9f923263533b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792540"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Comment gérer les clés dans QnA Maker

Votre service QnA Maker porte sur deux types de clés, les **clés d’abonnement** et les **clés de point de terminaison**.

![gestion des clés](../media/qnamaker-how-to-key-management/key-management.png)

1. **Clés d’abonnement** : ces clés sont utilisées pour accéder aux [API du service d’administration QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Ces API vous permettent de modifier votre base de connaissances.  

2. **Clés de point de terminaison** : ces clés sont utilisées pour accéder au point de terminaison de la base de connaissances afin d’obtenir une réponse à une question de l’utilisateur. En général, vous utilisez ce point de terminaison dans le code de votre bot conversationnel ou application cliente qui utilise le service QnA Maker.
 
## <a name="subscription-keys"></a>Clés d’abonnement
Vous pouvez afficher et réinitialiser vos clés d’abonnement à partir du portail Azure où la ressource QnA Maker a été créée. 
1. Accédez à la ressource QnA Maker dans le portail Azure.

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Accédez à **Clés**.

    ![clé d’abonnement](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Clés de point de terminaison

Les clés de point de terminaison peuvent être gérées à partir du [portail QnA Maker](https://qnamaker.ai).

1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai), accédez à votre profil, puis cliquez sur **Paramètres de service**.

    ![clé de point de terminaison](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Affichez ou réinitialisez vos clés.

    ![gestionnaire de la clé de point de terminaison](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Actualisez vos clés si vous pensez qu’elles ont été compromises. Cette opération peut nécessiter des modifications correspondantes de votre code de bot ou d’application cliente.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une base de connaissances dans une autre langue](./language-knowledge-base.md)
