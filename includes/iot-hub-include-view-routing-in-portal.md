---
title: Fichier include
description: Fichier include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 10/19/2021
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 63afee858ce8f536c5573a3db65957611af16bc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288001"
---
Maintenant que vos points de terminaison et les routes de messages sont configurés, vous pouvez examiner leur configuration sur le portail. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez aux **Groupes de ressources**. Sélectionnez ensuite votre groupe de ressources, puis votre hub (le nom de hub commence par `ContosoTestHub` dans ce tutoriel). Le volet IoT Hub s’affiche.

:::image type="content" source="./media/iot-hub-include-view-routing-in-portal/01-show-hub-properties.png" alt-text="Écran des propriétés IoT Hub" border="true":::

Dans les options IoT Hub, sélectionnez **Routage des messages**. Les routes que vous avez configurées avec succès sont affichées.

![Routes que vous avez configurées](./media/iot-hub-include-view-routing-in-portal/02-show-message-routes.png)

Dans l’écran **Routage des messages**, sélectionnez **Points de terminaison personnalisés** pour voir les points de terminaison que vous avez définis pour les routes.

![Points de terminaison configurés pour les routes](./media/iot-hub-include-view-routing-in-portal/03-show-routing-endpoints.png)