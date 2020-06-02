---
title: Connectivité des appareils dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à la connectivité des appareils dans Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ddbb1c6fd705e658867c0d594981e87bc8cd6afe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930486"
---
# <a name="get-connected-to-azure-iot-central"></a>Se connecter à Azure IoT Central

*Cet article s’applique aux opérateurs et aux développeurs d’appareils.*

Cet article décrit les options permettant de connecter vos appareils à une application Azure IoT Central.

Généralement, vous devez inscrire un appareil dans votre application pour qu’il puisse se connecter. Cependant, IoT Central prend en charge les scénarios où les [appareils peuvent se connecter sans avoir été inscrits](#connect-without-registering-devices).

IoT Central utilise le [service Azure IoT Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) pour gérer le processus de connexion. Un appareil se connecte d’abord à un point de terminaison DPS pour récupérer les informations dont il a besoin pour se connecter à votre application. En interne, votre application IoT Central utilise un hub IoT pour gérer la connectivité des appareils. L’utilisation de DPS permet :

- à IoT Central de prendre en charge l’intégration et la connexion des appareils à l’échelle ;
- aux clients de générer des informations d’identification d’appareils et de les configurer hors ligne sans avoir à les inscrire via l’interface utilisateur d’IoT Central ;
- aux clients d’utiliser leurs propres ID d’appareils pour inscrire des appareils dans IoT Central. L’utilisation de vos propres ID d’appareils simplifie l’intégration dans des systèmes back-office existants.
- C’est une manière cohérente de connecter des appareils à IoT Central.

Pour sécuriser la communication entre un appareil et votre application, IoT Central prend en charge les signatures d’accès partagé (SAP) et les certificats X.509. Les certificats X.509 sont recommandés dans les environnements de production.

Cet article décrit les cas d’usage suivants :

- [Connecter un seul appareil avec la SAP](#connect-a-single-device)
- [Connecter des appareils à l’échelle avec la SAS](#connect-devices-at-scale-using-sas)
- [Connecter des appareils à l’échelle avec des certificats X.509](#connect-devices-using-x509-certificates) : ’approche recommandée pour les environnements de production.
- [Connecter des appareils sans les inscrire au préalable](#connect-without-registering-devices)
- [Connecter des appareils qui utilisent des inscriptions individuelles DPS](#individual-enrollment-based-device-connectivity)
- [Associer automatiquement un appareil à un modèle d’appareil](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Connecter un appareil unique

Cette approche est pratique quand vous faites des essais avec IoT Central ou quand vous testez des appareils. Vous pouvez utiliser les clés SAP de connexion de l’appareil à partir de votre application IoT Central pour connecter un appareil à votre application IoT Central. Copiez la _clé SAP de l’appareil_ à partir des informations de connexion d’un appareil inscrit :

![Clés SAP pour un appareil individuel](./media/concepts-get-connected/single-device-sas.png)

Pour en savoir plus, consultez le tutoriel [Créer et connecter une application cliente Node.js à votre application Azure IoT Central](./tutorial-connect-device-nodejs.md).

## <a name="connect-devices-at-scale-using-sas"></a>Connecter des appareils à l’échelle avec la SAS

Pour connecter des appareils à IoT Central à l’échelle avec des clés SAS, vous devez inscrire puis configurer les appareils :

### <a name="register-devices-in-bulk"></a>Inscrire des appareils en bloc

Pour inscrire un grand nombre d’appareils avec votre application IoT Central, utilisez un fichier CSV pour [importer les ID et les noms des appareils](howto-manage-devices.md#import-devices).

Pour récupérer les informations de connexion des appareils importés, [exportez un fichier CSV à partir de votre application IoT Central](howto-manage-devices.md#export-devices). Le fichier CSV exporté inclut les ID des appareils et les clés SAS.

### <a name="set-up-your-devices"></a>Configurer vos appareils

Utilisez les informations de connexion du fichier d’exportation dans le code de votre appareil pour permettre à vos appareils de se connecter et d’envoyer des données IoT à votre application IoT Central. Vous avez également besoin de l’**étendue de l’ID** de DPS pour votre application. Vous pouvez trouver cette valeur sous **Administration > Connexion de l’appareil**.

> [!NOTE]
> Pour savoir comment connecter des appareils sans les inscrire d’abord dans IoT Central, consultez [Se connecter sans enregistrer des appareils](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Connecter des appareils avec des certificats X.509

Dans un environnement de production, l’utilisation de certificats X.509 est le mécanisme d’authentification des appareils recommandé pour IoT Central. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Avant de connecter un appareil à un certificat X.509, ajoutez et vérifiez un certificat X.509 intermédiaire ou racine dans votre application. Les appareils doivent utiliser des certificats X.509 feuilles générés à partir du certificat racine ou intermédiaire.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Ajouter et vérifier un certificat racine ou intermédiaire

Accédez à **Administration > Connexion d’appareils > Gérer le certificat primaire** et ajoutez le certificat X.509 racine ou intermédiaire que vous utilisez pour générer les certificats d’appareils.

![Paramètres de connexion](media/concepts-get-connected/manage-x509-certificate.png)

La vérification de la propriété du certificat permet de s’assurer que la personne qui charge le certificat a la clé privée associée. Pour vérifier le certificat :

  1. Sélectionnez le bouton en regard **Code de vérification** pour créer un code.
  1. Créez un certificat de vérification X.509 avec le code de vérification généré à l’étape précédente. Enregistrez le certificat en tant que fichier .cer.
  1. Chargez le certificat de vérification signé et sélectionnez **Vérifier**. Le certificat est marqué comme **Vérifié** lorsque la vérification est réussie.

En cas de violation de la sécurité ou si votre certificat principal arrive à expiration, utilisez le certificat secondaire pour réduire le temps d’arrêt. Vous pouvez continuer à approvisionner des appareils à l’aide du certificat secondaire pendant que vous mettez à jour le certificat principal.

### <a name="register-and-connect-devices"></a>Inscrire et connecter des appareils

Pour connecter des appareils en bloc avec des certificats X.509, inscrivez d’abord les appareils dans votre application en utilisant un fichier CSV pour [importer les ID et les noms des appareils](howto-manage-devices.md#import-devices). Les ID d’appareils doivent tous être en minuscules.

Générez des certificats feuilles X.509 pour vos appareils à l’aide du certificat racine ou intermédiaire chargé. Utilisez l’**ID d’appareil** en tant que valeur `CNAME` dans les certificats feuilles. Le code de votre appareil requiert la valeur **Étendue d’ID** pour votre application, l’**ID d’appareil** et le certificat d’appareil correspondant.

#### <a name="sample-device-code"></a>Exemple de code d’appareil

L’exemple suivant du [kit de développement logiciel (SDK) Node.JS d’Azure IoT](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) montre comment un client de périphérique Node.js utilise un certificat leaf X.509 et une installation Microsoft System Center Data Protection Manager pour s’inscrire auprès d’une application IoT Central :

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Pour obtenir un exemple C équivalent, consultez [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) dans le [SDK Azure IoT C Provisioning Device Client](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Uniquement à des fins de test

À des fins de test uniquement, vous pouvez vous servir des utilitaires suivants pour générer des certificats racines, intermédiaires et d’appareils :

- [Outils pour le kit de développement logiciel (SDK) pour appareils Azure IoT Device Provisioning](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md) : une collection d’outils Node.js que vous pouvez utiliser pour générer et vérifier des certificats X.509 et des clés.
- Si vous utilisez un appareil DevKit, cet [outil en ligne de commande](https://aka.ms/iotcentral-docs-dicetool) génère un certificat d’autorité de certification que vous pouvez ajouter à votre application IoT Central pour vérifier les certificats.
- [Gérer les certificats d’autorité de certification de test pour les exemples et les tutoriels](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) : une collection de scripts PowerShell et Bash pour :
  - Créer une chaîne de certificat.
  - Enregistrez les certificats en tant que fichiers .cer à télécharger vers votre application IoT Central.
  - Le code de vérification de l’application IoT Central permet de générer le certificat de vérification.
  - Créez des certificats de nœud terminal pour vos appareils à l’aide de vos ID d’appareils en tant que paramètre de l’outil.

## <a name="connect-without-registering-devices"></a>Se connecter sans inscrire les appareils

Tous les scénarios décrits précédemment vous obligent à inscrire des appareils dans votre application avant qu’ils ne se connectent. IoT Central permet également aux OEM de fabriquer en masse des appareils qui peuvent se connecter sans avoir été inscrits. Un OEM génère des informations d’identification appropriées et configure les appareils en usine. Lorsqu’un client active un appareil pour la première fois, il se connecte à DPS, qui connecte à son tour automatiquement l’appareil à la bonne application IoT Central. Un opérateur IoT Central doit approuver l’appareil avant que ce dernier ne commence à envoyer des données à l’application.

Le flux est légèrement différent selon que les appareils utilisent des jetons SAS ou des certificats X.509 :

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Connecter des appareils qui utilisent des jetons SAS sans inscription

1. Copiez la clé primaire du groupe de l’application IoT Central :

    ![Clé SAS primaire du groupe de l’application](media/concepts-get-connected/group-sas-keys.png)

1. Utilisez l’outil [dps-keygen](https://www.npmjs.com/package/dps-keygen) pour générer les clés SAS des appareils. Utilisez la clé primaire de groupe de l’étape précédente. Les ID d’appareils doivent être en minuscules :

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. L’OEM dote chaque périphérique d’un ID d’appareil, d’une clé SAS d’appareil générée et de la valeur d'**étendue de l’ID** de l’application.

1. Lorsque vous basculez sur un appareil, il se connecte d’abord à DPS pour récupérer ses informations d’inscription à IoT Central.

    Initialement, l’appareil a un état **Non associé** sur la page **Appareils** et n’est pas affecté à un modèle d’appareil. Sur la page **Appareils**, **Migrez** l’appareil vers le modèle d’appareil approprié. L’approvisionnement de l’appareil est maintenant terminé, l’état de l’appareil est désormais **Approvisionné**, et l’appareil peut commencer à envoyer des données.

    Sur la page **Administration > Connexion de l’appareil**, l’option **Approbation automatique** détermine si vous devez approuver manuellement l’appareil avant qu’il puisse commencer à envoyer des données.

    > [!NOTE]
    > Pour savoir comment associer automatiquement un appareil à un modèle d’appareil, consultez [Associer automatiquement un appareil à un modèle d’appareil](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Connecter des appareils qui utilisent des certificats X.509 sans inscription

1. [Ajoutez et vérifiez un certificat X.509 racine ou intermédiaire](#connect-devices-using-x509-certificates) dans votre application IoT Central.

1. générez les certificats de nœud terminal pour vos appareils avec le certificat intermédiaire ou racine que vous avez ajouté à votre application IoT Central. Utilisez des ID d’appareils en minuscules comme `CNAME` dans les certificats feuilles.

1. L’OEM dote chaque périphérique d’un ID d’appareil, d’un certificat X.509 leaf généré et de la valeur d'**étendue de l’ID** de l’application.

1. Lorsque vous basculez sur un appareil, il se connecte d’abord à DPS pour récupérer ses informations d’inscription à IoT Central.

    Initialement, l’appareil a un état **Non associé** sur la page **Appareils** et n’est pas affecté à un modèle d’appareil. Sur la page **Appareils**, **Migrez** l’appareil vers le modèle d’appareil approprié. L’approvisionnement de l’appareil est maintenant terminé, l’état de l’appareil est désormais **Approvisionné**, et l’appareil peut commencer à envoyer des données.

    Sur la page **Administration > Connexion de l’appareil**, l’option **Approbation automatique** détermine si vous devez approuver manuellement l’appareil avant qu’il puisse commencer à envoyer des données.

    > [!NOTE]
    > Pour savoir comment associer automatiquement un appareil à un modèle d’appareil, consultez [Associer automatiquement un appareil à un modèle d’appareil](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Connectivité de l’appareil pour chaque inscription

Pour les clients qui connectent des appareils ayant chacun leurs propres informations d’authentification, utilisez les inscriptions individuelles. Il s’agit d’une entrée pour un seul appareil autorisé à se connecter. Les inscriptions individuelles peuvent utiliser des certificats feuilles X.509 ou des jetons SAP (à partir d’un module de plateforme sécurisée physique ou virtuel) comme mécanismes d’attestation. L’ID d’appareil (également appelé « ID d’inscription ») d’une inscription est au format alphanumérique. Il est constitué de lettres minuscules et peut contenir des traits d’union. Pour plus d’informations, consultez [Inscription DPS individuelle](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Lorsque vous créez une inscription individuelle pour un appareil, elle prend la priorité sur les options d’inscription de groupe par défaut de votre application IoT Central.

### <a name="create-individual-enrollments"></a>Créer des inscriptions individuelles

IoT Central prend en charge les mécanismes d’attestation suivants pour les inscriptions individuelles :

- **Attestation de clé symétrique :** L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance DPS. Pour créer une inscription individuelle qui utilise des clés symétriques, ouvrez la page **Connexion de l’appareil**, sélectionnez **Inscription individuelle** comme méthode de connexion, puis **Signature d’accès partagé (SAP)** comme mécanisme. Entrez les clés primaires et secondaires codées base64 et enregistrez vos modifications. Utilisez l'**étendue de l’ID**, l’**ID de l’appareil** et la clé primaire ou secondaire pour connecter votre appareil.

    > [!TIP]
    > À des fins de test, vous pouvez utiliser **OpenSSL** pour générer des clés codées base64 : `openssl rand -base64 64`

- **Certificats X.509 :** Pour créer une inscription individuelle avec des certificats X.509, ouvrez la page **Connexion de l’appareil**, sélectionnez **Inscription individuelle** comme méthode de connexion et **Certificats (X.509)** comme mécanisme. Les certificats d’appareils utilisés avec une entrée d’inscription individuelle requièrent que le nom commun de l’émetteur et du sujet soit l’ID de l’appareil.

    > [!TIP]
    > À des fins de test, vous pouvez utiliser les [outils pour le kit de développement logiciel (SDK) d’appareil Azure IoT Device Provisioning pour Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) pour générer un certificat auto-signé : `node create_test_cert.js device "mytestdevice"`

- **Attestation de module de plateforme sécurisée (TPM) :** Un [module de plateforme sécurisée](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) est un type de module de sécurité matériel. L’utilisation d’un module de plateforme sécurisée (TPM) est l’un des moyens les plus sécurisés pour connecter un appareil. Cet article suppose que vous utilisez un microprogramme discret, ou un module de plateforme sécurisée (TPM) intégré. Les TPM logiciels émulés sont parfaitement adaptés pour le prototypage ou le test, mais ils n’offrent pas le même niveau de sécurité que les modules de plateforme sécurisée (TPM) intégrés ou les microprogrammes discrets. N’utilisez pas de module de plateforme sécurisée (TPM) logiciel en production. Pour créer une inscription individuelle utilisant un module de plateforme sécurisée (TPM), ouvrez la page **Connexion de l’appareil**, sélectionnez **Inscription individuelle** comme méthode de connexion et **Module de plateforme sécurisée (TPM)** comme mécanisme. Entrez la paire de clés de type EK (Endorsement Key) et enregistrez les informations de connexion de l’appareil.

## <a name="automatically-associate-with-a-device-template"></a>Associer automatiquement un modèle d’appareil

L'une des principales fonctionnalités d’IoT Central est la possibilité d'associer automatiquement des modèles d'appareil lors de la connexion de l'appareil. Avec les informations d'identification de l’appareil, les appareils peuvent envoyer **CapabilityModelId** dans le cadre de l'appel d'inscription de l’appareil. **CapabilityModelID** est un URN qui identifie le modèle de capacité implémenté par l’appareil. L’application IoT Central peut utiliser **CapabilityModelID** pour identifier le modèle d’appareil à utiliser, puis associer automatiquement l’appareil au modèle d’appareil. Le processus de détection fonctionne comme suit :

1. Si le modèle d’appareil est déjà publié dans l’application IoT Central, l’appareil est associé au modèle d’appareil.
1. Pour les appareils IoT Plug-and-Play pré-certifiés, si le modèle d’appareil n’est pas encore publié dans l’application IoT Central, le modèle d’appareil est extrait du référentiel public.

Les extraits de code suivants présentent le format de la charge utile supplémentaire que l’appareil doit envoyer pendant l’appel d’inscription DPS pour que l’association automatique fonctionne.

Il s’agit du format pour les appareils qui utilisent le kit de développement logiciel (SDK) d’appareil généralement disponible qui ne prend pas en charge IoT Plug-and-Play :

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Il s’agit du format pour les appareils qui utilisent le kit de développement logiciel (SDK) d’appareil en préversion publique qui prend en charge IoT Plug-and-Play :

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> L'option **Approbation automatique** sous **Administration > Connexion de l’appareil** doit être activée pour que les appareils se connectent automatiquement, détectent le modèle d’appareil et commencent à envoyer des données.

## <a name="device-status-values"></a>Valeurs d’état de l’appareil

Quand un appareil réel se connecte à votre application IoT Central, son état change comme suit :

1. L’état de l’appareil est d’abord **Inscrit**. Cet état signifie que l’appareil est créé dans IoT Central et a une identité d'appareil. Un appareil est inscrit quand :
    - un nouvel appareil réel est ajouté à la page **Appareils** ;
    - un ensemble d’appareils est ajouté avec **Importer** à la page **Appareils** ;

1. L’état passe à **Approvisionné** lorsque l’appareil connecté à votre application IoT Central avec des informations d’identification valides termine l’étape de provisionnement. À cette étape, l’appareil utilise DPS pour récupérer automatiquement une chaîne de connexion à partir du hub IoT utilisé par votre application IoT Central. L’appareil peut maintenant se connecter à IoT Central et commencer à envoyer des données.

1. Un opérateur peut bloquer un appareil. Quand un appareil est bloqué, il ne peut pas envoyer de données à votre application IoT Central. Les appareils bloqués affichent un état **Bloqué**. Un opérateur doit réinitialiser l’appareil avant que ce dernier puisse recommencer à envoyer des données. Quand un opérateur débloque un appareil, l’état revient à sa valeur précédente, à savoir **Inscrit** ou **Approvisionné**.

1. Si l’état de l’appareil est **En attente d’approbation**, cela signifie que l’option **Approbation automatique** est désactivée. Un opérateur doit approuver explicitement un appareil avant qu’il ne commence à envoyer des données. Les appareils non enregistrés manuellement sur la page **Appareils**, mais connectés avec des informations d'identification valides afficheront l’état **En attente d'approbation**. Les opérateurs peuvent approuver ces appareils sur la page **Appareils** en utilisant le bouton **Approver**.

1. Si l’état de l’appareil est **Non associé**, cela signifie que les appareils se connectant à IoT Central n’ont pas de modèle d’appareil associé. C’est généralement le cas dans les scénarios suivants :

    - Un ensemble d’appareils est ajouté en utilisant **Importer** sur la page **Appareils**, sans spécifier le modèle d’appareil.
    - Un appareil a été inscrit manuellement sur la page **Appareils** sans spécifier le modèle d’appareil. L’appareil est alors connecté avec des informations d’identification valides.  

    L'opérateur peut associer un appareil à un modèle d’appareil sur la page **Appareils** en utilisant le bouton **Migrer**.

## <a name="best-practices"></a>Meilleures pratiques

Ne conservez pas ou ne mettez pas en cache la chaîne de connexion de l’appareil que DPS renvoie quand vous connectez l’appareil pour la première fois. Pour reconnecter un appareil, passez en revue le flux standard d’inscription de l’appareil pour obtenir la chaîne de connexion de l’appareil correcte. Si le périphérique met en cache la chaîne de connexion, le logiciel de l’appareil risque d’avoir une chaîne de connexion obsolète si IoT Central met à jour le hub IoT Azure sous-jacent qu’il utilise.

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)

Le kit de développement logiciel (SDK) Azure Device est le moyen le plus simple pour vous d’implémenter votre code d’appareil. Les kits SDK d’appareils suivants sont disponibles :

- [SDK Azure IoT pour C](https://github.com/azure/azure-iot-sdk-c)
- [SDK Azure IoT pour Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK Azure IoT pour Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK Azure IoT pour Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK Azure IoT pour .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Fonctionnalités du SDK et connectivité d’IoT Hub

Toutes les communications des appareils avec IoT Hub utilisent les options de connectivité IoT Hub suivantes :

- [Messages d’appareil-à-cloud](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Représentations d’appareil physique](../../iot-hub/iot-hub-devguide-device-twins.md)

Le tableau suivant récapitule à quelles fonctionnalités des appareils Azure IoT Central correspondent les fonctionnalités IoT Hub :

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Télémétrie | Messages d’appareil-à-cloud |
| Propriété | Propriétés signalées du jumeau d’appareil |
| Propriété (accessible en écriture) | Propriétés souhaitées et signalées du jumeau d’appareil |
| Commande | Méthodes directes |

Pour en savoir plus sur l'utilisation des Kits de développement logiciel (SDK) d’appareil, voir [Connecter un appareil du kit DevDiv à votre application Azure IoT Central](howto-connect-devkit.md) pour obtenir un exemple de code.

### <a name="protocols"></a>Protocoles

Les kits SDK d’appareil prennent en charge les protocoles réseau suivants pour la connexion à un hub IoT :

- MQTT
- AMQP
- HTTPS

Pour plus d’informations sur les différences entre ces protocoles et des conseils sur le choix de l’un d’eux, consultez [Choisir un protocole de communication](../../iot-hub/iot-hub-devguide-protocols.md).

Si votre appareil ne peut utiliser aucun des protocoles pris en charge, vous pouvez utiliser Azure IoT Edge pour faire une conversion de protocole. IoT Edge prend en charge d’autres scénarios avancés permettant de décharger l’application Azure IoT Central de certains traitements.

## <a name="security"></a>Sécurité

Toutes les données échangées entre les appareils et votre application Azure IoT Central sont chiffrées. IoT Hub authentifie chaque demande provenant d’un appareil qui se connecte aux points de terminaison IoT Hub exposés aux appareils. Pour éviter d’échanger des informations d’identification sur le réseau, un appareil utilise des jetons signés pour s’authentifier. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes un développeur d’appareils, nous vous suggérons les étapes suivantes :

- Découvrez comment [superviser la connectivité des appareils à l’aide d’Azure CLI](./howto-monitor-devices-azure-cli.md)
- Découvrez comment [définir un nouveau type d’appareil IoT dans votre application Azure IoT Central](./howto-set-up-template.md)
- En savoir plus sur [les appareils Azure IoT Edge et Azure IoT Central](./concepts-iot-edge.md)
