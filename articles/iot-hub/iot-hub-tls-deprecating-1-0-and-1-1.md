---
title: Dépréciation de TLS 1.0 et 1.1 dans IoT Hub | Microsoft Docs
description: Instructions relatives à la dépréciation de TLS 1.0 et 1.1 et aux chiffrements pris en charge dans IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849516"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Dépréciation de TLS 1.0 et 1.1 dans IoT Hub

Pour offrir un chiffrement optimal, IoT Hub passe au mécanisme de chiffrement privilégié TLS (Transport Layer Security) 1.2 pour les appareils et services IoT. 

## <a name="timeline"></a>Chronologie

IoT Hub continuera à prendre en charge TLS 1.0/1.1 jusqu’à nouvel ordre. Toutefois, nous recommandons à tous les clients de migrer vers TLS 1.2 dès que possible.

## <a name="deprecating-tls-11-ciphers"></a>Dépréciation des chiffrements TLS 1.1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Dépréciation des chiffrements TLS 1.0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>Chiffrements TLS 1.2

Consultez [Chiffrements recommandés TLS 1.2 IoT Hub](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Feedback des clients

Même si la mise en œuvre de TLS 1.2 est un choix de chiffrement optimal à l’échelle industrielle et sera effectuée comme prévu, nous aimerions entendre l’avis des clients concernant leurs déploiements spécifiques et les difficultés d’adoption de TLS 1.2. Vous pouvez pour cela envoyer vos commentaires à l’adresse [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
