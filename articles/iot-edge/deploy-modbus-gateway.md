---
title: Traduire des protocoles modbus avec des passerelles - Azure IoT Edge | Microsoft Docs
description: Autoriser les appareils utilisant Modbus TCP à communiquer avec Azure IoT Hub en créant un appareil de passerelle IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873761"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connecter des appareils Modbus TCP via une passerelle d’appareils IoT Edge

Si vous souhaitez connecter des appareils IoT utilisant les protocoles Modbus TCP ou RTU à un hub IoT Azure, utilisez un appareil IoT Edge comme passerelle. Le périphérique de passerelle lit les données à partir de vos appareils Modbus, puis les communique dans le cloud en utilisant un protocole pris en charge.

![Appareils Modbus se connectent à IoT Hub via la passerelle IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Cet article vous explique comment créer votre propre image conteneur pour un module Modbus (vous pouvez également utiliser un exemple prédéfini), avant de la déployer sur l’appareil IoT Edge qui sera votre passerelle.

Cet article suppose que vous utilisez le protocole Modbus TCP. Pour plus d’informations sur la façon de configurer le module pour prendre en charge le protocole Modbus RTU, consultez le projet [Module Modbus Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) sur GitHub.

## <a name="prerequisites"></a>Conditions préalables
* Un appareil Azure IoT Edge. Pour une procédure pas à pas sur la façon de configurer un, consultez [déployer Azure IoT Edge sur Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* Chaîne de connexion de clé primaire de l’appareil IoT Edge.
* Un appareil Modbus physique ou simulé prenant en charge Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Préparer un conteneur Modbus

Si vous souhaitez tester la fonctionnalité de passerelle Modbus, Microsoft propose un module d’exemple que vous pouvez utiliser. Vous pouvez accéder au module à partir de la place de marché Azure, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), ou avec l’URI, d’image **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Si vous souhaitez créer votre propre module et le personnaliser en fonction de votre environnement, un projet open source [Module Modbus Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) existe sur GitHub. Suivez les instructions du projet pour créer votre propre image conteneur. Si vous créez votre propre image conteneur, reportez-vous à [développer C# modules dans Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou [développer des modules dans Visual Studio Code](how-to-vs-code-develop-module.md). Ces articles fournissent des instructions sur la création de nouveaux modules et de publication des images conteneur à un Registre.

## <a name="try-the-solution"></a>Essayez la solution

Cette section Guide de déploiement exemple Modbus de module de Microsoft sur votre appareil IoT Edge.

1. Accédez à votre IoT Hub sur le [portail Azure](https://portal.azure.com/).

2. Accédez à **IoT Edge** et cliquez sur votre appareil IoT Edge.

3. Sélectionnez **Définir des modules**.

4. Ajouter le module Modbus :

   1. Cliquez sur **Ajouter** et sélectionnez **Module IoT Edge**.

   2. Dans le champ **Nom**, saisissez « modbus».

   3. Dans le champ **Image**, saisissez l’URI d’image de l’exemple de conteneur : `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Cochez la case **Activer** pour mettre à jour les propriétés souhaitées des représentations du module.

   5. Copiez le JSON suivant dans la zone de texte. Remplacez la valeur de **SlaveConnection** par l’adresse IPv4 de votre appareil Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Sélectionnez **Enregistrer**.

5. Revenez à l’étape **Ajouter des modules** et sélectionnez **Suivant**.

7. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Cet itinéraire envoie tous les messages collectés par le module Modbus vers IoT Hub. Dans cet itinéraire, « modbusOutput » correspond du point de terminaison Modbus module permet de générer des données, et « upstream » est une destination spéciale qui indique au hub IoT Edge pour envoyer des messages à IoT Hub.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Sélectionnez **Suivant**.

9. À l’étape **Vérifier le déploiement**, sélectionnez **Envoyer**.

10. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**. Vous devez voir le nouveau module **modbus** en cours d’exécution avec le runtime IoT Edge.

## <a name="view-data"></a>Afficher les données
Afficher les données provenant du module modbus :
```cmd/sh
iotedge logs modbus
```

Vous pouvez afficher les données de télémétrie envoyées par l’appareil à l’aide de [l’extension Azure IoT Hub Toolkit pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anciennement l’extension Azure IoT Toolkit).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la façon dont les appareils IoT Edge peuvent agir comme passerelles, consultez [créer un appareil IoT Edge qui fait Office de passerelle transparente](./how-to-create-transparent-gateway.md).
- Pour plus d’informations sur le fonctionnement des modules IoT Edge, consultez [modules comprendre Azure IoT Edge](iot-edge-modules.md).
