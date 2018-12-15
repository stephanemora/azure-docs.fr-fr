---
title: Connecter un appareil Windows IoT Core à votre application Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, apprenez à connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7d593a992d0ff189d23185b3422dee86a55308d1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309448"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Connecter un appareil Windows IoT Core à votre application Azure IoT Central

Cet article vous explique comment, en tant que développeur d’appareils, connecter un appareil Windows IoT Cor à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central créée à partir du modèle d’application **Exemples de Devkits**. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
2. Un appareil exécutant le système d’exploitation Windows 10 IoT Core. Pour cette procédure pas à pas, nous utilisons un appareil Raspberry Pi.


## <a name="sample-devkits-application"></a>**Exemple d’application Devkits**

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Windows IoT Core** avec les caractéristiques suivantes : 

- Données de télémétrie qui contiennent les mesures d’**humidité**, de **température** et de **pression** de l’appareil. 
- Paramètres montrant la **vitesse du ventilateur**.
- Propriétés contenant la propriété d’appareil **Numéro gravé** et la propriété cloud **Emplacement**.


Pour plus d’informations sur la configuration du modèle d’appareil, consultez [Détails de modèle d’appareil Windows IoT Core](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details).

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Windows IoT Core** et prenez note de la chaîne de connexion de l’appareil. Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Préparer l’appareil Windows IoT Core

Pour configurer un appareil Windows IoT Core, suivez le guide pas à pas de la section [Configurer un appareil Windows IoT Core](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel à partir du modèle d’appareil **Windows IoT Core** et notez les informations de connexion de l’appareil (**ID d’étendue, ID d’appareil et Clé primaire**). Pour plus d’informations, consultez [Ajouter un appareil réel à votre application Azure IoT Central](tutorial-add-device.md).

 > [!NOTE]
   > Azure IoT Central utilise désormais le Service IoT Hub Device Provisioning (DPS) Azure pour toutes les connexions d’appareil. Suivez ces instructions pour [obtenir la chaîne de connexion d’appareil](concepts-connectivity.md#getting-device-connection-string) et continuer le didacticiel.

## <a name="prepare-the-windows-10-iot-core-device"></a>Préparer l’appareil Windows 10 IoT Core

### <a name="what-youll-need"></a>Ce dont vous avez besoin

Pour configurer un appareil Windows 10 IoT Core physique, vous devez d’abord disposer d’un appareil exécutant Windows 10 IoT Core. Découvrez [ici](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) comment configurer un appareil Windows 10 IoT Core.

Vous avez également besoin d’une application cliente qui peut communiquer avec Azure IoT Central. Vous pouvez créer votre propre application personnalisée avec le SDK Azure et la déployer sur votre appareil avec Visual Studio, ou bien vous pouvez télécharger un [exemple prégénéré](https://developer.microsoft.com/windows/iot/samples), puis simplement le déployer et l’exécuter sur l’appareil. 

### <a name="deploying-the-sample-client-application"></a>Déploiement de l’exemple d’application cliente

Pour déployer l’application cliente de l’étape précédente sur votre appareil Windows 10 IoT de façon à le préparer :

**Vérifiez que la chaîne de connexion est stockée sur l’appareil que l’application cliente utilisera**
* Sur le poste de travail, enregistrez la chaîne de connexion dans un fichier texte nommé connection.string.iothub.
* Copiez le fichier texte dans le dossier des documents de l’appareil : `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Une fois que vous avez fait cela, vous devez ouvrir le [Portail d’appareil Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) en tapant http://[adresse_IP_appareil]:8080 dans n’importe quel navigateur.

À partir d’ici, et comme montré dans l’illustration ci-dessous, vous devez :
1. Développer le nœud « Applications » sur la gauche.
2. Cliquer sur « Exemples d’exécution rapide ».
3. Cliquer sur « Client Azure IoT Hub ».
4. Cliquer sur « Déployer et exécuter ».

![Illustration montrant le Client Azure IoT Hub sur le Portail d’appareil Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

En cas de réussite, l’application est lancée sur l’appareil et se présente comme ceci :

![Capture d’écran de l’application cliente Azure IoT Hub](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

Dans Azure IoT Central, vous pouvez voir comment le code s’exécutant sur l’appareil Raspberry Pi interagit avec l’application :

* Dans la page **Mesures** de votre appareil réel, vous pouvez voir la télémétrie.
* Dans la page **Propriétés**, vous pouvez voir la valeur de la propriété Numéro gravé.
* Dans la page **Paramètres**, vous pouvez changer différents paramètres de l’appareil Raspberry Pi, comme le voltage et la vitesse du ventilateur.

## <a name="download-the-source-code"></a>Télécharger le code source

Si vous voulez explorer et modifier le code source de l’application cliente, vous pouvez le télécharger depuis GitHub [ici](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Si vous envisagez de modifier le code, suivez ces instructions du fichier readme [ici](https://github.com/Microsoft/Windows-iotcore-samples) pour le système d’exploitation de votre poste de travail.

> [!NOTE]
> Si **git** n’est pas installé dans votre environnement de développement, vous pouvez le télécharger à partir de [https://git-scm.com/download](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Détails de modèle d’appareil Windows IoT Core

Une application créée à partir du modèle d’application **Exemples de Devkits** présente un modèle d’appareil **Windows IoT Core** avec les caractéristiques suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

| Nom du champ     | Units  | Minimale | Maximale | Nombre de décimales |
| -------------- | ------ | ------- | ------- | -------------- |
| humidité       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pression       | hPa    | 260     | 1 260    | 0              |

### <a name="settings"></a>Paramètres

Paramètres numériques

| Nom complet | Nom du champ | Units | Nombre de décimales | Minimale | Maximale | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Vitesse du ventilateur    | fanSpeed   | TR/MIN   | 0              | 0       | 1 000    | 0       |


### <a name="properties"></a>properties

| type            | Nom complet | Nom du champ | Type de données |
| --------------- | ------------ | ---------- | --------- |
| Propriété d’appareil | Numéro gravé   | dieNumber  | number    |
| Texte            | Lieu     | location   | N/A       |
