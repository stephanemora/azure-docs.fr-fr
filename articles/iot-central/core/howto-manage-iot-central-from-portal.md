---
title: Gérer IoT Central à l’aide du Portail Azure | Microsoft Docs
description: Cet article explique comment créer et gérer des applications IoT Central sur le Portail Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: 3e5e126815d0171a6c1627a08419b05b9a3c0c23
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719202"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gérer IoT Central à l’aide du Portail Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour créer et gérer des applications IoT Central.

## <a name="create-iot-central-applications"></a>Créer des applications IoT Central

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Pour créer une application, accédez à la page [Application IoT Central](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) dans le portail Azure :

![Créer un formulaire IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

* Le **Nom de la ressource** est un nom unique que vous pouvez choisir pour votre application IoT Central dans votre groupe de ressources Azure.

* L’**URL de l’application** est l’URL que vous pouvez utiliser pour accéder à votre application.

* L’**Emplacement** correspond à la [zone géographique](https://azure.microsoft.com/global-infrastructure/geographies/) où vous souhaitez créer votre application. D'une façon générale, il est recommandé de choisir l'emplacement qui est physiquement le plus proche de vos appareils pour obtenir des performances optimales. Azure IoT Central est actuellement disponible aux localisations suivantes :

  * Asie-Pacifique
  * Australie
  * Europe
  * Japon
  * Royaume-Uni
  * États-Unis

  Une fois que vous avez choisi un emplacement, vous ne pouvez pas par la suite déplacer votre application dans un autre emplacement.

Après avoir renseigné tous les champs, sélectionnez **Créer**. Pour plus d’informations, consultez le guide de démarrage rapide [Créer une application IoT Central](quick-deploy-iot-central.md).

## <a name="manage-existing-iot-central-applications"></a>Gérer des applications IoT Central existantes

Si vous disposez déjà d’une application Azure IoT Central, vous pouvez la supprimer ou la déplacer vers un abonnement ou un groupe de ressources différent dans le portail Azure.

> [!NOTE]
> Les applications créées à l’aide du plan *gratuit* ne nécessitent pas d’abonnement Azure. Elles ne sont donc pas listées dans votre abonnement Azure sur le portail Azure. Vous pouvez voir et gérer les applications gratuites uniquement à partir du portail IoT Central.

Pour commencer, recherchez votre application dans la barre de recherche située en haut du portail Azure. Vous pouvez également voir toutes vos applications en recherchant _Applications IoT Central_, puis en sélectionnant le service :

![Capture d’écran qui montre les résultats de la recherche pour « Applications IoT Central » avec le premier service sélectionné.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Quand vous sélectionnez une application dans les résultats de la recherche, le portail Azure vous montre sa vue d’ensemble. Vous pouvez accéder à l’application en sélectionnant l’**URL de l’application IoT Central** :

![Capture d’écran montrant la page « Vue d’ensemble » avec l’option « URL de l’application IoT Central » mise en évidence.](media/howto-manage-iot-central-from-portal/image3.png)

Pour déplacer l’application vers un autre groupe de ressources, sélectionnez le lien **Modifier** à côté du groupe de ressources. Dans la page **Déplacer des ressources**, sélectionnez le groupe de ressources vers lequel vous souhaitez déplacer cette application :

![Capture d’écran montrant la page « Vue d’ensemble » avec l’option « Groupe de ressources (modifier) » mise en surbrillance.](media/howto-manage-iot-central-from-portal/image4a.png)

Pour déplacer l’application vers un autre abonnement, sélectionnez **Modifier** en regard de l’abonnement. Dans la page **Déplacer des ressources**, choisissez l’abonnement vers lequel vous souhaitez déplacer cette application :

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans le portail Azure, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)