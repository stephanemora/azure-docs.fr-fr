---
title: Meilleures pratiques de développement d’appareil dans Azure IoT Central | Microsoft Docs
description: Cet article décrit les meilleures pratiques pour la connectivité des appareils dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054083"
---
# <a name="best-practices-for-device-development"></a>Meilleures pratiques relatives pour le développement d’appareil

*Cet article s’applique aux développeurs d’appareils.*

Ces recommandations montrent comment implémenter des appareils pour tirer parti de la récupération d’urgence et de la mise à l’échelle automatique intégrée dans IoT Central.

La liste suivante présente le flux global quand un appareil se connecte à IoT Central :

1. Utilisez DPS pour approvisionner l’appareil et obtenir une chaîne de connexion d’appareil.

1. Utilisez la chaîne de connexion pour connecter le point de terminaison IoT Hub interne de IoT Central. Échangez des données avec votre application IoT Central.

1. Si l’appareil reçoit des échecs de connexion, en fonction du type d’erreur, retentez la connexion ou réapprovisionnez l’appareil.

## <a name="use-dps-to-provision-the-device"></a>Utiliser le service DPS pour approvisionner l’appareil

Pour approvisionner un appareil avec le service DPS, utilisez l’ID d’étendue, les informations d’identification et l’ID d’appareil provenant de votre application IoT Central. Pour en savoir plus sur les types d’informations d’identification, consultez [inscription de groupe X.509](concepts-get-connected.md#x509-group-enrollment) et [inscription de groupe SAP](concepts-get-connected.md#sas-group-enrollment). Pour en savoir plus sur les ID d’appareil, consultez [Inscription d’appareil](concepts-get-connected.md#device-registration).

En cas de réussite, le service DPS retourne une chaîne de connexion que l’appareil peut utiliser pour se connecter à votre application IoT Central. Pour résoudre des erreurs d’approvisionnement, consultez [Vérifier l’état d’approvisionnement de votre appareil](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

L’appareil peut mettre en cache la chaîne de connexion à utiliser pour des connexions ultérieures. Toutefois, l’appareil doit être préparé à [gérer des échecs de connexion](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Connecter à IoT Central

Utilisez la chaîne de connexion pour connecter le point de terminaison IoT Hub interne de IoT Central. La connexion vous permet d’envoyer des données de télémétrie à votre application IoT Central, de synchroniser les valeurs de propriété avec votre application IoT Central, et de répondre à des commandes envoyées par votre application IoT Central.

## <a name="handle-connection-failures"></a>Gérer les échecs de connexion

Pour la mise à l’échelle ou la récupération d’urgence, IoT Central peut mettre à jour son hub IoT sous-jacent. Pour maintenir la connectivité, le code de votre appareil doit gérer des erreurs de connexion spécifiques en établissant une connexion au nouveau point de terminaison IoT Hub.

Si l’appareil reçoit l’une des erreurs suivantes quand il se connecte, il doit recommencer l’étape d’approvisionnement avec le service DPS pour obtenir une nouvelle chaîne de connexion. Les erreurs suivantes signifient que la chaîne de connexion que l’appareil utilise n’est plus valide :

- Point de terminaison IoT Hub inaccessible.
- Jeton de sécurité périmé.
- Appareil désactivé dans IoT Hub.

Si l’appareil reçoit l’une des erreurs suivantes quand il se connecte, il doit utiliser une stratégie d’interruption pour réessayer la connexion. Ces erreurs signifient que la chaîne de connexion que l’appareil utilise est toujours valide, mais que des conditions temporaires empêchent l’appareil de se connecter :

- Appareil bloqué par l’opérateur.
- Erreur interne 500 du service.

Pour en savoir plus sur les codes d’erreur des appareils, consultez [Résolution des problèmes de connexion d’appareils](troubleshoot-connection.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes un développeur d’appareils, nous vous suggérons les étapes suivantes :

- Consultez un exemple de code montrant comment utiliser les jetons SAS dans [Tutoriel : Créer et connecter une application cliente à votre application Azure IoT Central](tutorial-connect-device.md)
- Découvrez comment [connecter des appareils avec des certificats X.509 à l’aide du kit de développement logiciel (SDK) d’appareil Node.js pour une application IoT Central](how-to-connect-devices-x509.md)
- Découvrez comment [superviser la connectivité des appareils à l’aide d’Azure CLI](./howto-monitor-devices-azure-cli.md)
- Découvrez comment [définir un nouveau type d’appareil IoT dans votre application Azure IoT Central](./howto-set-up-template.md)
- En savoir plus sur [les appareils Azure IoT Edge et Azure IoT Central](./concepts-iot-edge.md)
