---
title: Générer une chaîne de connexion d’appareil pour Azure IoT Central | Microsoft Docs
description: En tant que périphérique développeur, comment générer une chaîne de connexion pour l’appareil qui doit se connecter à une application IoT Central ?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426704"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Générer une chaîne de connexion d’appareil pour se connecter à une application Azure IoT Central

Cet article décrit comment, en tant que périphérique développeur, pour générer une chaîne de connexion pour l’appareil qui doit se connecter à une application IoT Central. La procédure décrite dans cet article vous montrent comment se connecter rapidement un seul appareil à l’aide d’une signature d’accès partagé (SAP). Cette approche est utile lorsque vous expérimenter IoT Central ou test d’appareils. Pour d’autres approches à utiliser dans un environnement de production, consultez [connectivité des appareils dans Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Un ordinateur de développement avec [Node.js](https://nodejs.org/) version 8.0.0 ou version ultérieure. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Les étapes suivantes décrivent comment obtenir les informations nécessaires générer une chaîne de connexion SAS pour un appareil :

1. Dans le **Explorer**, trouver le périphérique réel que vous souhaitez vous connecter à votre application :

    ![Sélectionnez un appareil réel](media/howto-generate-connection-string/real-devices.png)

1. Sur le **appareil** page, sélectionnez **Connect**:

    ![Sélectionnez se connecter](media/howto-generate-connection-string/connect.png)

1. Prenez note des détails de connexion, **ID de portée**, **ID d’appareil**, et **clé primaire appareil**, à utiliser dans les étapes suivantes :

    ![Informations de connexion](media/howto-generate-connection-string/device-connect.png)

    Vous pouvez copier les valeurs à partir de cette page pour enregistrer.

## <a name="generate-the-connection-string"></a>Générer la chaîne de connexion

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez généré une chaîne de connexion pour un périphérique réel pour vous connecter à votre application Azure IoT Central, voici les étapes suggérées :

* [Préparer et connecter un appareil DevKit (C)](howto-connect-devkit.md)
* [Préparer et connecter un Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Préparer et connecter un Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Préparer et connecter un appareil core de Windows 10 IoT (C#)](howto-connect-windowsiotcore.md)
* [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)