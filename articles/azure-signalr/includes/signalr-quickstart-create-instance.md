---
title: Fichier Include
description: Fichier Include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 15eded28e38279ea01bf019566d4fda5e7ac6c3e
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57588119"
---
## <a name="create-an-azure-signalr-service-instance"></a>Créer une instance de service SignalR Azure

Votre application se connectera à l’instance de service SignalR dans Azure.

1. Cliquez sur le bouton Nouveau dans le coin supérieur gauche du portail Azure. À l’écran Nouveau, tapez *Service SignalR* dans la zone de recherche et appuyez sur Entrée.

    ![Rechercher Service SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Sélectionnez **Service SignalR** dans les résultats de la recherche, puis **Créer**.

1. Entrez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de la ressource** | Nom globalement unique | Le nom qui identifie votre nouvelle instance de service SignalR. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | L’abonnement sous lequel cette nouvelle instance de service SignalR est créée. | 
    | **[Groupe de ressources](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Le nom du nouveau groupe de ressources dans lequel créer l’instance de service SignalR. | 
    | **Lieu** | USA Ouest | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. |
    | **Niveau tarifaire** | Gratuit | Essayez gratuitement le service Azure SignalR. |
    | **Nombre d’unité** |  Non applicable | Le nombre d’unité spécifie le nombre de connexion que votre instance de service SignalR peut accepter. Ceci n’est configurable que dans le niveau Standard. |

    ![Créer un service SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Sélectionnez **Créer** pour commencer le déploiement de l’instance de service SignalR.

1. Une fois l’instance déployée, ouvrez-la dans le portail, puis recherchez sa page Paramètres. Remplacez le paramètre Mode de service par *Serverless*.

    ![Mode SignalR Service](../media/signalr-concept-azure-functions/signalr-service-mode.png)