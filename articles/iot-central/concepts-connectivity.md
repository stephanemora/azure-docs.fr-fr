---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à la connectivité des appareils dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 533327ca1d202f15376e580d20317ddaf0b1d302
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693192"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connectivité des appareils dans Azure IoT Central

Cet article présente les concepts clés relatifs à la connectivité des appareils dans Microsoft Azure IoT Central.

Azure IoT Central utilise le [service Azure IoT Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) pour gérer toutes les inscriptions et connexions d’appareils.

L’utilisation de DPS permet :

- à IoT Central de prendre en charge l’intégration et la connexion des appareils à l’échelle ;
- aux clients de générer des informations d’identification d’appareils et de les configurer hors ligne sans avoir à les inscrire via l’interface utilisateur d’IoT Central ;
- aux appareils de se connecter avec les signatures d’accès partagé (SAP) ;
- aux appareils de se connecter avec des certificats X.509 standard du secteur ;
- aux clients d’utiliser leurs propres ID d’appareils pour inscrire des appareils dans IoT Central. L’utilisation de vos propres ID d’appareils simplifie l’intégration dans des systèmes back-office existants.
- C’est une manière cohérente de connecter des appareils à IoT Central.

Cet article décrit les quatre cas d’usage suivants :

1. [Connecter rapidement un seul appareil avec la SAS](#connect-a-single-device)
1. [Connecter des appareils à l’échelle avec la SAS](#connect-devices-at-scale-using-sas)
1. [Connecter des appareils à l’échelle avec des certificats X.509](#connect-devices-using-x509-certificates), l’approche recommandée pour les environnements de production.
1. [Connecter des appareils sans les inscrire au préalable](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Connecter un appareil unique

Cette approche est pratique quand vous faites des essais avec IoT Central ou quand vous testez des appareils. Vous pouvez utiliser les informations de connexion des appareils à partir de votre application IoT Central afin de générer la chaîne de connexion pour un appareil. Pour voir le détail des étapes, consultez [Comment générer une chaîne de connexion d’appareil pour se connecter à une application Azure IoT Central](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Connecter des appareils à l’échelle avec la SAS

Pour connecter des appareils à IoT Central à l’échelle avec la SAS, vous devez inscrire puis configurer les appareils :

### <a name="register-devices-in-bulk"></a>Inscrire des appareils en bloc

Pour inscrire un grand nombre d’appareils avec votre application IoT Central, utilisez un fichier CSV pour [importer les ID et les noms des appareils](howto-manage-devices.md#import-devices).

Pour récupérer les informations de connexion des appareils importés, [exportez un fichier CSV à partir de votre application IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Pour savoir comment connecter des appareils sans les inscrire d’abord dans IoT Central, consultez [Se connecter sans enregistrer des appareils](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Configurer vos appareils

Utilisez les informations de connexion du fichier d’exportation dans le code de votre appareil pour permettre à vos appareils de se connecter et d’envoyer des données IoT à votre application IoT Central. Pour plus d’informations sur la connexion d’appareils, consultez [Étapes suivantes](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Connecter des appareils avec des certificats X.509

Dans un environnement de production, l’utilisation de certificats X.509 est le mécanisme d’authentification des appareils recommandé pour IoT Central. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md).

Les étapes suivantes décrivent comment connecter des appareils à IoT Central à l’aide de certificats X.509 :

1. Dans votre application IoT Central, _ajoutez et vérifiez le certificat X.509 intermédiaire ou racine_ que vous utilisez pour générer des certificats d’appareils :

    - Accédez à **Administration > Connexion d’appareils > Certificats (X.509)** et ajoutez le certificat X.509 racine ou intermédiaire que vous utilisez pour générer les certificats de nœud terminal.

      ![Paramètres de connexion](media/concepts-connectivity/connection-settings.png)

      En cas de violation de la sécurité ou si votre certificat principal arrive à expiration, utilisez le certificat secondaire pour réduire le temps d’arrêt. Vous pouvez continuer à approvisionner des appareils à l’aide du certificat secondaire pendant que vous mettez à jour le certificat principal.

    - la vérification de la propriété du certificat permet de s’assurer que l’utilisateur qui a chargé le certificat a la clé privée associée. Pour vérifier le certificat :
        - Sélectionnez le bouton en regard **Code de vérification** pour créer un code.
        - Créez un certificat de vérification X.509 avec le code de vérification généré à l’étape précédente. Enregistrez le certificat en tant que fichier .cer.
        - Chargez le certificat de vérification signé et sélectionnez **Vérifier**.

          ![Paramètres de connexion](media/concepts-connectivity/verify-cert.png)

1. Utilisez un fichier CSV pour _importer et inscrire des appareils_ dans votre application IoT Central.

1. _Configurez vos appareils._ générez le certificat de nœud terminal avec le certificat racine téléchargé. Utilisez l’**ID d’appareil** en tant que valeur CNAME dans les certificats de nœud terminal. L’ID de l’appareil doit être en minuscules. Programmez ensuite vos appareils avec des informations de service d’approvisionnement. Quand un appareil est allumé pour la première fois, il récupère ses informations de connexion pour votre application IoT Central dans DPS.

### <a name="further-reference"></a>Référence supplémentaire

- Exemple d’implémentation pour [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Exemple de client d’appareil dans C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Uniquement à des fins de test

À des fins de test uniquement, vous pouvez vous servir de ces utilitaires pour générer des certificats d’autorité de certification et des certificats d’appareils.

- Si vous utilisez un appareil DevKit, cet [outil en ligne de commande](https://aka.ms/iotcentral-docs-dicetool) génère un certificat d’autorité de certification que vous pouvez ajouter à votre application IoT Central pour vérifier les certificats.

- Utilisez cet [outil en ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour :
  - Créer une chaîne de certificat. Suivez l’étape 2 dans l’article sur GitHub.
  - Enregistrez les certificats en tant que fichiers .cer à télécharger vers votre application IoT Central.
  - Le code de vérification de l’application IoT Central permet de générer le certificat de vérification. Suivez l’étape 3 dans l’article sur GitHub.
  - Créez des certificats de nœud terminal pour vos appareils à l’aide de vos ID d’appareils en tant que paramètre de l’outil. Suivez l’étape 4 dans l’article sur GitHub.

## <a name="connect-without-registering-devices"></a>Se connecter sans inscrire les appareils

Un scénario clé d’IoT Central permet aux OEM de fabriquer en série des appareils pouvant se connecter à une application IoT Central sans inscription préalable. Un fabricant doit générer des informations d’identification appropriées et configurer les appareils en usine. Quand un appareil est activé pour la première fois, il se connecte automatiquement à une application IoT Central. Un opérateur IoT Central doit approuver l’appareil avant que ce dernier puisse commencer à envoyer des données.

Le diagramme suivant retrace ce flux :

![Paramètres de connexion](media/concepts-connectivity/device-connection-flow1.png)

Les étapes suivantes décrivent ce processus plus en détail. Les étapes diffèrent légèrement selon que vous utilisez des certificats de signature d’accès partagé ou X.509 pour authentifier les appareils :

1. Configurez vos paramètres de connexion :

    - **Certificats X.509 :** [ajoutez et vérifiez le certificat intermédiaire/racine](#connect-devices-using-x509-certificates) et utilisez-le pour générer les certificats d’appareil lors de l’étape suivante.
    - **SAP :** copiez la clé primaire. Cette clé est la clé SAS de groupe pour l’application IoT Central. Utilisez la clé pour générer les clés de signature d’accès partagé de l’appareil à l’étape suivante.
    ![Paramètres de connexion SAP](media/concepts-connectivity/connection-settings-sas.png)

1. Générer les informations d’identification de vos appareils
    - **Certificats X.509 :** générez les certificats de nœud terminal pour vos appareils avec le certificat intermédiaire ou racine que vous avez ajouté à votre application IoT Central. Veillez à utiliser l’**ID de l’appareil** en minuscules comme CNAME dans les certificats de nœud terminal. À des fins de test uniquement, utilisez cet [outil en ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour générer des certificats d’appareils.
    - **SAP :** utilisez cette [outil en ligne de commande](https://www.npmjs.com/package/dps-keygen) pour générer des clés de signature d’accès partagé d’appareils. Utilisez le groupe **Clé primaire** de l’étape précédente. L’ID de l’appareil doit être en minuscules.

      Pour installer l’[utilitaire de génération de clés](https://github.com/Azure/dps-keygen), exécutez la commande suivante :

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Pour générer une clé d’appareil à partir de la clé primaire de SAS de groupe, exécutez la commande suivante :

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Pour configurer vos appareils, affichez chaque appareil avec l’**ID de l’étendue**, l’**ID de l’appareil** et le **certificat d’appareil X.509** ou la **clé SAS**.

1. Allumez ensuite l’appareil pour qu’il se connecte à votre application IoT Central. Lorsque vous basculez sur un appareil, il se connecte d’abord à DPS pour récupérer ses informations d’inscription à IoT Central.

1. L’appareil connecté s’affiche initialement en tant qu’**appareil non associé** sur la page **Device Explorer**. L’état d’approvisionnement de l’appareil est **Inscrit**. **Associez** l’appareil au modèle d’appareil approprié et autorisez-le à se connecter à votre application IoT Central. L’appareil peut ensuite récupérer une chaîne de connexion IoT Hub et commencer à envoyer des données. L’approvisionnement de l’appareil est maintenant terminée et l’état d’approvisionnement est maintenant **Approvisionné**.

## <a name="provisioning-status"></a>État d’approvisionnement

Quand un appareil réel se connecte à votre application IoT Central, son état approvisionnement change comme suit :

1. L’état d’approvisionnement de l’appareil est d’abord **Inscrit**. Cet état signifie que l’appareil est créé dans IoT Central et a une identité d'appareil. Un appareil est inscrit quand :
    - un nouvel appareil réel est ajouté à la page **Device Explorer** ;
    - un ensemble d’appareils est ajouté avec **Importer** à la page **Device Explorer** ;
    - un appareil n’a pas été inscrit manuellement à la page **Device Explorer**, mais est connecté avec des informations d’identification valides et visible en tant qu’appareil **Non associé** à la page **Device Explorer**.

1. L’état d’approvisionnement passe à **Approvisionné** lorsque l’appareil connecté à votre application IoT Central avec des informations d’identification valides termine l’étape de provisionnement. Dans cette étape, l’appareil récupère une chaîne de connexion d’IoT Hub. L’appareil peut maintenant se connecter à IoT Hub et commencer à envoyer des données.

1. Un opérateur peut bloquer un appareil. Quand un appareil est bloqué, il ne peut pas envoyer de données à votre application IoT Central. Les appareils bloqués ont un état d’approvisionnement **Bloqué**. Un opérateur doit réinitialiser l’appareil avant que ce dernier puisse recommencer à envoyer des données. Quand un opérateur débloque un appareil, l’état d’approvisionnement revient à sa valeur précédente, à savoir **Inscrit** ou **Approvisionné**.

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

Le kit de développement logiciel (SDK) Azure Device est le moyen le plus simple pour vous d’implémenter votre code d’appareil. Les kits SDK d’appareils suivants sont disponibles :

- [SDK Azure IoT pour C](https://github.com/azure/azure-iot-sdk-c)
- [SDK Azure IoT pour Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK Azure IoT pour Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK Azure IoT pour Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK Azure IoT pour .NET](https://github.com/azure/azure-iot-sdk-csharp)

Chaque appareil se connecte en utilisant une chaîne de connexion unique qui l’identifie. Un appareil ne peut se connecter qu’au hub IoT où il est inscrit. Quand vous créez un appareil réel dans votre application Azure IoT Central, l’application génère les informations dont vous avez besoin pour construire une chaîne de connexion avec `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Fonctionnalités du SDK et connectivité d’IoT Hub

Toutes les communications des appareils avec IoT Hub utilisent les options de connectivité IoT Hub suivantes :

- [Messages d’appareil-à-cloud](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Représentations d’appareil physique](../iot-hub/iot-hub-devguide-device-twins.md)

Le tableau suivant récapitule à quelles fonctionnalités des appareils Azure IoT Central correspondent les fonctionnalités IoT Hub :

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mesure : Télémétrie | Messages d’appareil-à-cloud |
| Propriétés de l’appareil | Propriétés signalées du jumeau d’appareil |
| Paramètres | Propriétés souhaitées et signalées du jumeau d’appareil |

Pour plus d’informations sur l’utilisation des kits SDK d’appareil, consultez les articles suivants pour obtenir des exemples de code :

- [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
- [Connecter un appareil Raspberry Pi à votre application Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Connecter un appareil du kit DevDiv à votre application Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protocoles

Les kits SDK d’appareil prennent en charge les protocoles réseau suivants pour la connexion à un hub IoT :

- MQTT
- AMQP
- HTTPS

Pour plus d’informations sur les différences entre ces protocoles et des conseils sur le choix de l’un d’eux, consultez [Choisir un protocole de communication](../iot-hub/iot-hub-devguide-protocols.md).

Si votre appareil ne peut utiliser aucun des protocoles pris en charge, vous pouvez utiliser Azure IoT Edge pour faire une conversion de protocole. IoT Edge prend en charge d’autres scénarios avancés permettant de décharger l’application Azure IoT Central de certains traitements.

## <a name="security"></a>Sécurité

Toutes les données échangées entre les appareils et votre application Azure IoT Central sont chiffrées. IoT Hub authentifie chaque demande provenant d’un appareil qui se connecte aux points de terminaison IoT Hub exposés aux appareils. Pour éviter d’échanger des informations d’identification sur le réseau, un appareil utilise des jetons signés pour s’authentifier. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la connectivité des appareils dans Azure IoT Central, voici les étapes suivantes suggérées :

- [Préparer et connecter un appareil DevKit](howto-connect-devkit.md)
- [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
- [Kit de développement logiciel (SDK) C : Kit de développement logiciel (SDK) client d’appareil d’approvisionnement](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
