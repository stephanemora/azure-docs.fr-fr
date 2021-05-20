---
title: Tutoriel – Connecter une application cliente générique à Azure IoT Central | Microsoft Docs
description: Ce tutoriel vous explique comment connecter un appareil exécutant une application cliente C, C#, Java, JavaScript ou Python à votre application Azure IoT Central. Vous modifiez le modèle d’appareil généré automatiquement en ajoutant des vues qui permettent à un opérateur d’interagir avec un appareil connecté.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 0e6f4ac3cd95873c8b735a1843438499f2405bef
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108750020"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central

Ce tutoriel vous explique comment connecter une application cliente à votre application Azure IoT Central. L'application simule le comportement d'un régulateur de température. Lorsque l'application se connecte à IoT Central, elle envoie l'ID du modèle au modèle de régulateur de température. IoT Central utilise cet ID du modèle pour récupérer le modèle d’appareil et créer un modèle pour votre appareil. Vous ajoutez des personnalisations et des vues au modèle d’appareil pour permettre à un opérateur d’interagir avec un appareil.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer et exécuter le code d’appareil et veiller à ce qu’il se connecte à votre application IoT Central.
> * Afficher la télémétrie simulée envoyée à partir de l’appareil.
> * Ajouter des vues personnalisées à un modèle d’appareil.
> * Publier le modèle d’appareil.
> * Utiliser une vue pour gérer les propriétés de l’appareil.
> * Appeler une commande pour contrôler l’appareil.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Voir les données brutes

Vous pouvez utiliser la vue **Données brutes** pour examiner les données brutes que votre appareil envoie à IoT Central :

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Vue Données brutes":::

Dans cette vue, vous pouvez sélectionner les colonnes à afficher et définir une plage de temps à afficher. La colonne **Données non modélisées** affiche les données d'appareil qui ne correspondent à aucune propriété ou définition de télémétrie dans le modèle d'appareil.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous préférez suivre l’ensemble des tutoriels IoT Central et en savoir plus sur la création d’une solution IoT Central, consultez :

> [!div class="nextstepaction"]
> [Créer un modèle d’appareil de passerelle](./tutorial-define-gateway-device-type.md)
