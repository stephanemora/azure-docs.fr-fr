---
title: 'Tutoriel : Partage entre des sessions et appareils avec Azure Spatial Anchors et un back-end Azure Cosmos DB | Microsoft Docs'
description: Dans ce tutoriel, vous allez apprendre à partager des identificateurs d’ancre Azure Spatial Anchors entre des appareils dans Unity avec un service back-end et Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753456"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>Tutoriel : Partage entre des sessions et appareils avec Azure Spatial Anchors et un back-end Azure Cosmos DB

Ce tutoriel vous explique comment utiliser [Azure Spatial Anchors](../overview.md) pour :

1. Créer des ancres dans une session et les localiser ensuite dans une autre session sur le même appareil ou un appareil différent, par exemple un autre jour.
2. Créer des ancres qui peuvent être localisées par plusieurs appareils au même endroit en même temps.

![Persistance](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils au fil du temps. Quand vous avez terminé, vous disposez d’une application qui peut être déployée sur plusieurs appareils. Les ancres Azure Spatial Anchors créées par une instance partagent leurs identificateurs avec d’autres à l’aide de Cosmos DB.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer une application web ASP.NET Core dans Azure qui peut être utilisée pour partager des ancres, en les stockant dans Cosmos DB.
> * Configurer la scène AzureSpatialAnchorsLocalSharedDemo dans l’exemple Unity à partir de nos guides de démarrage rapide pour tirer parti de l’application web de partage des ancres.
> * Déployer et exécuter une application sur un ou plusieurs appareils.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Il est important de noter que vous allez utiliser Unity et Azure Cosmos DB dans ce tutoriel uniquement pour illustrer un exemple de la façon de partager des identificateurs d’ancre Azure Spatial Anchors entre d’autres appareils. Vous pouvez utiliser d’autres langages et technologies back-end pour atteindre le même objectif. En outre, l’application web ASP.NET Core utilisée dans ce tutoriel a une dépendance sur le kit SDK .NET Core 2.2. Elle s’exécute correctement sur Azure Web Apps standard (pour Windows), mais ne fonctionne pas sur Azure Web Apps pour Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Notez la valeur de `Connection String`, car elle sera utilisée par la suite.

## <a name="deploy-your-sharing-anchors-service"></a>Déployer votre service de partage des ancres

Ouvrez Visual Studio, puis ouvrez le projet au dossier `Sharing\SharingServiceSample`.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Configurer le service afin qu’il utilise Cosmos DB

Dans l’**Explorateur de solutions**, ouvrez `SharingService\Startup.cs`.

Recherchez la ligne `#define INMEMORY_DEMO` en haut du fichier et commentez-la. Enregistrez le fichier .

Dans l’**Explorateur de solutions**, ouvrez `SharingService\appsettings.json`.

Recherchez la propriété `StorageConnectionString` et affectez-lui la valeur `Connection String` que vous avez notée lors de l’[étape de création d’un compte de base de données](#create-a-database-account). Enregistrez le fichier .

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé Azure Cosmos DB pour partager des identificateurs d’ancre entre des appareils. Pour en savoir plus sur la bibliothèque Azure Spatial Anchors, passez à notre guide sur la création et la localisation des ancres.

> [!div class="nextstepaction"]
> [Créer et localiser des ancres à l’aide d’Azure Spatial Anchors](../create-locate-anchors-overview.md)
