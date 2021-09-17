---
title: Comment créer une instance Azure Web PubSub avec le portail ?
description: Fichier include sur la création d’une instance Azure Web PubSub avec le portail
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: 38571df633c50cf309f15bd98218e77ab75c610e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734180"
---
## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [https://portal.azure.com/](https://portal.azure.com/) avec votre compte Azure.

## <a name="create-an-azure-web-pubsub-service-instance"></a>Créer une instance de service Azure Web PubSub

Votre application se connectera à l’instance de service Web PubSub dans Azure.

1. Cliquez sur le bouton Nouveau dans le coin supérieur gauche du portail Azure. À l’écran Nouveau, saisissez *Web PubSub* dans la zone de recherche et appuyez sur Entrée. (Vous pouvez également effectuer une recherche dans Azure Web PubSub à partir de la catégorie `Web`.)

    :::image type="content" source="../media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="Capture d’écran d’une recherche dans Azure Web PubSub depuis le portail.":::

2. Sélectionnez **Web PubSub** dans les résultats de la recherche, puis **Créer**.

3. Entrez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de la ressource** | Nom globalement unique | Nom unique au monde qui identifie votre nouvelle instance de service Web PubSub. Les caractères valides sont `a-z`, `0-9` et `-`. |
    | **Abonnement** | Votre abonnement | Abonnement Azure sous lequel cette instance de service Web PubSub est créée. |
    | **[Groupe de ressources](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Le nom du nouveau groupe de ressources dans lequel créer l’instance de service Web PubSub. |
    | **Lieu** | USA Ouest | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. |
    | **Niveau tarifaire** | Gratuit | Vous pouvez d’abord essayer le service Azure Web PubSub gratuitement. En savoir plus sur les [niveaux tarifaires du service Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub/) |
    | **Nombre d’unité** |  - | Le nombre d’unité spécifie le nombre de connexions que votre instance de service Web PubSub peut accepter. Chaque unité prend en charge 1 000 connexions simultanées au maximum. Ceci n’est configurable que dans le niveau Standard. |

    :::image type="content" source="../media/howto-develop-create-instance/create-web-pubsub-instance-in-portal.png" alt-text="Capture d’écran illustrant la création d’une instance Azure Web PubSub dans le portail.":::

4. Sélectionnez **Créer** pour commencer le déploiement de l’instance de service Web PubSub.
