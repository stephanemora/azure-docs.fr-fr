---
title: Tutoriel – Partager Azure Spatial Anchors entre plusieurs sessions et appareils et un back-end Azure Cosmos DB | Microsoft Docs
description: Dans ce tutoriel, vous allez apprendre à partager des identificateurs Azure Spatial Anchors entre des appareils Android/iOS dans Unity avec un service back-end et Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 19c2298c9bda03acba28496a97c89c2a53e3c44e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964900"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Didacticiel : Partager Azure Spatial Anchors entre plusieurs sessions et appareils et un back-end Azure Cosmos DB

Dans ce tutoriel, vous allez apprendre à utiliser [Azure Spatial Anchors](../overview.md) pour créer des ancres au cours d’une session et les localiser ensuite au cours d’une autre session sur le même appareil ou un autre. Par exemple, la deuxième session peut se dérouler un autre jour. Ces mêmes ancres pourront aussi être localisées par plusieurs appareils au même emplacement et au même moment.

![GIF illustrant la persistance des objets](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) est un service de développement multiplateforme que vous pouvez utiliser pour créer des expériences de réalité mixte avec des objets qui conservent leur emplacement sur les appareils. Quand vous avez terminé, vous disposez d’une application qui peut être déployée sur plusieurs appareils. Les ancres spatiales créées par une instance partagent leurs identificateurs avec les autres à l’aide d’Azure Cosmos DB.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer une application web ASP.NET Core dans Azure qui peut être utilisée pour partager des ancres, en les stockant dans Azure Cosmos DB
> * Configurer la scène AzureSpatialAnchorsLocalSharedDemo dans l’exemple Unity à partir des guides de démarrage rapide Azure pour tirer parti de l’application web de partage des ancres
> * Déployer une application sur un ou plusieurs appareils et l’exécuter

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Il est important de noter que vous utiliserez Unity et Azure Cosmos DB dans ce tutoriel uniquement pour illustrer un exemple de la façon de partager des identificateurs Spatial Anchors entre appareils. Vous pouvez utiliser d’autres langages et technologies back-end pour atteindre le même objectif. Par ailleurs, l’application web ASP.NET Core utilisée dans ce tutoriel nécessite le kit SDK .NET Core 2.2. Elle fonctionne correctement sur Web Apps pour Windows, mais ne s’exécute pas sur Web Apps pour Linux pour l’instant.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copiez la `Connection String`, car vous en aurez besoin.

## <a name="open-the-sample-project-in-unity"></a>Ouvrir l’exemple de projet dans Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Déployer le service de partage des ancres

Ouvrez Visual Studio, puis ouvrez le projet dans le dossier `Sharing\SharingServiceSample`.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Configurer le service pour utiliser votre base de données Azure Cosmos DB

Dans l’**Explorateur de solutions**, ouvrez `SharingService\Startup.cs`.

Recherchez `#define INMEMORY_DEMO` en haut du fichier et décommentez cette ligne. Enregistrez le fichier .

Dans l’**Explorateur de solutions**, ouvrez `SharingService\appsettings.json`.

Recherchez la propriété `StorageConnectionString` et affectez-lui la valeur `Connection String` que vous avez copiée lors de l’[étape de création d’un compte de base de données](#create-a-database-account). Enregistrez le fichier .

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé Azure Cosmos DB pour partager des identificateurs d’ancre entre des appareils. Pour en savoir plus sur l’utilisation d’Azure Spatial Anchors dans une nouvelle application Unity HoloLens, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Démarrage d’une nouvelle application Android](./tutorial-new-unity-hololens-app.md)