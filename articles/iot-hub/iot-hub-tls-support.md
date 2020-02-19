---
title: Prise en charge du protocole TLS dans Azure IoT Hub
description: Meilleures pratiques en matière d’utilisation de connexions TLS sécurisées pour les appareils et les services communiquant avec IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 244a71d400493a2029e831b729c63bc0b0dfe559
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049651"
---
# <a name="tls-support-in-iot-hub"></a>Prise en charge du protocole TLS dans IoT Hub

IoT Hub utilise le protocole TLS (Transport Layer Security) pour sécuriser les connexions provenant d’appareils et de services IoT. Trois versions du protocole TLS sont actuellement prises en charge, à savoir les versions 1.0, 1.1 et 1.2.

Les protocoles TLS 1.0 et 1.1 sont considérés comme hérités et leur obsolescence est planifiée. Pour plus d’informations, consultez [Dépréciation de TLS 1.0 et 1.1 pour IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Il vous est fortement recommandé d’utiliser TLS 1.2 comme version TLS par défaut lors de la connexion à IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Restreindre les connexions à TLS 1.2 dans votre ressource IoT Hub

Pour renforcer la sécurité, il est recommandé de configurer vos hubs IoT pour autoriser *uniquement* les connexions client qui utilisent la version 1.2 du protocole TLS et pour appliquer l’utilisation des [chiffrements recommandés](#recommended-ciphers).

À cet effet, approvisionnez un nouveau hub IoT dans l’une des [régions prises en charge](#supported-regions) et définissez la propriété `minTlsVersion` sur `1.2` dans la spécification de ressource IoT Hub de votre modèle Azure Resource Manager :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

La ressource IoT Hub créée à l’aide de cette configuration refusera les clients d’appareil et de service qui tentent de se connecter à l’aide des versions 1.0 et 1.1 du protocole TLS. De même, la négociation TLS est refusée si le message HELLO client ne répertorie pas les [chiffrements recommandés](#recommended-ciphers).

> [!NOTE]
> La propriété `minTlsVersion` est en lecture seule et ne peut pas être modifiée une fois votre ressource IoT Hub créée. C’est pourquoi il est essentiel de tester et de valider correctement que *tous* vos appareils et services IoT sont compatibles avec le protocole TLS 1.2 et les [chiffrements recommandés](#recommended-ciphers) au préalable.

### <a name="supported-regions"></a>Régions prises en charge

Les hubs IoT qui requièrent l’utilisation de TLS 1.2 peuvent être créés dans les régions suivantes :

* USA Est
* États-Unis - partie centrale méridionale
* USA Ouest 2

> [!NOTE]
> Lors des basculements, la propriété `minTlsVersion` de votre IoT Hub reste effective dans la région jumelée géographiquement après le basculement.

### <a name="recommended-ciphers"></a>Chiffrements recommandés

Les hubs IoT configurés pour accepter uniquement TLS 1.2 appliquent également l’utilisation des chiffrements recommandés suivants :

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Utiliser TLS 1.2 dans vos Kits de développement logiciel (SDK) IoT Hub

Utilisez les liens ci-dessous pour configurer TLS 1.2 et les chiffrements autorisés dans les Kits de développement logiciel (SDK) clients IoT Hub.

| Langage | Versions prenant en charge TLS 1.2 | Documentation |
|----------|------------------------------------|---------------|
| C        | Balise 2019-12-11 ou ultérieure            | [Lien](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 ou ultérieure             | [Lien](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 ou ultérieure            | [Lien](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 ou ultérieure            | [Lien](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 ou ultérieure            | [Lien](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Utiliser TLS 1.2 dans votre configuration IoT Edge

Les appareils IoT Edge peuvent être configurés pour utiliser TLS 1.2 lors des communications avec IoT Hub. À cet effet, consultez la [page de documentation d’IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).