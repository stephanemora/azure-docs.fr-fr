---
title: 'Tutoriel : Partager des ancres avec Azure Cosmos DB'
description: Dans ce tutoriel, vous allez apprendre à partager des identificateurs Azure Spatial Anchors entre des appareils Android/iOS dans Unity avec un service back-end et Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ff431c27dd53e82f9003b658c82ceb3efb5d320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810018"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutoriel : Partage d’Azure Spatial Anchors entre plusieurs sessions et appareils avec un back-end Azure Cosmos DB

Ce tutoriel est la suite du [partage d’Azure Spatial Anchors entre des sessions et des appareils](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). Il vous guidera tout au long du processus d'ajout de quelques fonctionnalités supplémentaires permettant de transformer Azure Cosmos DB en stockage back-end tout en partageant des ancres spatiales Azure entre des sessions et des appareils.

![GIF illustrant la persistance des objets](./media/persistence.gif)

Il est important de noter que vous utiliserez Unity et Azure Cosmos DB dans ce tutoriel uniquement pour illustrer un exemple de la façon de partager des identificateurs Spatial Anchors entre appareils. Vous pouvez utiliser d’autres langages et technologies back-end pour atteindre le même objectif.

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

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé Azure Cosmos DB pour partager des identificateurs d’ancre entre des appareils. Pour en savoir plus sur l’utilisation d’Azure Spatial Anchors dans une nouvelle application Unity HoloLens, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Démarrage d’une nouvelle application Unity HoloLens](./tutorial-new-unity-hololens-app.md)
