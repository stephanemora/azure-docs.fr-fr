---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à la connectivité des appareils dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9e1e85d1ab1c5e7ce0cbd96c64137309c2e2916a
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425965"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connectivité des appareils dans Azure IoT Central

Cet article présente les concepts clés relatifs à la connectivité des appareils dans Microsoft Azure IoT Central.

Azure IoT Central utilise le [service Azure IoT Hub Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) pour gérer tous les l’inscription et connexion.

L’utilisation de points de distribution permet :

- IoT Central pour prendre en charge l’intégration et les appareils se connectant à l’échelle.
- Vous permet de générer l’appareil les informations d’identification et configurez les appareils hors connexion sans inscrire les appareils via IoT Central, l’interface utilisateur.
- Appareils de se connecter à l’aide des signatures d’accès (SAP) partagé.
- Appareils de se connecter à l’aide des certificats X.509 standard du secteur.
- Vous utilisez votre propre ID d’appareil pour inscrire des appareils dans IoT Central. À l’aide de votre propre ID d’appareil simplifie l’intégration avec les systèmes back office existants.
- Un moyen unique et homogène à connecter des appareils à IoT Central.

Cet article décrit les cas de quatre usage suivants :

1. [Se connecter rapidement un seul appareil à l’aide de SAS](#connect-a-single-device)
1. [Connecter des appareils à l’échelle à l’aide de SAS](#connect-devices-at-scale-using-sas)
1. [Connecter des appareils à l’échelle à l’aide de certificats X.509](#connect-devices-using-x509-certificates) c’est l’approche recommandée pour les environnements de production.
1. [Connecter des appareils sans les inscrire au préalable](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Connecter un appareil unique

Cette approche est utile lorsque vous expérimenter IoT Central ou test d’appareils. Vous pouvez utiliser les informations de connexion d’appareil à partir de votre application IoT Central pour générer la chaîne de connexion pour un appareil. Pour des instructions détaillées, consultez [comment générer une chaîne de connexion d’appareil pour se connecter à une application Azure IoT Central](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Connecter des appareils à l’échelle à l’aide de SAS

Pour connecter des appareils à IoT Central à l’échelle à l’aide de SAS, vous devez inscrire et puis configurer les appareils :

### <a name="register-devices-in-bulk"></a>Inscrire des appareils en bloc

Pour inscrire un grand nombre d’appareils avec votre application IoT Central, utilisez un fichier CSV à [importer les ID d’appareil et les noms de périphérique](howto-manage-devices.md#import-devices).

Pour récupérer les informations de connexion pour les appareils importés, [exporter un fichier CSV à partir de votre application IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Pour savoir comment vous pouvez connecter des appareils sans premier inscrivant dans IoT Central, consultez [se connecter sans l’enregistrement des appareils](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Configurer vos périphériques

Utilisez les informations de connexion du fichier d’exportation dans votre code de l’appareil pour permettre à vos appareils pour vous connecter et envoyer des données à IoT à votre application IoT Central. Pour plus d’informations sur la connexion des appareils, consultez [étapes suivantes](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Connecter des appareils à l’aide de certificats X.509

Dans un environnement de production, à l’aide de certificats X.509 est le mécanisme d’authentification recommandée de périphérique pour IoT Central. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](../iot-hub/iot-hub-x509ca-overview.md).

Les étapes suivantes décrivent comment connecter des appareils à IoT Central à l’aide de certificats X.509 :

1. Dans votre application IoT Central, _ajouter et vérifier le niveau intermédiaire ou racine du certificat X.509_ que vous utilisez pour générer des certificats de l’appareil :

    - Accédez à **Administration > connexion de l’appareil > certificats (X.509)** et ajoutez X.509 certificat racine ou intermédiaire que vous utilisez pour générer les certificats d’appareil de feuille.

      ![Paramètres de connexion](media/concepts-connectivity/connection-settings.png)

      Si vous avez une violation de la sécurité ou votre certificat principal est configuré pour expirer, utiliser le certificat secondaire afin de réduire les temps d’arrêt. Vous pouvez continuer à approvisionner des appareils à l’aide du certificat secondaire pendant que vous mettez à jour le certificat principal.

    - La propriété du certificat de vérification garantit que l’outil de chargement du certificat a la clé privée du certificat. Pour vérifier le certificat :
        - Sélectionnez le bouton en regard **Code de vérification** pour générer un code.
        - Créer un certificat de vérification X.509 avec le code de vérification que vous avez généré à l’étape précédente. Enregistrez le certificat en tant que fichier .cer.
        - Télécharger le certificat de vérification signé et sélectionnez **Vérifiez**.

          ![Paramètres de connexion](media/concepts-connectivity/verify-cert.png)

1. Utiliser un fichier CSV pour _importer et inscrire des appareils_ dans votre application IoT Central.

1. _Configurer vos appareils._ générez le certificat de nœud terminal avec le certificat racine téléchargé. Utilisez le **ID d’appareil** en tant que la valeur de l’enregistrement CNAME dans les certificats feuilles. L’ID de l’appareil doit être entièrement en minuscules. Programmez ensuite vos appareils avec des informations de service de configuration. Quand un appareil est allumé pour la première, il récupère ses informations de connexion pour votre application IoT Central à partir de points de distribution.

### <a name="further-reference"></a>Référence supplémentaire

- Exemple d’implémentation pour [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Exemple de client de périphérique en C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Fins de test uniquement

Fins de test uniquement, vous pouvez utiliser ces utilitaires pour générer des certificats d’autorité de certification et les certificats de l’appareil.

- Si vous utilisez un appareil DevKit, cela [outil de ligne de commande](https://aka.ms/iotcentral-docs-dicetool) génère un certificat d’autorité de certification que vous pouvez ajouter à votre application IoT Central pour vérifier les certificats.

- Utilisez cette [outil de ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour :
  - Créer une chaîne de certificats. Suivez l’étape 2 dans l’article de GitHub.
  - Enregistrer les certificats en tant que fichiers .cer à télécharger vers votre application IoT Central.
  - Le code de vérification de l’application IoT Central permet de générer le certificat de vérification. Suivez l’étape 3 dans l’article de GitHub.
  - Créer des certificats feuilles pour vos appareils à l’aide de votre ID d’appareil en tant que paramètre à l’outil. Suivez l’étape 4 dans l’article de GitHub.

## <a name="connect-without-registering-devices"></a>Se connecter sans l’inscription des appareils

Est d’un scénario clé IoT Central permet aux fabricants OEM fabriquer en série des appareils pouvant se connecter à une application IoT Central sans d’abord en cours d’inscription. Le fabricant doit générer des informations d’identification appropriées et configurer les appareils dans la fabrique. Quand un appareil active pour la première fois, il se connecte automatiquement à une application IoT Central. Un opérateur IoT Central doit approuver l’appareil pour pouvoir envoyer des données statistiques.

Le diagramme suivant illustre ce flux :

![Paramètres de connexion](media/concepts-connectivity/device-connection-flow.png)

Les étapes suivantes décrivent ce processus plus en détail. Les étapes diffèrent légèrement selon si vous utilisez des certificats de signature d’accès partagé ou X.509 pour l’authentification des appareils :

1. Configurer vos paramètres de connexion :

    - **Certificats X.509 :** [Ajouter et vérifier le certificat racine/intermédiaire](#connect-devices-using-x509-certificates) et l’utiliser pour générer les certificats de périphérique à l’étape suivante.
    - **SAP :** Copiez la clé primaire. Cette clé est la clé SAS de groupe pour l’application IoT Central. Utilisez la clé pour générer les clés de signature d’accès partagé de périphérique à l’étape suivante.
    ![Paramètres de connexion SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Générer vos informations d’identification de l’appareil
    - **Certificats X.509 :** Générer les certificats feuilles pour vos appareils à l’aide du certificat racine ou intermédiaire que vous avez ajouté à votre application IoT Central. Vérifiez que vous utilisez les minuscules **ID d’appareil** en tant que l’enregistrement CNAME dans les certificats feuilles. Pour vous à des fins de tests uniquement, utilisent [outil de ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour générer des certificats de l’appareil.
    - **SAP :** Utilisez cette [outil de ligne de commande](https://www.npmjs.com/package/dps-keygen) pour générer des clés de signature d’accès partagé d’appareil. Utilisez le groupe **clé primaire** à partir de l’étape précédente. L’ID de périphérique doivent être en minuscule.

      Pour installer le [utilitaire du Générateur de clé](https://github.com/Azure/dps-keygen), exécutez la commande suivante :

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Pour générer une clé d’appareil à partir de la clé primaire de SAP de groupe, exécutez la commande suivante :

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Pour configurer vos appareils, flash chaque appareil avec le **ID de portée**, **ID d’appareil**, et **certificat d’appareil X.509** ou **clé SAS**.

1. Allumez l’appareil pour qu’il se connecte à votre application IoT Central. Lorsque vous basculez sur un appareil, il se connecte d’abord à points de distribution pour récupérer les informations d’enregistrement de IoT Central.

1. L’appareil connecté s’affiche initialement en tant qu’un **appareil non associée** sur le **Device Explorer** page. L’état d’approvisionnement de l’appareil est **Inscrit**. **Associer** l’appareil pour le modèle de périphérique approprié et approuver l’appareil pour se connecter à votre application IoT Central. L’appareil peut ensuite extraire une chaîne de connexion IoT Hub et commencer à envoyer des données. Provisionnement des appareils est désormais terminé et l’état de configuration est maintenant **approvisionné**.

## <a name="provisioning-status"></a>État de mise en service

Quand un appareil réel se connecte à votre application IoT Central, les modifications apportées à son état approvisionnement comme suit :

1. L’état de provisionnement des appareils est d’abord **Registered**. Cet état signifie que l’appareil est créé dans IoT Central et a un ID d’appareil. Un appareil est inscrit lorsque :
    - Un nouvel appareil réel est ajouté sur le **Device Explorer** page.
    - Un ensemble d’appareils est ajouté à l’aide de **importation** sur le **Device Explorer** page.
    - Un appareil n’a pas été inscrit manuellement dans le **Device Explorer** page, mais il est connecté avec les informations d’identification valides et est visible en tant qu’un **Unassociated** appareil sur le **Device Explorer**page.

1. Modifications de l’état de provisionnement des appareils à **approvisionné** lorsque l’appareil connecté à votre application IoT Central avec les informations d’identification valides termine à l’étape de provisionnement. Dans cette étape, l’appareil récupère une chaîne de connexion IoT Hub. L’appareil peut maintenant se connecter à IoT Hub et commencer à envoyer des données.

1. Un opérateur peut bloquer un appareil. Quand un appareil est bloqué, il ne peut pas envoyer les données à votre application IoT Central. Bloqué appareils ont un état de configuration de **bloqué**. Un opérateur doit réinitialiser l’appareil avant qu’il peut reprendre l’envoi de données. Quand un opérateur débloque un appareil de l’état d’approvisionnement retourne à sa valeur précédente, **Registered** ou **approvisionné**.

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

L’offre de kits de développement logiciel Azure appareil le moyen le plus simple pour vous implémentez votre code de l’appareil. Les kits SDK d’appareils suivants sont disponibles :

- [SDK Azure IoT pour C](https://github.com/azure/azure-iot-sdk-c)
- [SDK Azure IoT pour Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK Azure IoT pour Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK Azure IoT pour Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK Azure IoT pour .NET](https://github.com/azure/azure-iot-sdk-csharp)

Chaque appareil se connecte en utilisant une chaîne de connexion unique qui l’identifie. Un appareil peut uniquement se connecter au hub IoT où il est enregistré. Lorsque vous créez un périphérique réel dans votre application Azure IoT Central, l’application génère les informations nécessaires construire une chaîne de connexion à l’aide de `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Fonctionnalités du SDK et connectivité d’IoT Hub

Toutes les communications des appareils avec IoT Hub utilisent les options de connectivité IoT Hub suivantes :

- [Messages d’appareil-à-cloud](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Jumeaux d’appareil](../iot-hub/iot-hub-devguide-device-twins.md)

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

Maintenant que vous avez découvert la connectivité des appareils dans Azure IoT Central, voici les étapes suggérées :

- [Préparer et connecter un appareil DevKit](howto-connect-devkit.md)
- [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
- [KIT DE DÉVELOPPEMENT LOGICIEL C : Approvisionnement d’appareil Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
