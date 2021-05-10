---
title: Démonstration en direct du service Azure Web PubSub
description: Guide de démarrage rapide pour démarrer avec la démonstration en direct du service Azure Web PubSub.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: a516d215a971b2a6cb54d4e73305cc69ce0edf8c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166890"
---
# <a name="quickstart-get-started-with-live-demo"></a>Démarrage rapide : Bien démarrer avec la démonstration en direct

Le service Azure Web PubSub vous aide à créer facilement des applications web de messagerie en temps réel avec des WebSockets et le modèle publication-abonnement. Dans ce guide de démarrage rapide, découvrez comment bien démarrer avec une démonstration en direct.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-live-demo"></a>Bien démarrer avec la démonstration en direct

### <a name="get-client-url-with-a-temp-access-token"></a>Obtenir l’URL du client avec un jeton d’accès temporaire

Pour commencer, vous avez besoin d’obtenir l’URL du client à partir de l’instance Azure Web PubSub. 

- Accédez au portail Azure et recherchez l’instance Azure Web PubSub.
- Accédez à `Client URL Generator` dans le panneau `Key`. 
- Définissez `Roles` correctement : **Envoyer aux groupes** et **Rejoindre/quitter des groupes**
- Générez et copiez `Client Access URL`. 

### <a name="try-the-live-demo"></a>Tester la démonstration en direct 

Cette démonstration en direct vous permet de rejoindre ou quitter un groupe et d’envoyer facilement des messages aux membres d’un groupe. 

- Ouvrez la [démonstration Pub/Sub du client](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html), collez la valeur de `Client Access URL` et connectez-vous. 
- Essayez de rejoindre différents groupes et de leur envoyer des messages, puis vérifiez les messages reçus.
- Vous pouvez aussi essayer de décocher `Roles` lors de la génération de la valeur `Client Access URL` pour voir ce qui se passe quand vous rejoignez/quittez un groupe ou envoyez des messages à un groupe.