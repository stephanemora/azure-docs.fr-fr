---
title: Limiter l’accès réseau sortant IoT Hub et protection contre la perte de données
description: 'Guide du développeur : comment configurer IoT Hub pour une sortie vers des emplacements approuvés uniquement.'
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: f4f8044237c82212723a54b677d77bc5aecb95a5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867800"
---
# <a name="restrict-outbound-network-access-for-azure-iot-hub"></a>Limiter l’accès réseau sortant pour Azure IoT Hub

IoT Hub prend en charge la sortie de données vers d’autres services via le [routage vers des points de terminaison personnalisés](iot-hub-devguide-messages-d2c.md), le [chargement de fichiers](iot-hub-devguide-file-upload.md) et l'[exportation d’identité d’appareil](iot-hub-bulk-identity-mgmt.md). Pour une sécurité supplémentaire dans un environnement d’entreprise, utilisez l'API `restrictOutboundNetworkAccess` pour limiter la sortie d’un hub IoT vers des destinations approuvées explicitement uniquement. Actuellement, cette fonctionnalité n’est pas disponible dans le portail Azure.

## <a name="enabling-the-restriction"></a>Activation de la restriction

Pour activer la fonctionnalité, utilisez n’importe quelle méthode pour mettre à jour les propriétés de ressource IoT Hub (`PUT`) afin de définir `restrictOutboundNetworkAccess` sur `true` tout en incluant un `allowedFqdnList` qui contient des noms de domaines complets (FQDN) sous forme de tableau. 

Exemple d’illustration de la représentation JSON à utiliser avec la [méthode Create ou Update](/rest/api/iothub/iothubresource/createorupdate) :

```json
{
...
            "properties": {
              ...
                "restrictOutboundNetworkAccess": true,
                "allowedFqdnList": [
                    "my-eventhub.servicebus.windows.net",
                    "iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net"
                ]
              ...
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            }
        }
    }
}
```
Pour effectuer la même mise à jour à l’aide d’Azure CLI, exécutez

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.restrictOutboundNetworkAccess=true properties.allowedFqdnList="['my-eventhub.servicebus.windows.net','iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net']"
```

## <a name="restricting-outbound-network-access-with-existing-routes"></a>Restriction de l’accès réseau sortant avec des itinéraires existants

Une fois que `restrictOutboundNetworkAccess` est défini sur `true`, les tentatives d’émission de données vers des destinations en dehors des FQDN autorisés échouent. Même les itinéraires configurés existants cessent de fonctionner si le point de terminaison personnalisé n’est pas inclus dans la liste des FQDN autorisés.

## <a name="built-in-endpoint"></a>Point de terminaison intégré

Si `restrictOutboundNetworkAccess` est défini sur `true`, le point de terminaison compatible avec l’Event Hub intégré n’est pas exempté de la restriction. En d’autres termes, vous devez inclure le FQDN du point de terminaison intégré dans la liste des FQDN autorisés pour continuer à travailler.

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser une identité managée pour la sortie des données, consultez [Prise en charge d’IoT Hub pour les identités managées](iot-hub-managed-identity.md).
- Pour limiter l’accès réseau entrant, consultez [Gestion de l’accès réseau public pour votre hub IoT](iot-hub-public-network-access.md) et [Prise en charge d’IoT Hub pour les réseaux virtuels avec Private Link](virtual-network-support.md).