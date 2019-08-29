---
title: Utiliser des groupes d’appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, comment utiliser des groupes d’appareils dans votre application Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878981"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Utiliser des groupes d’appareils dans votre application Azure IoT Central (fonctionnalités de préversion)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Cet article décrit comment, en tant qu’opérateur, utiliser des groupes d’appareils dans votre application Azure IoT Central.

Un groupe d’appareils est une liste d’appareils qui sont regroupés, car ils correspondent à certains critères spécifiés. Les groupes d’appareils vous permettent de gérer, de visualiser et d’analyser des appareils à grande échelle en regroupant les appareils dans des groupes logiques plus petits. Par exemple, vous pouvez créer un groupe d’appareils répertoriant tous les appareils de climatisation à Seattle pour permettre à un technicien de rechercher les appareils dont il est responsable. Cet article vous explique comment créer et configurer des groupes d’appareils.

## <a name="create-a-device-group"></a>Créer un groupe d’appareils

Pour créer un groupe d’appareils :

1. Choisissez **Groupes d’appareils** dans le menu de navigation gauche.

1. Sélectionnez **+Nouveau**.

    ![Nouveau groupe d’appareils](media/howto-use-device-groups-pnp/image1.png)

1. Donnez à votre groupe d’appareils un nom unique dans toute l’application. Vous pouvez également ajouter une description. Un groupe d’appareils peut contenir seulement des appareils d’un même modèle d’appareil. Choisissez le modèle à utiliser pour ce groupe.

1. Créez la requête permettant d’identifier les appareils pour le groupe d’appareils en sélectionnant une propriété, un opérateur de comparaison et une valeur. Vous pouvez ajouter plusieurs requêtes, et les appareils qui répondent à **tous** les critères sont placés dans le groupe d’appareils. Le groupe d’appareils que vous créez est accessible à toute personne ayant accès à l’application : toutes ces personnes peuvent donc voir, modifier ou supprimer le groupe d’appareils.

    ![Requête de groupe d’appareils](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > Le groupe d’appareils est une requête dynamique. Chaque fois que vous visualisez la liste des appareils, des appareils différents peuvent figurer dans la liste. La liste varie selon les appareils qui répondent actuellement aux critères de la requête.

1. Choisissez **Enregistrer**.

## <a name="analytics"></a>Analytics

L’analyse des groupes d’appareils est identique à celle de l’onglet d’analytique principal dans le menu de navigation gauche. Pour plus d’informations sur l’analytique, consultez [Comment créer une analytique](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des groupes d’appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
