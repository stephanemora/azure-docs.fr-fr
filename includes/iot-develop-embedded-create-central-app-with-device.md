---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 3620e8021cfe5adf6226f007ea240921951ea6fe
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110061272"
---
## <a name="create-the-cloud-components"></a>Créer les composants cloud

### <a name="create-the-iot-central-application"></a>Créer l’application IoT Central

Il existe plusieurs méthodes pour connecter des appareils à Azure IoT. Dans cette section, vous allez apprendre à connecter un appareil à l’aide d’Azure IoT Central. IoT Central est une plateforme d’application IoT qui réduit le coût et la complexité de la création et de la gestion de solutions IoT.

Pour créer une application :
1. Dans le [portail Azure IoT Central](https://apps.azureiotcentral.com/), sélectionnez **Mes applications** dans le menu de navigation latéral.
1. Sélectionnez **+ Nouvelle application**.
1. Sélectionnez **Applications personnalisées**.
1. Ajoutez le nom de l’application et une URL.
1. Choisissez le plan de tarification **Gratuit** pour activer une version d’évaluation de 7 jours.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-custom.png" alt-text="Créer une application Azure IoT Central personnalisée":::

1. Sélectionnez **Create** (Créer).

    Après avoir approvisionné l’application, IoT Central vous redirige automatiquement vers le tableau de bord de la nouvelle application.

    > [!NOTE]
    > Si vous disposez d’une application IoT Central existante, vous pouvez l’utiliser pour exécuter la procédure de cet article au lieu de créer une application.

### <a name="create-a-new-device"></a>Créer un appareil

Dans cette section, vous utilisez le tableau de bord de l’application IoT Central pour créer un appareil. Vous allez utiliser les informations de connexion de l’appareil que vous venez de créer pour connecter votre appareil physique en toute sécurité dans une section ultérieure.

Pour créer un appareil :
1. Dans le tableau de bord de l’application, sélectionnez **Appareils** dans le menu de navigation latéral.
1. Sélectionnez **+ Nouveau** pour ouvrir la fenêtre **Créer un appareil**.
1. Laissez Modèle d’appareil défini sur **Non attribué**.
1. Renseignez le nom de l’appareil et l’ID de l’appareil souhaités.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-create-device.png" alt-text="Créer un appareil dans Azure IoT Central":::

1. Cliquez sur le bouton **Créer**.
1. L’appareil que vous venez de créer s’affiche dans la liste **Tous les appareils**.  Sélectionnez sur le nom de l’appareil pour afficher les détails.
1. Sélectionnez **Connecter** dans la barre de menus en haut à droite pour afficher les informations de connexion utilisées pour configurer l’appareil dans la section suivante.

    :::image type="content" source="media/iot-develop-embedded-create-central-app-with-device/iot-central-device-connection-info.png" alt-text="Consulter les détails de connexion de l’appareil":::

1. Notez les valeurs de connexion pour les paramètres de chaîne de connexion suivants affichés dans la boîte de dialogue **Connexion**. Vous allez ajouter ces valeurs à un fichier de configuration à l’étape suivante :

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`