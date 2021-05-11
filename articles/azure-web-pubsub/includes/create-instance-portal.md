---
title: Comment créer une instance Azure Web PubSub avec le portail ?
description: Fichier include sur la création d’une instance Azure Web PubSub avec le portail
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: 72e3c56b4f8fdaeec94bff60be45aadfb9b09e76
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166886"
---
## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [https://portal.azure.com/](https://portal.azure.com/) avec votre compte Azure.

## <a name="create-an-azure-web-pubsub-service-instance"></a>Créer une instance de service Azure Web PubSub

Votre application se connectera à l’instance de service Web PubSub dans Azure.

1. Cliquez sur le bouton Nouveau dans le coin supérieur gauche du portail Azure. À l’écran Nouveau, tapez *Web PubSub* dans la zone de recherche et appuyez sur Entrée.

1. Sélectionnez **Web PubSub** dans les résultats de la recherche, puis **Créer**.

1. Entrez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de la ressource** | Nom globalement unique | Le nom qui identifie votre nouvelle instance de service Web PubSub. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | L’abonnement sous lequel cette instance de service Web PubSub est créée. | 
    | **[Groupe de ressources](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Le nom du nouveau groupe de ressources dans lequel créer l’instance de service Web PubSub. | 
    | **Lieu** | USA Ouest | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. |
    | **Niveau tarifaire** | Gratuit | Essayez gratuitement le service Azure Web PubSub. |
    | **Nombre d’unité** |  Non applicable | Le nombre d’unité spécifie le nombre de connexions que votre instance de service Web PubSub peut accepter. Ceci n’est configurable que dans le niveau Standard. |

1. Sélectionnez **Créer** pour commencer le déploiement de l’instance de service Web PubSub.
