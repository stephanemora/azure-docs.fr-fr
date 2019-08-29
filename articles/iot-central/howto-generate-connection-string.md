---
title: Générer une chaîne de connexion d’appareil pour Azure IoT Central | Microsoft Docs
description: En tant que développeur pour des appareils, comment puis-je générer une chaîne de connexion pour un appareil qui doit se connecter à une application IoT Central ?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019774"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Générer une chaîne de connexion d’appareil pour se connecter à une application Azure IoT Central

Cet article décrit comment, en tant que développeur pour des appareils, vous pouvez générer une chaîne de connexion pour un appareil qui doit se connecter à une application IoT Central. La procédure décrite dans cet article vous montre comment connecter rapidement un seul appareil en utilisant une signature d’accès partagé. Cette approche est pratique quand vous faites des essais avec IoT Central ou quand vous testez des appareils. Pour d’autres approches à utiliser dans un environnement de production, consultez [Connectivité des appareils dans Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Une application Azure IoT Central. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
- Une machine de développement où [Node.js](https://nodejs.org/) version 8.0.0 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Les étapes suivantes décrivent comment obtenir les informations nécessaires pour générer une chaîne de connexion de signature d’accès partagé pour un appareil :

1. Dans l’**Explorateur d’appareils**, recherchez l’appareil réel que vous voulez connecter à votre application :

    ![Sélectionner un appareil réel](media/howto-generate-connection-string/real-devices.png)

1. Dans la page **Appareil**, sélectionnez **Se connecter** :

    ![Sélectionner Se connecter](media/howto-generate-connection-string/connect.png)

1. Prenez note des détails de la connexion, **ID de l’étendue**, **ID de l’appareil**, et **Clé principale de l’appareil**, que vous allez utiliser dans les étapes suivantes :

    ![Informations de connexion](media/howto-generate-connection-string/device-connect.png)

    Vous pouvez copier les valeurs à partir de cette page pour les enregistrer.

## <a name="generate-the-connection-string"></a>Générer la chaîne de connexion

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez généré une chaîne de connexion permettant à un appareil réel de se connecter à votre application Azure IoT Central, voici les prochaines étapes suggérées :

* [Préparer et connecter un appareil DevKit (C)](howto-connect-devkit.md)
* [Préparer et connecter un Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Préparer et connecter un Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Préparer et connecter un appareil Windows 10 IoT Core (C#)](howto-connect-windowsiotcore.md)
* [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)