---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à la connectivité des appareils dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: ae57fc5366e1ed99febcd9a9d08e7f95f3bbf196
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487351"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connectivité des appareils dans Azure IoT Central

Cet article présente les concepts clés relatifs à la connectivité des appareils dans Microsoft Azure IoT Central.

Azure IoT Central utilise le [service Azure IoT Hub Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps), ce qui lui permet de prendre en charge l’intégration et la connexion d’appareils à l’échelle.

-   Les clients peuvent maintenant générer des informations d’identification d’appareils et configurer ces appareils hors ligne sans avoir à les inscrire dans IoT Central en premier lieu
-   IoT Central prend en charge la connexion de l’appareil avec la connectivité recommandée X509, tout en continuant à prendre en charge et à améliorer la connectivité SAP (signatures d’accès partagé)
-   Les clients IoT Central peuvent maintenant apporter leurs propres ID d’appareil pour inscrire des appareils dans IoT Central, ce qui permet une intégration simple avec des systèmes back-office existants.
-   Il y a une façon cohérente pour connecter des appareils à IoT Central 

>[!NOTE]
>IoT Central utilise le service Azure IoT Device Provisioning (DPS) lors de toutes les inscriptions et connexions d’appareils, [en savoir plus sur le service DPS](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

En fonction de votre cas d’usage, suivez les instructions pour connecter vos appareils à IoT Central
1. [Connecter un appareil unique rapidement (avec les signatures d’accès partagé)](#connect-a-single-device)
1. [Connecter des appareils à l’échelle avec les signatures d’accès partagé (SAP)](#connect-devices-at-scale-using-shared-access-signatures)
1. [Connecter des appareils à l’échelle les certificats X509](#connect-devices-using-x509-certificates) **recommandés pour les charges de travail de production**
1. [Connecter des appareils sans les inscrire au préalable](#connect-without-first-registering-devices) 


>[!NOTE]
>Voici le point de terminaison général pour que les appareils se connectent et approvisionnent **global.azure-devices-provisioning.net**.

## <a name="connect-a-single-device"></a>Connecter un appareil unique
La connexion d’un appareil unique à IoT Central avec SAP est facile et ne nécessite que quelques étapes 
1. Ajoutez un **appareil réel** à partir de Device Explorer, cliquez sur **+ Nouveau > Réel** pour ajouter un appareil réel.
    * Entrez l’ID d’appareil **<span style="color:Red">(doit être en minuscules)</span>** ou utilisez l’ID d’appareil suggéré.
    * Entrez le nom de l’appareil ou utilisez le nom suggéré   
    ![Ajout d’un appareil](media/concepts-connectivity/add-device.png)
1. Obtenez des informations de connexion telles que **l’ID d’étendue, l’ID de l’appareil et la clé primaire** pour l’appareil ajouté en cliquant sur **Connecter** sur la page de l’appareil.
    * L’**[ID d’étendue](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)** est propre à une application IoT Central et générée par le service DPS, et utilisée pour garantir un ID d’appareil unique au sein d’une application.
    * L’**ID de l’appareil** est l’ID d’appareil propre à une application. L’appareil doit envoyer l’ID d’appareil lors de l’appel d’inscription.   
    * La **clé primaire** est un jeton SAP, généré par IoT Central pour cet appareil spécifique. 
    ![Informations de connexion](media/concepts-connectivity/device-connect.PNG)
1. Utilisez ces informations de connexion (**Identité d’appareil, Nom de l’appareil et Clé primaire de l’appareil**) dans le code de votre appareil pour l’approvisionner et le connecter, et constater l’afflux instantané des données. Si vous utilisez l’appareil MxChip, suivez [les instructions pas à pas ici](howto-connect-devkit.md#add-a-real-device). Commencez par la section **Préparer l’appareil DevKit**.   

    Voici les références pour d’autres langues à utiliser.

    *   **Langage C :** si vous utilisez C, suivez cet [exemple de client d’appareil C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) pour connecter un exemple d’appareil. Utilisez les réglages suivants dans l’exemple.   

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    *   **Node.js :**  si vous voulez utiliser Node.js, [suivez les instructions pas à pas ici](tutorial-add-device.md#prepare-the-client-code), commencez par la section **Préparer le code client**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Connecter des appareils à l’échelle avec les signatures d’accès partagé

Pour connecter des appareils à l’échelle avec IoT Central avec SAP, vous devez suivre deux étapes : 
1. **Inscrivez des appareils** en les important dans IoT Central via un fichier CSV et exportez des appareils avec les informations de connexion d’appareil à utiliser pour connecter vos appareils
1. **Configuration d’appareil** : l’appareil est programmé avec les informations de connexion (**ID d’étendue, ID de l’appareil et clé primaire**) pour qu’il puisse appeler le service de provisionnement afin d’obtenir son attribution d’application IoT Central/d’informations de connexion quand il est allumé.

>[!NOTE]
>Une option avancée est aussi disponible, vous permettant de connecter des appareils sans avoir à les inscrire dans IoT Central au préalable. [En savoir plus ici](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Inscrire des appareils**

Pour connecter un grand nombre d’appareils à votre application, Azure IoT Central offre une importation en masse des appareils via un fichier CSV. 

Exigences du fichier CSV : Le fichier CSV doit disposer des colonnes (et en-têtes) suivantes
1.  IOTC_DeviceID **<span style="color:Red">(doit être en minuscules)</span>**
1.  IOTC_DeviceName (facultatif)



Importer des appareils pour les inscrire dans votre application
1.  Choisissez **Explorer** dans le menu de navigation gauche.
1.  Dans le volet gauche, choisissez le modèle d’appareil pour lequel vous souhaitez créer des appareils en masse. 
1.  Cliquez sur **Importer**, sélectionnez le fichier CSV contenant la liste des ID d’appareil à importer.
Le fichier CSV doit disposer des colonnes (et en-têtes) suivantes
    *   IOTC_DeviceID **<span style="color:Red">(doit être en minuscules)</span>**
    *   IOTC_DeviceName (facultatif)
1.  Une fois l’importation terminée, un message de réussite s’affiche sur la grille de l’appareil.

Exportez des appareils pour recevoir des informations de connexion ; l’exportation crée un fichier CSV avec l’ID de l’appareil, le nom de l’appareil et la clé de l’appareil. Utilisez ces informations pour connecter l’appareil à IoT Central.
Pour exporter en bloc des appareils à partir de votre application :
1.  Choisissez **Explorer** dans le menu de navigation gauche.
1.  Sélectionnez les appareils que vous souhaitez exporter, puis cliquez sur l’action **Exporter**.
1.  Une fois l’exportation terminée, un message de réussite s’affiche avec un lien pour télécharger le fichier généré.
1.  Cliquez sur le message de réussite pour télécharger le fichier vers un dossier local sur le disque.
1.  Le fichier CSV exporté contient les colonnes suivantes : **ID de l’appareil, nom de l’appareil, clé primaire/secondaire de l’appareil et empreinte numérique de certificat primaire/secondaire**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Configuration de l’appareil**

Utilisez ces informations de connexion, **Identité d’appareil (IOTC_DEVICEID), Clé primaire de l’appareil (IOTC_SASKEY_PRIMARY) et ID d’étendue** dans le code de votre appareil pour l’approvisionner et le connecter. Si ce n’est pas déjà le cas, obtenez l’**ID d’étendue** depuis votre Application IoT Central **Administration >   Connexion de l’appareil > ID d’étendue**.
Si vous utilisez l’appareil **MxChip** pour vous connecter, suivez les [instructions pas à pas ici](howto-connect-devkit.md#add-a-real-device). Commencez par la section **Préparer l’appareil DevKit**.   

Voici les références pour d’autres langues à utiliser.

   *   **Langage C :** si vous utilisez C, suivez cet [exemple de client d’appareil C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) pour connecter un exemple d’appareil. Utilisez les réglages suivants dans l’exemple.   
         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```
    * **Node.js :**  si vous voulez utiliser Node.js, [suivez les instructions pas à pas ici](tutorial-add-device.md#prepare-the-client-code), commencez par la section **Préparer le code client**.


## <a name="connect-devices-using-x509-certificates"></a>Connecter des appareils avec des certificats X509

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la **production** et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance dans laquelle chaque certificat est signé par la clé privée du certificat plus élevé suivant, et ainsi de suite, pour aboutir à un certificat racine auto-signé. Il en résulte une chaîne déléguée de confiance allant du certificat racine généré par une autorité de certification (CA) racine approuvée jusqu’au certificat « feuille » d’entité finale installé sur un appareil, en passant par chaque autorité de certification intermédiaire. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Pour connecter des appareils à IoT Central avec des certificats X509, il y a trois étapes à suivre. 
1. **Configurer les paramètres de connexion** dans l’application IoT Central en ajoutant/vérifiant le certificat intermédiaire/racine X509 utilisé pour générer les certificats de l’appareil.  Il y a deux étapes pour configurer les paramètres de connexion des certificats X509.  

    *   **Ajouter le certificat intermédiaire ou racine X509** que vous utilisez pour générer les certificats d’appareil de nœud terminal. Accédez à Administration > Connexion de l’appareil > Certificats. 
    
        ![Paramètres de connexion](media/concepts-connectivity/connection-settings.PNG)
    *   **Vérification du certificat :** la vérification de la propriété du certificat permet de s’assurer que l’utilisateur qui a chargé le certificat est en possession de la clé privée associée. Pour vérifier le certificat
        *  Générez le code de vérification, cliquez sur le bouton situé à côté du champ Code de vérification pour générer le code. 
        *  Créez un certificat de vérification X.509 avec le code de vérification, enregistrez le certificat au format .cer. 
        *  Chargez le certificat de vérification signé et cliquez sur Vérifier.

        ![Paramètres de connexion](media/concepts-connectivity/verify-cert.png)
    *   **Certificat secondaire :** Pendant le cycle de vie de votre solution IoT, vous devrez renouveler des certificats. Deux des principales raisons pour lesquelles il convient de renouveler les certificats sont les violations de la sécurité et l’expiration des certificats. Les certificats secondaires sont utilisés pour réduire les temps d’arrêt des appareils tentant de s’approvisionner pendant que vous mettez à jour le certificat primaire.

    **UNIQUEMENT À DES FINS DE TEST** 
    
    Vous trouverez ci-dessous des outils de ligne de commandes que vous pouvez utiliser pour gérer des certificats CA et d’appareil.

    * Si vous utilisez MxChip, voici un [outil de ligne de commande](https://aka.ms/iotcentral-docs-dicetool) pour générer des certificats CA. Ajoutez-le à votre application IoT Central et vérifiez les certificats. 

    *   Utilisez cet [outil de ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour
        * Créez la chaîne d’approbation (suivre l’étape 2 dans les documents GitHub). 
         Enregistrez les certificats au format .cer et chargez-les dans IoT Central (primaire).   
        * Obtenez le code de vérification depuis l’application IoT Central, générez le certificat (suivre l’étape 3 dans les documents GitHub), et chargez-le pour vérifier. 
        * Créez des certificats de nœud terminal avec l’ID de votre appareil en tant que paramètre de l’outil (suivre l’étape 4). Enregistrez le certificat et utilisez-le sur votre appareil.     

1. **Inscrivez des appareils** en les important dans IoT Central via un fichier CSV.

1. **Configuration de l’appareil** : générez le certificat de nœud terminal avec le certificat racine téléchargé. Veillez à utiliser l’**ID de l’appareil** comme CNAME dans les certificats de nœud terminal. L’ID doit être en **minuscules**. Voici un [outil de ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour générer des certificats d’appareil/de nœud terminal à des **FINS DE TEST UNIQUEMENT**.

    Programmez l’appareil avec les informations du service d’approvisionnement pour lui permettre d’obtenir ses informations de connexion et l’attribution de l’application IoT Central lorsqu’il est allumé.    

    **Référence supplémentaire** 
    *   Exemple d’implémentation pour [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)  

    *   [Exemple de client d’appareil dans C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>Utilise l’**ID de l’appareil** comme cname lorsque vous générez les certificats de nœud terminal pour les appareils.

>[!NOTE]
>L’**ID de l’appareil** doit être en minuscules 
 
## <a name="connect-without-first-registering-devices"></a>Connecter des appareils sans les inscrire au préalable
IoT Central permet aux fabricants OEM de fabriquer des appareils en masse, de générer des informations d’identification et de configurer ces appareils dans l’usine sans avoir à les inscrire au préalable dans IoT Central. Une fois les appareils allumés et connectés à IoT Central, l’opérateur autorise l’appareil à se connecter à l’application IoT Central.

Voici le flux pour connecter des appareils avec cette fonctionnalité

![Paramètres de connexion](media/concepts-connectivity/device-connection-flow.PNG)


Suivez les étapes selon votre choix de schéma d’authentification d’appareil (X509/SAP)

1. **Paramètres de connexion** 
    * **Certificats X509 :** [ajoutez et vérifiez le certificat intermédiaire/racine](#connect-devices-using-x509-certificates) et utilisez-le pour générer les certificats d’appareil dans la prochaine étape.
    * **SAP :** copiez la clé primaire (il s’agit de clé SAP de groupe de cette application IoT Central), et utilisez-la pour générer les clés SAP d’appareil dans la prochaine étape. 
![Paramètres de connexion SAP](media/concepts-connectivity/connection-settings-sas.png)

1. **Générer des informations d’identification d’appareil** 
    *   **Certificats (X.509) :** générez les certificats de nœud terminal pour vos appareils avec le certificat intermédiaire/racine que vous avez ajouté dans l’application. Veillez à utiliser l’**ID de l’appareil** comme cname dans les certificats de nœud terminal. L’ID **<span style="color:Red">doit être en minuscules</span>**. Voici un [outil de ligne de commande](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) pour générer des certificats d’appareil/de nœud terminal pour des tests.
    *   Les clés **SAP** d’appareil peuvent être générées avec cet [outil de ligne de commande](https://www.npmjs.com/package/dps-keygen). Utilisez la clé SAP primaire (clé SAP de groupe) de l’étape précédente. Veillez à ce que l’ID de l’appareil soit **<span style="color:Red">en minuscules</span>**.

        Suivez les instructions ci-dessous pour générer une clé SAP d’appareil.           

        ```
        npm i -g dps-keygen
        ```
    
        **Utilisation**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Configuration de l’appareil** 
    
     Flashez l’appareil avec l’**ID d’étendue, l’ID de l’appareil, la clé SAP/certificat d’appareil** et allumez l’appareil pour le connecter à l’application IoT Central.

1. **Connecter un appareil à IoT Central** : une fois allumés, les appareils se connectent aux services DPS/IoT Central pour l’inscription.

1. **Associer un appareil à un modèle** : l’appareil connecté s’affiche sous **UnAssociated Devices** (Appareils non associés) dans **Device Explorer**. L’état d’approvisionnement de l’appareil est **Inscrit**. **Associez** l’appareil au modèle d’appareil approprié et autorisez-le à se connecter à l’application IoT Central. L’appareil reçoit ses informations de connexion à l’application IoT Central, puis se connecte et commence à transmettre des données. L’approvisionnement de l’appareil est maintenant terminé et l’*État de l’approvisionnement* passe à **Approvisionné**.

## <a name="device-provisioning-status"></a>État de l’approvisionnement de l’appareil
Il y a une série d’étapes à suivre lorsqu’un appareil réel est connecté à Azure IoT Central 
1. **Inscrit** : l’appareil est d’abord **Inscrit**, ce qui signifie qu’il est créé dans IoT Central et dispose de son ID d’appareil.
L’appareil est inscrit quand  
    *   Un nouvel appareil réel est ajouté sur **Explorer**
    *   Un ensemble d’appareil est ajouté avec **Import** (Importer) sur **Explorer**
    *   Un appareil n’a pas été enregistré mais se connecte avec des informations d’identification valides et est visible sous les appareils **non associés**. 

    Dans tous les cas ci-dessus, l’*État de l’approvisionnement* est **Inscrit**.

1. **Approvisionné** : lorsque l’appareil se connecte avec des informations d’identification valides, IoT Central complète l’étape d’approvisionnement (en créant l’appareil dans IoT Hub). Il retourne ensuite la chaîne de connexion à l’appareil pour le connecter et commencer à transmettre des données. L’*État d’approvisionnement* de l’appareil passe maintenant de **Inscrit** à **Approvisionné**.

1.  **Bloqué** : l’opérateur peut bloquer un appareil. Une fois bloqué, il ne peut plus envoyer de données à IoT Central et doit être réinitialisé. L’*état d’approvisionnement* des appareils bloqués est **Bloqué**. L’opérateur peut aussi débloquer l’appareil. Une fois débloqué, l’*était d’approvisionnement* de l’appareil repasse à l’*état d’approvisionnement* précédent (Inscrit ou Approvisionné). 

## <a name="getting-device-connection-string"></a>Obtention de la chaîne de connexion de l’appareil
Vous pouvez obtenir la chaîne de connexion de l’appareil IoT Hub à Azure IoT Hub en suivant les étapes suivantes 
1. Obtenez des informations de connexion telles que **l’ID d’étendue, l’ID de l’appareil et la clé primaire** sur la page de l’appareil (page de l’appareil > cliquez sur Connecter). 

    ![Informations de connexion](media/concepts-connectivity/device-connect.PNG)

1. Obtenez la chaîne de connexion de l’appareil avec l’outil de ligne de commande ci-dessous.
    Suivez les instructions ci-dessous pour obtenir la chaîne de connexion d’appareil.  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Utilisation**

    Pour créer une chaîne de connexion, recherchez le fichier binaire sous le dossier bin/
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    En savoir plus sur l’[outil dps-keygen ici](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

Les kits SDK d’appareil Azure offrent le moyen le plus simple d’implémenter le code sur vos appareils qui se connectent à votre application Azure IoT Central. Les kits SDK d’appareils suivants sont disponibles :

- [SDK Azure IoT pour C](https://github.com/azure/azure-iot-sdk-c)
- [SDK Azure IoT pour Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK Azure IoT pour Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK Azure IoT pour Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK Azure IoT pour .NET](https://github.com/azure/azure-iot-sdk-csharp)

Chaque appareil se connecte en utilisant une chaîne de connexion unique qui l’identifie. Un appareil peut se connecter au hub IoT où il est inscrit. Quand vous créez un appareil réel dans votre application Azure IoT Central, l’application génère une chaîne de connexion que vous devez utiliser.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Fonctionnalités du SDK et connectivité d’IoT Hub

Toutes les communications des appareils avec IoT Hub utilisent les options de connectivité IoT Hub suivantes :

- [Messages d’appareil-à-cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Représentations d’appareil physique](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

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


## <a name="protocols"></a>Protocoles

Les kits SDK d’appareil prennent en charge les protocoles réseau suivants pour la connexion à un hub IoT :

- MQTT
- AMQP
- HTTPS

Pour plus d’informations sur les différences entre ces protocoles et des conseils sur le choix de l’un d’eux, consultez [Choisir un protocole de communication](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Si votre appareil ne peut utiliser aucun des protocoles pris en charge, vous pouvez utiliser Azure IoT Edge pour faire une conversion de protocole. IoT Edge prend en charge d’autres scénarios avancés permettant de décharger l’application Azure IoT Central de certains traitements.

## <a name="security"></a>Sécurité

Toutes les données échangées entre les appareils et votre application Azure IoT Central sont chiffrées. IoT Hub authentifie chaque demande provenant d’un appareil qui se connecte aux points de terminaison IoT Hub exposés aux appareils. Pour éviter d’échanger des informations d’identification sur le réseau, un appareil utilise des jetons signés pour s’authentifier. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Actuellement, les appareils qui se connectent à Azure IoT Central doivent utiliser des jetons SAP. Les certificats X.509 ne sont pas pris en charge pour les appareils qui se connectent à Azure IoT Central.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la connectivité des appareils dans Azure IoT Central, voici les étapes suivantes suggérées :

- [Préparer et connecter un appareil DevKit](howto-connect-devkit.md)
- [Préparer et connecter un Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connecter un client Node.js générique à votre application Azure IoT Central](howto-connect-nodejs.md)
