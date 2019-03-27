---
title: 'Tutoriel : Partage entre des sessions et appareils avec Azure Spatial Anchors | Microsoft Docs'
description: Dans ce tutoriel, vous allez apprendre à partager des identificateurs Azure Spatial Anchor entre des appareils Android/iOS dans Unity avec un service back-end.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7d9fe58b7db60513eed81aae628ebd7ca754a53a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901302"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors"></a>Didacticiel : Partage entre des sessions et appareils avec Azure Spatial Anchors

Ce tutoriel vous explique comment utiliser [Azure Spatial Anchors](../overview.md) pour :

1. Créer des ancres dans une session et les localiser ensuite dans une autre session sur le même appareil ou un appareil différent, par exemple un autre jour.
2. Créer des ancres qui peuvent être localisées par plusieurs appareils au même endroit en même temps.

![Persistance](./media/persistence.gif)

Azure Spatial Anchors est un service de développement multiplateforme qui vous permet de créer des expériences de réalité mixte en utilisant des objets qui conservent leur emplacement sur les appareils. Quand vous avez terminé, vous disposez d’une application qui peut être déployée sur plusieurs appareils. Les ancres spatiales Azure créées par une instance peuvent être partagées avec d’autres.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Déployer une application web ASP.NET Core dans Azure qui peut être utilisée pour partager des ancres, en les stockant en mémoire pendant un certain temps.
> * Configurer la scène AzureSpatialAnchorsLocalSharedDemo dans l’exemple Unity à partir de nos guides de démarrage rapide pour tirer parti de l’application web de partage des ancres.
> * Déployer et exécuter une application sur un ou plusieurs appareils.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Il est important de noter que vous allez utiliser Unity et une application web ASP.NET Core dans ce tutoriel uniquement pour illustrer un exemple de la façon de partager des identificateurs d’ancre spatiale Azure entre d’autres appareils. Vous pouvez utiliser d’autres langages et technologies back-end pour atteindre le même objectif. Par ailleurs, l’application web ASP.NET Core utilisée dans ce tutoriel a une dépendance sur le kit SDK .NET Core 2.2. Elle s’exécute correctement sur Azure Web Apps standard (pour Windows), mais ne fonctionne pas sur Azure Web Apps pour Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Ouvrir l’exemple de projet dans Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Déployer votre service de partage des ancres

Ouvrez Visual Studio, puis ouvrez le projet au dossier `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="open-the-sample-project-in-unity"></a>Ouvrir l’exemple de projet dans Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé une application web ASP.NET Core dans Azure, puis configuré et déployé une application Unity. Vous avez créé des ancres spatiales avec l’application, puis les avez partagées avec d’autres appareils à l’aide de votre application web ASP.NET Core.

Pour en savoir plus sur la façon d’améliorer votre application web ASP.NET Core afin qu’elle utilise Azure Cosmos DB pour stocker les ancres spatiales partagées, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser Azure Cosmos DB pour stocker des ancres](./tutorial-use-cosmos-db-to-store-anchors.md)
