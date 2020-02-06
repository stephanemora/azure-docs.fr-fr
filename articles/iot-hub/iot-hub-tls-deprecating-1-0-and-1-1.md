---
title: Dépréciation de TLS 1.0 et 1.1 dans IoT Hub et le service de provisionnement des appareils (Microsoft System Center Data Protection Manager) | Microsoft Docs
description: Instructions relatives à la dépréciation de TLS 1.0 et 1.1 et aux chiffrements pris en charge dans IoT Hub et DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722657"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Dépréciation de TLS 1.0 et 1.1 dans IoT Hub et le service de provisionnement des appareils

Pour offrir un chiffrement optimal, IoT Hub et le Service Device Provisioning (DPS) passent au mécanisme de chiffrement privilégié TLS (Transport Layer Security) 1.2 pour les appareils et services IoT. Par conséquent, la prise en charge héritée de TLS 1.0 et TLS 1.1, ainsi que plusieurs chiffrements hérités non recommandés, sera dépréciée le **1er juillet 2020**.


## <a name="impact"></a>Impact
En fonction des circonstances et des configurations spécifiques des clients, la dépréciation de TLS 1.0 et 1.1 et des chiffrements hérités non recommandés peut représenter un changement important pour les appareils et services IoT communiquant avec IoT Hub ou DPS. Dans certains cas, les appareils et les services incompatibles avec ces modifications ne pourront pas se connecter au hub IoT ou à DPS après la date limite susmentionnée.


## <a name="supported-ciphers"></a>Chiffrements pris en charge

Seuls les chiffrements suivants sont autorisés pendant l’établissement d’une liaison TLS :

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Feedback des clients

Même si la mise en œuvre de TLS 1.2 est un choix de chiffrement optimal à l’échelle industrielle et sera effectuée comme prévu, nous aimerions entendre l’avis des clients concernant leurs déploiements spécifiques et les difficultés d’adoption de TLS 1.2. Vous pouvez pour cela envoyer vos commentaires à l’adresse [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).