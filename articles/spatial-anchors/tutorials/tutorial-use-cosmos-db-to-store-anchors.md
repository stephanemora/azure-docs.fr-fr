---
title: 'Didacticiel : Partager des ancres avec Azure Cosmos DB'
description: Dans ce tutoriel, vous allez apprendre à partager des identificateurs Azure Spatial Anchors entre des appareils Android/iOS dans Unity avec un service back-end et Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57bf9c9752cf2203ffcac08c213373fd71a79148
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276876"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Didacticiel : Partage d’Azure Spatial Anchors entre plusieurs sessions et appareils avec un back-end Azure Cosmos DB

Ce tutoriel est la suite du [partage d’Azure Spatial Anchors entre des sessions et des appareils](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). Il vous guide tout au long du processus d’ajout de quelques fonctionnalités supplémentaires permettant de faire d’Azure Cosmos DB le stockage back-end tout en partageant des ancres spatiales Azure entre des sessions et des appareils.

![GIF illustrant la persistance des objets](./media/persistence.gif)

Il est important de noter que vous utiliserez Unity et Azure Cosmos DB dans ce tutoriel uniquement pour illustrer un exemple de la façon de partager des identificateurs Spatial Anchors entre appareils. Vous pouvez utiliser d’autres langages et technologies back-end pour atteindre le même objectif. Par ailleurs, l’application web ASP.NET Core utilisée dans ce tutoriel nécessite le kit SDK .NET Core 2.2. Elle fonctionne correctement sur Web Apps pour Windows, mais ne s’exécute pas sur Web Apps pour Linux pour l’instant.

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Ajoutez une base de données Azure Cosmos au groupe de ressources que vous avez créé précédemment.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copiez la `Connection String`, car vous en aurez besoin.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Apporter des changements mineurs aux fichiers SharingService

Dans l’**Explorateur de solutions**, ouvrez `SharingService\Startup.cs`.

Recherchez `#define INMEMORY_DEMO` en haut du fichier et décommentez cette ligne. Enregistrez le fichier .

Dans l’**Explorateur de solutions**, ouvrez `SharingService\appsettings.json`.

Recherchez la propriété `StorageConnectionString` et affectez-lui la valeur `Connection String` que vous avez copiée lors de l’[étape de création d’un compte de base de données](#create-a-database-account). Enregistrez le fichier .

Vous pouvez republier le service de partage et exécuter l’exemple d’application.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé Azure Cosmos DB pour partager des identificateurs d’ancre entre des appareils. Pour en savoir plus sur l’utilisation d’Azure Spatial Anchors dans une nouvelle application Unity HoloLens, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Démarrage d’une nouvelle application Unity HoloLens](./tutorial-new-unity-hololens-app.md)
