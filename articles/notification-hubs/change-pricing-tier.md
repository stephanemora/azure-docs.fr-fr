---
title: Changer le niveau tarifaire de l'espace de noms Notification Hubs | Microsoft Docs
description: Découvrez comment changer le niveau tarifaire d’un espace de noms Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261073"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Changer le niveau tarifaire d’un espace de noms Azure Notification Hubs
Notification Hubs est disponible en trois niveaux : **Gratuit**, **De base** et **Standard**. Cet article vous montre comment changer le niveau tarifaire d’un espace de noms Azure Notification Hubs. 

## <a name="overview"></a>Vue d’ensemble
Dans Azure Notification Hubs, un **hub** est l’entité/la ressource la plus petite. Elle mappe généralement à une application et peut conserver un certificat pour chaque système Platform Notification System que nous prenons en charge pour l’application. L’application peut être une application multiplateforme hybride ou native.

Un **espace de noms** est une collection de hubs de notification. Chaque espace de noms se compose généralement de hubs qui sont liés et utilisés dans un but spécifique. Par exemple, vous pouvez avoir trois espaces de noms différents pour le développement, le test et la production, respectivement. 

Vous pouvez associer un niveau tarifaire au niveau de l’espace de noms. Notification Hubs prend en charge trois niveaux : **Gratuit**, **De base** et **Standard**. Vous pouvez utiliser le niveau qui répond le mieux à vos besoins pour un espace de noms. Les sections suivantes vous montrent comment changer le niveau tarifaire d’un espace de noms Notification Hubs. 

## <a name="use-azure-portal"></a>Utiliser le portail Azure 
Quand vous utilisez le portail Azure, vous pouvez changer le niveau tarifaire d’un espace de noms dans la page des espace de noms ou la page d’un hub.  Quand vous le changez dans la page d’un hub, vous effectuez en fait ce changement au niveau de l’espace de noms. Cette opération change le niveau tarifaire pour l’espace de noms et tous les hubs qu’il contient. 

### <a name="change-tier-on-the-namespace-page"></a>Changer le niveau tarifaire dans la page des espaces de noms
La procédure suivante indique les étapes à suivre pour changer le niveau tarifaire d’un espace de noms dans la page des espaces de noms. Quand vous changez le niveau pour un espace de noms, celui-ci s’applique à tous les hubs qu’il contient.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** dans le menu de gauche. 
3. Sélectionnez **Espaces de noms du hub de notification** dans la section **Internet des objets**. Si vous sélectionnez l’étoile (`*`) affichée à côté du texte, celui-ci est ajouté à la barre de navigation de gauche sous **FAVORIS**. Cela vous permet d’accéder plus rapidement à la page des espaces de noms la prochaine fois. Après l’avoir ajouté aux FAVORIS, sélectionnez **Espaces de noms du hub de notification**. 

    ![Tous les services -> Espaces de noms du hub de notification](./media/change-pricing-tier/all-services-nhub.png)
1. Dans la page **Espaces de noms du hub de notification**, sélectionnez l’espace de noms pour lequel vous voulez changer le niveau tarifaire. 
2. Dans la page **Espace de noms du hub de notification** de votre espace de noms, vous pouvez voir le niveau tarifaire actuel de l’espace de noms dans la section **Éléments principaux**. Dans l’image suivante, vous pouvez voir que le niveau tarifaire de l’espace de noms est **Gratuit**. 

    ![Niveau tarifaire actuel dans la page des espaces de noms](./media/change-pricing-tier/pricing-tier-before.png)
1. Dans la page **Espace de noms du hub de notification** de votre espace de noms, sélectionnez **Niveau tarifaire** sous la section **Gérer**. 

    ![Sélectionner le niveau tarifaire dans la page des espaces de noms](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Changez votre niveau tarifaire, puis cliquez sur le bouton **Sélectionner**.    
7. Vous voyez l’état de l’action de changement de niveau dans les **alertes**. 
8. Basculez vers la page **Vue d’ensemble**. Vérifiez que le nouveau niveau est affiché dans le champ **Niveau tarifaire** de la section **Éléments principaux**.     
1. Cette étape est facultative. Sélectionnez n’importe quel hub dans l’espace de noms. Vérifiez que vous voyez le même niveau tarifaire dans la section **Éléments principaux**. Vous devez voir le même niveau tarifaire pour tous les hubs de l’espace de noms. 

### <a name="change-tier-on-the-hub-page"></a>Changer le niveau tarifaire dans la page de hub
La procédure suivante indique les étapes à suivre pour changer le niveau tarifaire d’un espace de noms dans la page de hub. Même si vous effectuez ces étapes à partir de la page de hub, vous changez en fait le niveau tarifaire pour l’espace de noms et tous les hubs qu’il contient. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** dans le menu de gauche.
3. Sélectionnez **Hubs de notifications** dans la section **Internet des objets**. 
4. Sélectionnez votre **hub** de notification. 
5. Sélectionnez **Niveau tarifaire** dans le menu de gauche. 
6. Changez le niveau tarifaire, puis cliquez sur le bouton **Sélectionner**. Cette action change le paramètre de niveau tarifaire pour l’espace de noms qui contient le hub. Par conséquent, vous voyez le nouveau niveau tarifaire dans la page de l’espace de noms et dans toutes les pages de hub. 

## <a name="use-rest-api"></a>Avec l’API REST
Vous pouvez utiliser les API REST du fournisseur de ressources suivantes pour obtenir le niveau tarifaire actuel et le mettre à jour. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obtenir le niveau tarifaire actuel pour un espace de noms
Pour obtenir le **niveau actuel de l’espace de noms**, envoyez une commande GET comme indiqué dans l’exemple suivant : 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Mettre à jour le niveau tarifaire d’un espace de noms
Pour **mettre à jour le niveau tarifaire d’un espace de noms**, envoyez une commande PUT comme indiqué dans l’exemple suivant : 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur ces niveaux et les tarifs, consultez [Tarifs de Notification Hubs](https://azure.microsoft.com/pricing/details/notification-hubs/).
