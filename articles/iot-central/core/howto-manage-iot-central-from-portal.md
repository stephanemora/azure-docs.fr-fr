---
title: Gérer IoT Central à l’aide du Portail Azure | Microsoft Docs
description: Cet article explique comment créer et gérer des applications IoT Central sur le Portail Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 90bccf76b4c98c732cb926bb4252654d20478412
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018973"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gérer IoT Central à l’aide du Portail Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central sur le site web du [Gestionnaire d'applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour gérer vos applications.

## <a name="create-iot-central-applications"></a>Créer des applications IoT Central

Pour créer une application, accédez au [Portail Azure](https://ms.portal.azure.com), puis sélectionnez **Créer une ressource** dans le volet principal sur la gauche.

![Portail de gestion : menu de navigation](media/howto-manage-iot-central-from-portal/image0.png)

Dans la barre de recherche, tapez **IoT Central**.

![Portail de gestion : recherche](media/howto-manage-iot-central-from-portal/image0a1.png)

Sélectionnez la ligne **Application IoT Central** dans les résultats de la recherche.

![Portail de gestion : résultats de la recherche](media/howto-manage-iot-central-from-portal/image0b1.png)

À présent, sélectionnez **Créer**.

![Portail de gestion : Ressource IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Renseignez tous les champs dans le formulaire. Ce formulaire ressemble à celui que vous devez compléter pour créer des applications sur le site web du [Gestionnaire d'applications Azure IoT Central](https://aka.ms/iotcentral). Pour plus d’informations, consultez le guide de démarrage rapide [Créer une application IoT Central](quick-deploy-iot-central.md).

![créer un formulaire IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

L’**emplacement** correspond à la [zone géographique](https://azure.microsoft.com/global-infrastructure/geographies/) où vous souhaitez créer votre application. D'une façon générale, il est recommandé de choisir l'emplacement qui est physiquement le plus proche de vos appareils pour obtenir des performances optimales. Azure IoT Central est actuellement disponible aux **États-Unis**, en **Australie**, en **Asie-Pacifique** et en **Europe**.  Une fois que vous aurez choisi un emplacement, vous ne pourrez plus déplacer votre application vers un autre emplacement.


Après avoir renseigné tous les champs, sélectionnez **Créer**.

## <a name="manage-existing-iot-central-applications"></a>Gérer des applications IoT Central existantes

Si vous disposez déjà d’une application Azure IoT Central, vous pouvez la supprimer ou la déplacer vers un abonnement ou un groupe de ressources différent dans le portail Azure.

> [!NOTE]
> Vous ne voyez pas les applications créées avec le plan Gratuit dans le portail Azure, car celles-ci ne sont pas associées à votre abonnement.

Pour commencer, sélectionnez **Toutes les ressources** dans le volet principal sur la gauche. Tapez le nom de votre application dans la zone de recherche pour la trouver dans la liste de vos ressources. Sélectionnez ensuite sur l’application IoT Central que vous souhaitez gérer.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image2a.png)

Pour accéder à l’application, sélectionnez l’URL de l’application IoT Central.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image3.png)

Pour déplacer l’application vers un autre groupe de ressources, sélectionnez le lien **Modifier** à côté du groupe de ressources. Dans la page **Déplacer des ressources**, sélectionnez le groupe de ressources vers lequel vous souhaitez effectuer la migration de cette application.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image4a.png)

Pour déplacer l’application vers un autre abonnement, sélectionnez lien **Modifier** à côté de l’abonnement. Dans la boîte de dialogue qui s’affiche, sélectionnez l’abonnement vers lequel vous souhaitez effectuer la migration de l’application.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans le portail Azure, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)