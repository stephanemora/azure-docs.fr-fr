---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/08/2021
ms.openlocfilehash: 1cd52fc7930407317d426b3e2615202dce37e860
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751164"
---
## <a name="get-the-client-url-with-a-temp-access-token"></a>Récupération de l’URL du client avec un jeton d’accès temporaire

Le Portail Azure fournit un générateur d’URL de client simple qui permet de générer une URL temporaire à des fins de test/validation rapide. Utilisons cet outil pour obtenir une URL d’accès client temporaire et nous connecter à l’instance.

- Accédez au portail Azure et recherchez l’instance Azure Web PubSub.
- Accédez à `Client URL Generator` dans le panneau `Key`. 
- Définissez `Roles` correctement : **Envoyer aux groupes** et **Rejoindre/quitter des groupes**
- Générez et copiez `Client Access URL`. 

:::image type="content" source="../media/quickstart-live-demo/generate-client-url.png" alt-text="Capture d’écran de génération de l’URL du client.":::

## <a name="try-the-instance-with-an-online-demo"></a>Essai de l’instance avec une démonstration en ligne

Cette démonstration en direct vous permet de rejoindre ou de quitter un groupe et d’envoyer facilement des messages à ses membres :

> [!div class="nextstepaction"]
> [Ouvrir la démonstration](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)

> [!NOTE]
>  L’**URL d’accès client** est un outil pratique fourni dans le portail pour simplifier votre expérience de prise en main. Vous pouvez également utiliser cette URL d’accès client pour effectuer un test de connexion rapide. Pour écrire votre propre application, nous fournissons des kits de développement logiciel (SDK) dans 4 langues et vous permettre de générer l’URL. 

- Essayez de rejoindre différents groupes et de leur envoyer des messages, puis vérifiez les messages reçus. Par exemple :
    - Effectuez la jointure de deux clients dans le même groupe. Vous verrez que le message peut être diffusé aux membres du groupe. 
    - Effectuez la jointure de deux clients dans des groupes différents. Vous verrez que le client ne peut pas recevoir de message s’il n’est pas membre du groupe. 
- Vous pouvez aussi essayer de décocher `Roles` lors de la génération de la valeur `Client Access URL` pour voir ce qui se passe quand vous rejoignez un groupe ou envoyez des messages à un groupe. Par exemple :
    - Décochez l'autorisation `Send to Groups`. Vous verrez que le client ne peut pas envoyer de messages au groupe. 
    - Décochez l'autorisation `Join/Leave Groups`. Vous verrez que le client ne peut pas rejoindre de groupe. 
