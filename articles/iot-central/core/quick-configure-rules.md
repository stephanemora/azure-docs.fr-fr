---
title: Démarrage rapide – Configurer des règles et des actions dans Azure IoT Central
description: Dans le cadre de ce démarrage rapide, vous découvrez comment configurer des règles et des actions basées sur la télémétrie dans votre application IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 9357198e140a6ba403fcb74787d31a0940554fa7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459132"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Démarrage rapide : Configurer des règles et des actions pour votre appareil dans Azure IoT Central

Dans ce guide de démarrage rapide, vous créez une règle IoT Central qui envoie un e-mail quand quelqu’un met votre téléphone sous tension.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, passez en revue ce démarrage rapide [Créer et utiliser une application Azure IoT Central](./quick-deploy-iot-central.md) pour connecter une appli mobile **IoT Plug-and-Play** à votre application IoT Central.

## <a name="create-a-telemetry-based-rule"></a>Créer une règle basée sur la télémétrie

L’appli mobile envoie des données de télémétrie qui incluent des valeurs du capteur de l’accéléromètre. Lorsque le téléphone est couché sur le dos, la valeur **z** est supérieure à `9`, lorsque le téléphone est couché sur l’avant, la valeur **z** est inférieure à `-9`.

1. Pour ajouter une nouvelle règle basée sur la télémétrie à votre application, dans le volet de gauche, sélectionnez **Règles**.

1. Pour créer une nouvelle règle, sélectionnez **Créer une règle**.

1. Entez **Phone turned over** comme nom de règle.

1. Dans la section **Appareils cibles**, sélectionnez **IoT Plug-and-Play mobile** comme **modèle d’appareil**. Cette option filtre les appareils auxquels s’applique la règle par type de modèle d’appareil. Vous pouvez ajouter d’autres critères de filtre en sélectionnant **+ Filtre**.

1. Dans la section **Conditions**, vous définissez ce qui déclenche votre règle. Utilisez les informations suivantes pour définir une condition unique basée sur la télémétrie de l’axe z de l’accéléromètre. Cette règle utilise l’agrégation de sorte que vous receviez un e-mail maximum pour chaque appareil toutes les cinq minutes :

    | Champ            | Valeur            |
    |------------------|------------------|
    | Agrégation du temps | 5 minutes    |
    | Télémétrie        | Accélération/Z |
    | Opérateur         | est inférieur à     |
    | Agrégation      | Minimum          |
    | Valeur            | -9               |

    :::image type="content" source="media/quick-configure-rules/rule-target-condition.png" alt-text="Capture d’écran qui montre la condition de règle.":::

1. Pour ajouter une action d’e-mail à exécuter quand la règle se déclenche, dans la section **Action**, sélectionnez **+ E-mail**.

1. Utilisez les informations du tableau suivant pour définir votre action, puis sélectionnez **Terminé** :

    | Paramètre      | Valeur                    |
    |--------------|--------------------------|
    | Nom complet | Votre téléphone a été déplacé         |
    | À           | Votre adresse e-mail       |
    | Notes        | Votre téléphone est retourné |

    > [!TIP]
    > Pour recevoir une notification par courrier électronique, l’adresse e-mail doit être un [ID d’utilisateur dans l’application](howto-manage-users-roles.md), et l’utilisateur doit s’être connecté au moins une fois à l’application.

    :::image type="content" source="media/quick-configure-rules/rule-action.png" alt-text="Capture d’écran montrant une action d’e-mail ajoutée à la règle":::

1. Sélectionnez **Enregistrer**. Votre règle est désormais listée dans la page **Règles**.

## <a name="test-the-rule"></a>Tester la règle

Peu après l’enregistrement de la règle, elle devient active. Lorsque les conditions définies dans la règle sont remplies, IoT Central envoie un e-mail à l’adresse que vous avez spécifiée dans l’action.

Pour déclencher la règle, assurez-vous que l’appli mobile envoie des données, puis placez-votre téléphone face vers le bureau. L’application envoie à présent des valeurs de télémétrie de l’axe z de l’accéléromètre inférieures à `-9`. Au bout de cinq minutes, IoT Central vous envoie un e-mail vous informant que votre smartphone est face en bas.

Une fois le test terminé, désactivez la règle pour ne plus recevoir d’alertes dans votre boîte de réception.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

* Créer une règle basée sur la télémétrie
* Ajouter une action

Pour en savoir plus sur l’intégration de votre application IoT Central avec d’autres services, consultez :

> [!div class="nextstepaction"]
> [Exporter et traiter les données de votre application IOT central](quick-export-data.md).
