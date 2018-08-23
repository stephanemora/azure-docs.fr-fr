---
title: Création d’un concentrateur d’événements Azure | Microsoft Docs
description: Créer un espace de noms Azure Event Hubs et un concentrateur d’événements avec le portail Azure
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5b468e1758d752cd3001c85b328d064369429499
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42140069"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Créer un espace de noms Event Hubs et un concentrateur d’événements avec le portail Azure

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

1. Connectez-vous au [portail Azure][Azure portal], puis cliquez sur **Créer une ressource** en haut à gauche de l’écran.
2. Cliquez sur **Internet des objets**, puis sur **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. Dans **Créer un espace de noms**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.  

4. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique ou Standard). Choisissez également un abonnement Azure, un groupe de ressources et un emplacement où créer la ressource.
 
5. Cliquez sur **Créer** pour créer l’espace de noms. Vous devrez peut-être attendre quelques minutes pour que le système approvisionne entièrement les ressources.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. Dans la liste des espaces de noms du portail, cliquez sur l’espace de noms que vous venez de créer.

7. Cliquez sur **Stratégies d’accès partagé**, puis sur **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Cliquez sur le bouton Copier pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrez cette chaîne de connexion dans un emplacement temporaire, tel que le Bloc-notes, pour une utilisation ultérieure.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Créer un hub d’événements

1. Dans la liste d’espaces de noms Event Hubs, cliquez sur le nouvel espace de noms.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Dans le panneau de l’espace de noms, cliquez sur **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Cliquez sur **+ Event Hub** en haut du panneau.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Tapez un nom pour votre hub d’événements, puis cliquez sur **Créer**. 

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus les hubs d’événements, consultez ces liens :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vue d’ensemble de l'API Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/