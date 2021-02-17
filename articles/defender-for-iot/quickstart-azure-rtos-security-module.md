---
title: 'Démarrage rapide : Configurer et activer le module de sécurité pour Azure RTOS'
description: Découvrez comment intégrer et activer le module de sécurité pour le service Azure RTOS dans votre hub Azure IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 3054981bbbff45666297399033663d1830ad9e4e
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820430"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Démarrage rapide : Module de sécurité pour Azure RTOS (préversion)

Cet article décrit les prérequis avant de commencer et explique comment activer le module de sécurité pour le service Azure RTOS sur un hub IoT. Si vous n’avez pas de hub IoT, pour commencer, voir [Créer un hub IoT à l’aide du portail Azure](../iot-hub/iot-hub-create-through-portal.md).

## <a name="prerequisites"></a>Prérequis 

### <a name="supported-devices"></a>Appareils pris en charge

- Kit de découverte ST STM32F746G
- NXP i.MX RT1060 EVK
- Puce SAM E54 Xplained Pro EVK

Téléchargez, compilez et exécutez l’un des fichiers .zip pour le tableau et l’outil spécifiques (IAR, IDE semi ou PC) de votre choix à partir du [module de sécurité pour la ressource GitHub Azure RTOS](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Ressources Azure

La phase suivante de la mise en route consiste à préparer vos ressources Azure. Vous avez besoin d’un hub IoT et nous suggérons un espace de travail Log Analytics. Pour IoT Hub, vous aurez besoin d’une chaîne de connexion IoT Hub pour vous connecter à votre appareil. 
  
### <a name="iot-hub-connection"></a>Connexion au hub IoT

Une connexion au hub IoT est nécessaire pour commencer. 

1. Ouvrez votre **hub IoT** dans le portail Azure.

1. Accédez à **Appareils IoT**.

1. Sélectionnez **Create** (Créer).

1. Copiez la chaîne de connexion IoT dans le [fichier de configuration](how-to-azure-rtos-security-module.md).

Les informations d’identification de connexion sont extraites des paramètres **HOST_NAME**, **DEVICE_ID** et **DEVICE_SYMMETRIC_KEY** de la configuration de l’application utilisateur.

Le module de sécurité pour Azure RTOS utilise des connexions middleware Azure IoT basées sur le protocole **MQTT**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour terminer la configuration et la personnalisation de votre solution.

> [!div class="nextstepaction"]
> [Configurer le module de sécurité pour Azure RTOS](how-to-azure-rtos-security-module.md)