---
title: Comment créer une instance Azure Web PubSub avec le portail ?
description: Fichier include sur la création d’une instance Azure Web PubSub avec le portail
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: e147d6f954009f8ed4267f8b947438e2e60da0b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110486412"
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
    | **Nom de la ressource** | Nom globalement unique | Le nom qui identifie votre nouvelle instance de service Web PubSub. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | L’abonnement sous lequel cette instance de service Web PubSub est créée. | 
    | **[Groupe de ressources](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Le nom du nouveau groupe de ressources dans lequel créer l’instance de service Web PubSub. | 
    | **Lieu** | USA Ouest | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. |
    | **Niveau tarifaire** | Gratuit | Essayez gratuitement le service Azure Web PubSub. |
    | **Nombre d’unité** |  Non applicable | Le nombre d’unité spécifie le nombre de connexions que votre instance de service Web PubSub peut accepter. Ceci n’est configurable que dans le niveau Standard. |

4. Sélectionnez **Créer** pour commencer le déploiement de l’instance de service Web PubSub.
