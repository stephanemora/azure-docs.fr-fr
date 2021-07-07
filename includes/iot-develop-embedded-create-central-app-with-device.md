---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 8b48aacf29666536a360af34b5cbc0f6a2dcf0f2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111993490"
---
## <a name="create-the-cloud-components"></a>Créer les composants cloud

### <a name="create-the-iot-central-application"></a>Créer l’application IoT Central

Il existe plusieurs méthodes pour connecter des appareils à Azure IoT. Dans cette section, vous allez apprendre à connecter un appareil à l’aide d’Azure IoT Central. IoT Central est une plateforme d’application IoT qui réduit le coût et la complexité de la création et de la gestion de solutions IoT.

Pour créer une application :
1. Dans le [portail Azure IoT Central](https://apps.azureiotcentral.com/), sélectionnez **Build** dans le menu de navigation latéral.

    > [!NOTE]
    > Si vous disposez d’une application IoT Central existante, vous pouvez l’utiliser pour exécuter la procédure de cet article au lieu de créer une application. Dans ce cas, nous vous recommandons de créer un appareil, ou de supprimer et de recréer l’appareil si vous souhaitez utiliser un ID d’appareil existant.

1. Sélectionnez **Créer une application** dans la vignette **Application personnalisée**.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-select-custom.png" alt-text="Capture d’écran de la création d’une application Azure IoT Central personnalisée":::

1. Ajoutez le nom de l’application et une URL.
1. Choisissez le plan de tarification **Gratuit** pour activer une version d’évaluation de 7 jours.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-custom.png" alt-text="Capture d’écran de la saisie d’informations pour la nouvelle application dans Azure IoT Central":::

1. Sélectionnez **Create** (Créer). Après avoir approvisionné l’application, IoT Central vous redirige automatiquement vers le tableau de bord de la nouvelle application.


### <a name="create-a-new-device"></a>Créer un appareil

Dans cette section, vous utilisez le tableau de bord de l’application IoT Central pour créer un appareil. Vous allez utiliser les informations de connexion de l’appareil que vous venez de créer pour connecter votre appareil physique en toute sécurité dans une section ultérieure.

Pour créer un appareil :
1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Sélectionnez **Créer un appareil** dans le volet **Tous les appareils** pour ouvrir la fenêtre **Créer un appareil**. (Si vous réutilisez une application existante qui possède déjà au moins un appareil, sélectionnez **+ Nouveau** pour ouvrir la fenêtre.)
1. Laissez Modèle d’appareil défini sur **Non attribué**.
1. Renseignez le nom de l’appareil et l’ID de l’appareil souhaités.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-device.png" alt-text="Capture d’écran de la saisie d’informations pour un nouvel appareil dans Azure IoT Central":::

1. Cliquez sur le bouton **Créer**.
1. L’appareil que vous venez de créer s’affiche dans la liste **Tous les appareils**.  Sélectionnez sur le nom de l’appareil pour afficher les détails.
1. Sélectionnez **Connecter** dans la barre de menus en haut à droite pour afficher les informations de connexion utilisées pour configurer l’appareil dans la section suivante.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-device-connection-info.png" alt-text="Capture d’écran des détails de connexion de l’appareil dans Azure IoT Central":::

1. Notez les valeurs de connexion pour les paramètres de chaîne de connexion suivants affichés dans la boîte de dialogue **Connexion**. Vous allez ajouter ces valeurs à un fichier de configuration à l’étape suivante :

    * `ID scope`
    * `Device ID`
    * `Primary key`