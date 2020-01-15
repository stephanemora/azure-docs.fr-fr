---
title: Dépréciation de TLS 1.0 et 1.1 dans IoT Hub et le Service Device Provisioning (DPS) | Microsoft Docs
description: Instructions relatives à la dépréciation de TLS 1.0 et 1.1 et aux chiffrements pris en charge dans IoT Hub et DPS.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476807"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>Dépréciation de TLS 1.0 et 1.1 dans IoT Hub et le Service Device Provisioning

Pour offrir un chiffrement optimal, IoT Hub et le Service Device Provisioning (DPS) passent au mécanisme de chiffrement privilégié TLS (Transport Layer Security) 1.2 pour les appareils et services IoT. Par conséquent, la prise en charge héritée de TLS 1.0 et TLS 1.1, ainsi que plusieurs chiffrements hérités non recommandés, sera dépréciée le **1er juillet 2020**.


## <a name="impact"></a>Impact
En fonction des circonstances et des configurations spécifiques des clients, la dépréciation de TLS 1.0 et 1.1 et des chiffrements hérités non recommandés peut représenter un changement important pour les appareils et services IoT communiquant avec IoT Hub ou DPS. Dans certains cas, les appareils et les services incompatibles avec ces modifications ne pourront pas se connecter au hub IoT ou à DPS après la date limite susmentionnée.


## <a name="supported-ciphers"></a>Chiffrements pris en charge

Seuls les chiffrements suivants seront autorisés pendant l’établissement d’une liaison TLS :

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Feedback des clients

Même si la mise en œuvre de TLS 1.2 est un choix de chiffrement optimal à l’échelle industrielle et sera effectuée comme prévu, nous aimerions entendre l’avis des clients concernant leurs déploiements spécifiques et les difficultés d’adoption de TLS 1.2. Vous pouvez pour cela envoyer vos commentaires à l’adresse [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).