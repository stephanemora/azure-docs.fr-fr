---
title: Comment transférer des données supplémentaires entre un appareil et le service Azure Device Provisioning
description: Ce document explique comment transférer des données supplémentaires entre l’appareil et le Service Device Provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974851"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Comment transférer des données supplémentaires entre l’appareil et le service DPS
Parfois, DPS a besoin de recevoir plus de données des appareils afin de les provisionner correctement dans l’IoT Hub approprié, et ces données doivent être fournies par l’appareil. Inversement, DPS peut renvoyer des données à l’appareil afin de faciliter les logiques côté client. 

## <a name="when-to-use-it"></a>Quand utiliser cette fonctionnalité ?
Utilisez cette fonctionnalité pour améliorer [l’allocation personnalisée](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies), par exemple lorsque vous souhaitez allouer vos appareils en fonction du modèle d’appareil sans intervention humaine. Dans ce cas, utilisez [l’allocation personnalisée](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Vous pouvez configurer l’appareil pour qu’il transmette les informations sur le modèle dans le cadre de [l’appel d’enregistrement de l’appareil](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS transmet les informations sur l’appareil au webhook d’allocation personnalisé. Votre fonction peut alors décider vers quel IoT Hub cet appareil sera dirigé lorsqu’il recevra des informations sur le modèle de l’appareil. De même, si le webhook souhaite renvoyer certaines données à l’appareil, il les renvoie sous forme de chaîne dans la réponse webhook.  

## <a name="device-sends-data-to-dps"></a>L’appareil envoie des données à DPS
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
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS renvoie des données à l’appareil
De même, si le webhook de la stratégie d’allocation personnalisée souhaite renvoyer certaines données à l’appareil, il les renvoie sous forme de chaîne dans la réponse webhook. La modification se trouve dans la section returnData ci-dessous. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)
Cette fonctionnalité est aussi disponible dans les [Kits de développement logiciel (SDK) clients](https://docs.microsoft.com/azure/iot-dps/) C, C#, JAVA et Node.js.  

## <a name="next-steps"></a>Étapes suivantes
* Développer à l’aide du [SDK Azure IoT]( https://github.com/Azure/azure-iot-sdks) pour Azure IoT Hub et le service Azure IoT Hub Device Provisioning
