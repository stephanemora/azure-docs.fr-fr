---
title: Traduire des protocoles modbus avec des passerelles - Azure IoT Edge | Microsoft Docs
description: Autoriser les appareils utilisant Modbus TCP à communiquer avec Azure IoT Hub en créant un appareil de passerelle IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 325b69eb7b9b069db0ba49b4578541ee801c3444
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476180"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connecter des appareils Modbus TCP via une passerelle d’appareils IoT Edge

Si vous souhaitez connecter des appareils IoT utilisant les protocoles Modbus TCP ou RTU à un hub IoT Azure, utilisez un appareil IoT Edge comme passerelle. Le périphérique de passerelle lit les données à partir de vos appareils Modbus, puis les communique dans le cloud en utilisant un protocole pris en charge.

![Les appareils Modbus se connectent à IoT Hub via une passerelle IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Cet article vous explique comment créer votre propre image conteneur pour un module Modbus (vous pouvez également utiliser un exemple prédéfini), avant de la déployer sur l’appareil IoT Edge qui sera votre passerelle.

Cet article suppose que vous utilisez le protocole Modbus TCP. Pour plus d’informations sur la façon de configurer le module pour prendre en charge le protocole Modbus RTU, consultez le projet [Module Modbus Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) sur GitHub.

## <a name="prerequisites"></a>Prérequis
* Un appareil Azure IoT Edge. Pour une procédure détaillée montrant comment en configurer un, consultez [Déployer Azure IoT Edge sur Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* Chaîne de connexion de clé primaire de l’appareil IoT Edge.
* Un appareil Modbus physique ou simulé prenant en charge Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Préparer un conteneur Modbus

Si vous souhaitez tester la fonctionnalité de passerelle Modbus, Microsoft propose un module d’exemple que vous pouvez utiliser. Vous pouvez accéder au module à partir de la Place de marché Azure, de [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview) ou avec l’URI d’image **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Si vous souhaitez créer votre propre module et le personnaliser en fonction de votre environnement, un projet open source [Module Modbus Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) existe sur GitHub. Suivez les instructions du projet pour créer votre propre image conteneur. Pour créer une image de conteneur, reportez-vous aux rubriques [Développer des modules C# dans Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou [Développer des modules dans Visual Studio Code](how-to-vs-code-develop-module.md). Ces articles fournissent des instructions sur la création de modules et sur la publication des images conteneur sur un registre.

## <a name="try-the-solution"></a>Essayer la solution

Cette section est consacrée au déploiement de l’exemple de module Modbus de Microsoft sur votre appareil IoT Edge.

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
                  "StartAddress":"40001",
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

7. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Cet itinéraire envoie tous les messages collectés par le module Modbus vers IoT Hub. Dans cette route, **modbusOutput** correspond au point de terminaison utilisé par le module Modbus pour transmettre des données, et **$upstream** est une destination spéciale qui indique au hub IoT Edge d’envoyer des messages à IoT Hub.

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

- Pour plus d’informations sur la façon dont les appareils IoT Edge peuvent agir en tant que passerelles, consultez [Créer un appareil IoT Edge agissant comme une passerelle transparente](./how-to-create-transparent-gateway.md).
- Pour plus d’informations sur le fonctionnement des modules IoT Edge, consultez [Comprendre les modules Azure IoT Edge](iot-edge-modules.md).
