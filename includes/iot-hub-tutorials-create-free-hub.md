---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a7a86c6a2661a8a1f30491391fc76f4dc5d71f54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66163450"
---
Pour créer un hub IoT à l’aide du portail Azure :

1. Connectez-vous au [Portail Azure](http://portal.azure.com).

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **IoT Hub**.

    ![Sélectionnez cette option pour installer IoT Hub](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Pour créer votre hub IoT de niveau gratuit, utilisez les valeurs des tableaux suivants :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Groupe de ressources | Créer. Ce didacticiel utilise le nom **tutorials-iot-hub-rg**. |
    | Région | Ce didacticiel utilise **USA Ouest**. Vous pouvez choisir la région la plus proche. |
    | Nom | La capture d’écran suivante utilise le nom **tutorials-iot-hub**. Vous devez choisir votre propre nom unique quand vous créez ce hub. |

    ![Paramètres du hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Paramètre | Valeur |
    | ------- | ----- |
    | Tarification et niveau de mise à l’échelle | F1 gratuit. Vous ne pouvez avoir qu’un seul hub de niveau gratuit dans un abonnement. |
    | Unités IoT Hub | 1 |

    ![Paramètres du hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Cliquez sur **Créer**. La création du hub peut prendre plusieurs minutes.

    ![Paramètres du hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Notez le nom de hub IoT choisi. Vous utiliserez cette valeur plus loin dans le didacticiel.