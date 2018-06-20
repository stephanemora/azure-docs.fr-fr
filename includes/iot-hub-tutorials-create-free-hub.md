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
ms.openlocfilehash: e3110e4018e214e7e7aa591b811246369c029ecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34667183"
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
    | Région | Ce didacticiel utilise **Ouest des États-Unis**. Vous pouvez choisir la région la plus proche. |
    | NOM | La capture d’écran suivante utilise le nom **tutorials-iot-hub**. Vous devez choisir votre propre nom unique quand vous créez ce hub. |

    ![Paramètres du hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Paramètre | Valeur |
    | ------- | ----- |
    | Tarification et niveau de mise à l’échelle | F1 gratuit. Vous ne pouvez avoir qu’un seul hub de niveau gratuit dans un abonnement. |
    | Unités IoT Hub | 1 |

    ![Paramètres du hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Cliquez sur **Créer**. La création du hub peut prendre plusieurs minutes.

    ![Paramètres du hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Notez le nom de hub IoT choisi. Vous utiliserez cette valeur plus loin dans le didacticiel.