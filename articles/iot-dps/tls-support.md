---
title: Prise en charge de TLS dans le service Azure IoT Device Provisioning (DPS)
description: Meilleures pratiques en matière d’utilisation de connexions TLS sécurisées pour les appareils et les services communiquant avec le service IoT Device Provisioning (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984256"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Prise en charge de TLS dans le service Azure IoT Hub Device Provisioning (DPS)

DPS utilise le protocole TLS (Transport Layer Security) pour sécuriser les connexions provenant d’appareils IoT. Trois versions du protocole TLS sont actuellement prises en charge, à savoir les versions 1.0, 1.1 et 1.2.

Les protocoles TLS 1.0 et 1.1 sont considérés comme hérités et leur obsolescence est planifiée. Pour plus d’informations, consultez [Dépréciation de TLS 1.0 et 1.1 pour IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). Il vous est fortement recommandé d’utiliser TLS 1.2 comme version TLS par défaut lors de la connexion à DPS.

## <a name="restrict-connections-to-tls-12"></a>Restreindre les connexions à TLS 1.2

Pour renforcer la sécurité, il est recommandé de configurer vos instances DPS pour autoriser *uniquement* les connexions d’appareils clients qui utilisent la version 1.2 du protocole TLS et pour appliquer l’utilisation des [chiffrements recommandés](#recommended-ciphers).

Pour ce faire, approvisionnez une nouvelle ressource DPS dans l’une des [régions prises en charge](#supported-regions) et définissez la propriété `minTlsVersion` sur `1.2` dans la spécification de ressource DPS de votre modèle Azure Resource Manager. L’exemple de modèle JSON suivant spécifie la propriété `minTlsVersion` pour une nouvelle instance DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Vous pouvez déployer ce modèle en exécutant les commandes Azure CLI suivantes : 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Pour plus d’informations sur la création de ressources DPS avec des modèles Resource Manager, consultez l’article [Configurer DPS avec un modèle Azure Resource Manager](quick-setup-auto-provision-rm.md).

La ressource DPS créée à l’aide de cette configuration refusera les appareils qui tentent de se connecter à l’aide des versions 1.0 et 1.1 du protocole TLS. De même, la négociation TLS est refusée si le message HELLO de l’appareil client ne répertorie pas les [chiffrements recommandés](#recommended-ciphers).

> [!NOTE]
> La propriété `minTlsVersion` est en lecture seule et ne peut pas être modifiée une fois votre ressource DPS créée. C’est pourquoi il est essentiel de tester et de valider correctement que *tous* vos appareils IoT sont compatibles avec le protocole TLS 1.2 et les [chiffrements recommandés](#recommended-ciphers) au préalable.

## <a name="supported-regions"></a>Régions prises en charge

Les instances DPS qui requièrent l’utilisation de TLS 1.2 peuvent être créés dans les régions suivantes :

* Gouvernement des États-Unis – Arizona
* Gouvernement américain - Virginie

> [!NOTE]
> Lors des basculements, la propriété `minTlsVersion` de votre DPS reste effective dans la région jumelée géographiquement après le basculement.

## <a name="recommended-ciphers"></a>Chiffrements recommandés

Les instances DPS configurées pour accepter uniquement TLS 1.2 appliquent également l’utilisation des chiffrements recommandés suivants :

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Utiliser TLS 1.2 dans les kits de développement logiciel (SDK) IoT

Utilisez les liens ci-dessous pour configurer TLS 1.2 et les chiffrements autorisés dans les Kits de développement logiciel (SDK) clients Azure IoT.

| Langage | Versions prenant en charge TLS 1.2 | Documentation |
|----------|------------------------------------|---------------|
| C        | Balise 2019-12-11 ou ultérieure            | [Lien](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 ou ultérieure             | [Lien](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 ou ultérieure            | [Lien](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 ou ultérieure            | [Lien](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 ou ultérieure            | [Lien](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>Utiliser TLS 1.2 avec IoT Edge

Les appareils IoT Edge peuvent être configurés pour utiliser TLS 1.2 lors des communications avec IoT Hub et DPS. À cet effet, consultez la [page de documentation d’IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).