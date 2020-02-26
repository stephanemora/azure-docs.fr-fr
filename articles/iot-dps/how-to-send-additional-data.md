---
title: Comment transférer une charge utile entre un appareil et le service Azure Device Provisioning
description: Ce document explique comment transférer une charge utile entre un appareil et le service Device Provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3eec39e975b1e782eafe16205623c625f462a865
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209407"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Comment transférer une charge utile entre l’appareil et DPS
Parfois, DPS a besoin de recevoir plus de données des appareils afin de les provisionner correctement dans l’IoT Hub approprié, et ces données doivent être fournies par l’appareil. Inversement, DPS peut renvoyer des données à l’appareil afin de faciliter les logiques côté client. 

## <a name="when-to-use-it"></a>Quand utiliser cette fonctionnalité ?
Utilisez cette fonctionnalité pour améliorer [l’allocation personnalisée](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies), par exemple lorsque vous souhaitez allouer vos appareils en fonction du modèle d’appareil sans intervention humaine. Dans ce cas, utilisez [l’allocation personnalisée](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Vous pouvez configurer l’appareil pour qu’il transmette les informations sur le modèle dans le cadre de [l’appel d’enregistrement de l’appareil](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS transmet la charge utile de l’appareil au webhook d’allocation personnalisé. Votre fonction peut alors décider vers quel IoT Hub cet appareil sera dirigé lorsqu’il recevra des informations sur le modèle de l’appareil. De même, si le webhook souhaite renvoyer certaines données à l’appareil, il les renvoie sous forme de chaîne dans la réponse webhook.  

## <a name="device-sends-data-payload-to-dps"></a>L’appareil envoie la charge utile de données à DPS
Lorsque votre appareil envoie un [appel d’inscription d’appareil](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) à DPS, l’appel d’inscription peut être amélioré pour prendre d’autres champs dans le corps. Le corps ressemble à ce qui suit : 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS renvoie des données à l’appareil
De même, si le webhook de la stratégie d’allocation personnalisée souhaite renvoyer certaines données à l’appareil, il les renvoie sous forme de chaîne dans la réponse webhook. La modification se trouve dans la section Charge utile ci-dessous. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)
Cette fonctionnalité est aussi disponible dans les [Kits de développement logiciel (SDK) clients](https://docs.microsoft.com/azure/iot-dps/) C, C#, JAVA et Node.js.  

## <a name="next-steps"></a>Étapes suivantes
* Développer à l’aide du [SDK Azure IoT]( https://github.com/Azure/azure-iot-sdks) pour Azure IoT Hub et le service Azure IoT Hub Device Provisioning
