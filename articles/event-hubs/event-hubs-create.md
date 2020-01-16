---
title: Démarrage rapide Azure - Créer un Event Hub à l'aide du portail Azure
description: Dans ce démarrage rapide, vous allez apprendre à créer un Event Hub Azure à l’aide du portail Azure et à envoyer et recevoir des événements à l’aide du Kit de développement .NET Standard.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 8b6a9978d6db1a8978f993abf16d7cfbfb2f44a9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771017"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Démarrage rapide : Créer un hub d’événements avec le portail Azure
Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Dans ce démarrage rapide, vous créez un Event Hub avec le [portail Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide de démarrage rapide, veillez à avoir :

- Abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Visual Studio 2019](https://www.visualstudio.com/vs) ou version ultérieure.
- [Kit SDK .NET Standard](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est une collection logique de ressources Azure. Toutes les ressources sont déployées et gérées dans un groupe de ressources. Pour créer un groupe de ressources :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet de navigation de gauche, cliquez sur **Groupes de ressources**. Cliquez ensuite sur **Ajouter**.

   ![Groupes de ressources - Ajouter un bouton](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Sous **Abonnement**, sélectionnez le nom de l’abonnement Azure dans lequel vous souhaitez créer le groupe de ressources.
3. Tapez un **nom unique pour le groupe de ressources**. Le système vérifie immédiatement si le nom est disponible dans l’abonnement Azure actuellement sélectionné.
4. Sélectionnez une **région** pour le groupe de ressources.
5. Sélectionnez **Vérifier + créer**.

   ![Groupe de ressources - Créer](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Dans la page **Vérifier + créer**, sélectionnez **Créer**. 

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Un espace de noms Event Hubs fournit un conteneur d’étendue unique, référencé par son nom de domaine complet, dans lequel vous créez un ou plusieurs hubs d’événements. Pour créer un espace de noms dans votre groupe de ressources à l’aide du portail, effectuez les actions suivantes :

1. Dans le portail Azure, cliquez sur **Créer une ressource** en haut à gauche de l’écran.
2. Sélectionnez **Tous les services** dans le menu de gauche, puis l’**étoile (`*`)** à côté d’**Event Hubs** dans la catégorie **Analytique**. Confirmez l’ajout d’**Event Hubs** dans **FAVORIS** dans le menu de navigation de gauche. 
    
   ![Rechercher Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Sélectionnez **Event Hubs** sous **FAVORIS** dans le menu de navigation de gauche, puis **Ajouter** dans la barre d’outils.

   ![Bouton Ajouter](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Dans la page **Créer un espace de noms**, effectuez les étapes suivantes :
    1. Entrez un **nom** pour l’espace de noms. Le système vérifie immédiatement si le nom est disponible.
    2. Choisissez le **niveau tarifaire** (De base ou Standard).
    3. Notez que l’option **Activer Kafka** est automatiquement activée. Azure Event Hubs offre un point de terminaison Kafka. Ce point de terminaison permet à votre espace de noms Event Hubs de comprendre de manière native le protocole de message et les API [Apache Kafka](https://kafka.apache.org/intro). Cette fonctionnalité vous permet de communiquer avec vos hubs d’événements comme avec les rubriques Kafka sans changer vos clients de protocole ni exécuter vos propres clusters. Event Hubs prend en charge [Apache Kafka versions 1.0](https://kafka.apache.org/10/documentation.html) et ultérieures.
    4. Sélectionnez l’**abonnement** dans lequel vous souhaitez créer l’espace de noms.
    5. Sélectionnez un **groupe de ressources** existant ou créez-en un. 
    4. Sélectionnez un **emplacement** pour l’espace de noms.
    5. Sélectionnez **Create** (Créer). Vous devrez peut-être attendre quelques minutes pour que le système approvisionne entièrement les ressources.

       ![Créer un espace de noms Event Hub](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Actualisez la page **Event Hubs** pour voir l’espace de noms Event Hubs. Vous pouvez examiner l’état de la création du hub d’événements dans les alertes. 

    ![Créer un espace de noms Event Hub](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Sélectionnez l’espace de noms. La page d’accueil de votre **espace de noms Event Hubs** apparaît dans le portail. 

   ![Page d’accueil de l’espace de noms](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Créer un hub d’événements

Pour créer un Event Hub dans l’espace de noms, effectuez les actions suivantes :

1. Dans la page Espace de noms Event Hubs, sélectionnez **Event Hubs** dans le menu de gauche.
1. Cliquez sur **+ Event Hub** en haut de la fenêtre.
   
    ![Ajouter Event Hub - bouton](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Tapez un nom pour votre hub d’événements, puis cliquez sur **Créer**.
   
    ![Créer un event hub](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Vous pouvez examiner l’état de la création du hub d’événements dans les alertes. Une fois créé, il apparaît dans la liste des hubs d’événements, comme indiqué dans l’image suivante :

    ![Hub d’événements créé](./media/event-hubs-quickstart-portal/event-hub-created.png)

Félicitations ! Vous avez utilisé le portail pour créer un espace de noms Event Hubs, ainsi qu’un hub d’événements dans cet espace de noms. 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un groupe de ressources, un espace de noms Event Hubs et un Event Hub. Pour obtenir des instructions pas à pas sur l’envoi d’événements à un hub d’événements ou leur réception à partir d’un hub d’événements, consultez les tutoriels **Envoyer et recevoir des événements** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.JS](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (envoi uniquement)](event-hubs-c-getstarted-send.md)
- [Apache Storm (réception uniquement)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
