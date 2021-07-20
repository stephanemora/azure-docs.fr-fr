---
title: Démonstration en direct du service Azure Web PubSub
description: Guide de démarrage rapide pour démarrer avec la démonstration en direct du service Azure Web PubSub.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e35ef66c038748e31ba218a56adb8111374d2339
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219603"
---
# <a name="quickstart-get-started-with-chatroom-live-demo"></a>Démarrage rapide : Bien démarrer avec la démonstration en direct de salon de conversation

Le service Azure Web PubSub vous aide à créer facilement des applications web de messagerie en temps réel avec des WebSockets et le modèle publication-abonnement. La [démonstration en direct de démonstration de salon](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html) illustre la fonctionnalité de messagerie en temps réel fournie par Azure Web PubSub. Grâce à cette démonstration en direct, vous pouvez facilement rejoindre un groupe de conversation et envoyer un message en temps réel à un groupe spécifique. 

:::image type="content" source="media/quickstart-live-demo/chat-live-demo.gif" alt-text="Utilisation de la démonstration en direct de salon de conversation.":::

Dans ce guide de démarrage rapide, découvrez comment bien démarrer avec une démonstration en direct.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-chatroom-live-demo"></a>Bien démarrer avec la démonstration en direct de salon de conversation

### <a name="get-client-url-with-a-temp-access-token"></a>Obtenir l’URL du client avec un jeton d’accès temporaire

Pour commencer, vous avez besoin d’obtenir l’URL du client à partir de l’instance Azure Web PubSub. 

- Accédez au portail Azure et recherchez l’instance Azure Web PubSub.
- Accédez à `Client URL Generator` dans le panneau `Key`. 
- Définissez `Roles` correctement : **Envoyer aux groupes** et **Rejoindre/quitter des groupes**
- Générez et copiez `Client Access URL`. 

:::image type="content" source="media/quickstart-live-demo/generate-client-url.png" alt-text="Capture d’écran de génération de l’URL du client.":::

### <a name="try-the-live-demo"></a>Tester la démonstration en direct 

Cette démonstration en direct vous permet de rejoindre ou quitter un groupe et d’envoyer facilement des messages aux membres d’un groupe. 

- Ouvrez la [démonstration en direct de salon de conversation](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html), collez l’élément `Client Access URL` et connectez-vous. 

:::image type="content" source="media/quickstart-live-demo/paste-client-access-url.png" alt-text="Capture d’écran de collage de l’URL du client avec la démonstration en direct.":::

> [!NOTE]
>  L’**URL d’accès client** est un outil pratique fourni dans le portail pour simplifier votre expérience de prise en main. Vous pouvez également utiliser cette URL d’accès client pour effectuer un test de connexion rapide. Pour écrire votre propre application, nous fournissons des kits de développement logiciel (SDK) dans 4 langues et vous permettre de générer l’URL. 

- Essayez de rejoindre différents groupes et de leur envoyer des messages, puis vérifiez les messages reçus. Par exemple :
    - Effectuez la jointure de deux clients dans le même groupe. Vous verrez que le message peut être diffusé aux membres du groupe. 
    - Effectuez la jointure de deux clients dans des groupes différents. Vous verrez que le client ne peut pas recevoir de message s’il n’est pas membre du groupe. 
- Vous pouvez aussi essayer de décocher `Roles` lors de la génération de la valeur `Client Access URL` pour voir ce qui se passe quand vous rejoignez un groupe ou envoyez des messages à un groupe. Par exemple :
    - Décochez l'autorisation `Send to Groups`. Vous verrez que le client ne peut pas envoyer de messages au groupe. 
    - Décochez l'autorisation `Join/Leave Groups`. Vous verrez que le client ne peut pas rejoindre de groupe. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez découvert la fonctionnalité de messagerie en temps réel avec la démonstration en direct de salon de conversation. À présent, vous pouvez commencer à créer votre propre application. 

> [!div class="nextstepaction"]
> [Démarrage rapide : Publier et s’abonner à des messages dans Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/publish-messages/js-publish-message)

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un salon de conversation simple avec Azure Web PubSub](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une application de conversation simple serverless avec Azure Functions et le service Azure Web PubSub](./quickstart-serverless.md)

> [!div class="nextstepaction"]
> [Explorer d’autres exemples Azure Web PubSub](https://github.com/Azure/azure-webpubsub/tree/main/samples)