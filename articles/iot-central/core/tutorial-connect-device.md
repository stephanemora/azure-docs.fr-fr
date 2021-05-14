---
title: Tutoriel – Connecter une application cliente générique à Azure IoT Central | Microsoft Docs
description: Ce tutoriel vous explique comment, en tant que développeur d’appareils, connecter un appareil exécutant une application cliente C, C#, Java, JavaScript ou Python à votre application Azure IoT Central. Vous modifiez le modèle d’appareil généré automatiquement en ajoutant des vues qui permettent à un opérateur d’interagir avec un appareil connecté.
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
ms.openlocfilehash: 479c12ca00614b2d34bd08f41e3451826a253035
ms.sourcegitcommit: 12f15775e64e7a10a5daebcc52154370f3e6fa0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2021
ms.locfileid: "108001810"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central

*Cet article s’applique aux créateurs de solutions et aux développeurs d’appareils.*

Ce tutoriel vous explique comment, en tant que développeur d’appareils, connecter une application cliente à votre application Azure IoT Central. L'application simule le comportement d'un régulateur de température. Lorsque l'application se connecte à IoT Central, elle envoie l'ID du modèle au modèle de régulateur de température. IoT Central utilise cet ID du modèle pour récupérer le modèle d’appareil et créer un modèle pour votre appareil. Vous ajoutez des personnalisations et des vues au modèle d’appareil pour permettre à un opérateur d’interagir avec un appareil.

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

En tant que développeur d’appareils, vous pouvez utiliser la vue **Données brutes** pour examiner les données brutes que votre appareil envoie à IoT Central :

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Vue Données brutes":::

Dans cette vue, vous pouvez sélectionner les colonnes à afficher et définir une plage de temps à afficher. La colonne **Données non modélisées** affiche les données d'appareil qui ne correspondent à aucune propriété ou définition de télémétrie dans le modèle d'appareil.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous préférez suivre l’ensemble des tutoriels IoT Central et en savoir plus sur la création d’une solution IoT Central, consultez :

> [!div class="nextstepaction"]
> [Créer un modèle d’appareil de passerelle](./tutorial-define-gateway-device-type.md)

En tant que développeur d’appareils, maintenant que vous avez appris les bases de la création d’un appareil, voici les prochaines étapes suggérées :

* Pour en savoir plus sur le rôle des modèles d’appareils quand vous implémentez votre code d’appareil, consultez [Présentation des modèles d’appareils](./concepts-device-templates.md).
* Lisez [Se connecter à Azure IoT Central](./concepts-get-connected.md) pour en savoir plus sur la façon d’inscrire des appareils auprès d’IoT Central et sur la manière dont IoT Central sécurise les connexions des appareils.
* Lisez [Charges utiles de télémétrie, de propriétés et de commandes](concepts-telemetry-properties-commands.md) pour en savoir plus sur les données échangées par l’appareil avec IoT Central.
* Lisez le [guide du développeur d’appareils IoT Plug-and-Play](../../iot-pnp/concepts-developer-guide-device.md).
